# Architecture

## Overview

The IT Helpdesk Agent uses Copilot Studio, Power Automate, Dataverse, Microsoft Teams, and Azure OpenAI / Azure AI Foundry to support an authenticated V2 helpdesk workflow.

The architecture is built around trusted identity, Dataverse state tracking, idempotent watcher flows, Teams Adaptive Card write-back, and secure BYOM summarisation.

See the public architecture diagram in [architecture/IT-solution-Architecture.svg](../architecture/IT-solution-Architecture.svg).

## High-Level Flow

```text
Signed-in user
  -> IT Support (Portfolio)
  -> Power Automate helper flows
  -> Dataverse IT Ticket / IT Tickets
  -> Teams Adaptive Cards / Technician Console / SLA watchers
  -> SummariseTicket_BYOM
  -> Azure OpenAI / Azure AI Foundry
```

## Copilot Studio Layer

`IT Support (Portfolio)` provides the authenticated V2 agent experience.

Built capabilities:

- Entra ID / organizational sign-in configured and tested.
- Identity-based user context built and tested.
- Structured ticket intake.
- Ticket status checks.
- Urgent escalation.
- No typed email used for authorization.

The agent calls Power Automate helper flows and passes signed-in identity values into the orchestration layer.

## Power Automate Orchestration Layer

Power Automate contains the operational logic behind the agent.

Built flows:

- `Helper - Create Ticket`
- `Helper - Check Status`
- `Helper - Escalate Ticket`
- `Automation - Notify Manager`
- `SummariseTicket_BYOM`
- SLA watcher / SLA escalation watcher

Power Automate is responsible for Dataverse writes, ownership validation, manager notifications, acknowledgement write-back, SLA monitoring, and AI summary generation.

## Dataverse System Of Record

Dataverse `IT Ticket` / `IT Tickets` is the system of record.

It stores:

- Ticket state and user-facing ticket details.
- Requestor identity.
- Escalation state and audit fields.
- Teams notification and acknowledgement fields.
- SLA and reminder fields.
- AI-generated summary fields.
- Dataverse `Status` and `Status Reason`.

Ticket references use `TKT####`, for example `TKT1001`.

## Authentication And Authorization

The authenticated experience uses Entra ID / organizational sign-in.

Ownership is enforced by comparing:

```text
RequestorAadObjectId == CurrentUserAadObjectId
```

This model prevents users from relying on typed email addresses for authorization. Status checks and escalation updates only proceed when the signed-in user owns the ticket.

## Teams Adaptive Card Write-Back

`Automation - Notify Manager` sends Teams Adaptive Cards for escalation visibility and manager acknowledgement.

The acknowledgement action writes back to Dataverse fields such as:

- `AcknowledgedOn`
- `AcknowledgedByAadObjectId`
- `AcknowledgedByDisplayName`
- `AcknowledgedByEmail`

Notification state is tracked with fields such as `NotificationSent`, `NotificationSentOn`, `ManagerNotified`, `ManagerNotifiedOn`, and `CardSentOn`.

## BYOM Azure OpenAI Summarisation

`SummariseTicket_BYOM` is a child flow that creates ticket summaries using Azure OpenAI / Azure AI Foundry.

Azure OpenAI / Azure AI Foundry provides the BYOM model deployment and API key used by the summarisation flow. The API key value is never documented or committed.

The child flow uses:

- A stable child-flow input contract.
- Environment-variable driven configuration.
- Secure API key handling.
- HTTP call to Azure OpenAI.
- Expression-based AI response parsing.
- TRY/CATCH fallback summary behavior.
- Dataverse write-back to summary fields.

Azure OpenAI configuration is externalised through environment variables, including endpoint, deployment name, API version, and summarisation prompt.

The Azure OpenAI API key is handled through secure secret configuration using Azure Key Vault / Power Platform secret environment variables. The key is retrieved at runtime and is not hardcoded in Power Automate flows, documentation, screenshots, or source files.

## SLA Watcher And Director Escalation

The SLA watcher / SLA escalation watcher monitors ticket state and supports reminder and escalation patterns.

Relevant Dataverse fields include:

- `ReminderCount`
- `LastReminderOn`
- `EscalatedToDirector`
- `DirectorEscalatedOn`

The watcher pattern is designed to be idempotent and auditable.

## Field-Level Security And RBAC

The solution uses RBAC / least-privilege thinking so employees, technicians, and escalation owners have appropriate access.

Field-Level Security is used for decision, acknowledgement, and escalation fields so sensitive operational fields are not broadly editable.

## ALM And Governance

The solution uses:

- `ITHelpdeskPortfolio` as the solution name.
- `lai` as the publisher prefix.
- Environment variables for configuration.
- Power Platform secret environment variables / Azure Key Vault for secure secret handling.
- Connection references for deployable flows.
- Unmanaged solutions for build.
- Managed solutions for deployment.
- Dataverse state tracking for auditability.
- No secrets or private tenant-specific details in the repo.

## Public Safety

Do not publish actual secret values, endpoint values, tenant URLs, subscription IDs, directory IDs, vault URIs, personal account details, private screenshots, or real ticket content.
