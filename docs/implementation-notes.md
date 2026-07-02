# Implementation Notes

## Overview

These notes summarize the public implementation design for the V2 IT Helpdesk Agent portfolio build. The focus is authenticated Copilot Studio interaction, Dataverse-backed ticket ownership, Power Automate orchestration, Teams Adaptive Card write-back, BYOM Azure OpenAI summarisation, SLA watcher automation, and governance.

## Key Design Decisions

| Area | Decision |
| --- | --- |
| Authentication | Entra ID / organizational sign-in is configured and tested |
| Authorization | Ticket ownership is enforced through AAD Object ID comparison |
| Ticket reference | Public ticket format is `TKT####`, for example `TKT1001` |
| System of record | Dataverse `IT Ticket` / `IT Tickets` stores ticket state and audit fields |
| Orchestration | Power Automate handles create, status, escalation, notification, SLA, and summary workflows |
| Teams write-back | Adaptive Card acknowledgement updates Dataverse |
| BYOM summarisation | `SummariseTicket_BYOM` calls Azure OpenAI / Azure AI Foundry |
| Secure configuration | Environment variables and secure secret configuration keep secrets out of flows and repo files |
| ALM | Build unmanaged, deploy managed, use connection references and environment variables |

## Identity And Ownership Model

The V2 agent does not use typed email for authorization.

When a ticket is created, Dataverse stores requestor identity fields:

- `RequestorAadObjectId`
- `RequestorUPN`
- `RequestorDisplayName`

For protected actions, Power Automate compares:

```text
RequestorAadObjectId == CurrentUserAadObjectId
```

If the signed-in user does not own the ticket, the flow returns `NotAuthorized` instead of returning or updating protected ticket data.

## Ticket Reference Standard

The public ticket reference standard is:

```text
TKT####
```

Example:

```text
TKT1001
```

Legacy variants such as `TKT-####` and `TKT--####` are excluded from the V2 public documentation.

## BYOM Summarisation Design

`SummariseTicket_BYOM` is implemented as a child flow with a stable input contract. It accepts ticket context, calls Azure OpenAI / Azure AI Foundry, parses the response, and writes summary output back to Dataverse.

Implementation details:

- Environment-variable prompt abstraction through `lai_ITSD_SummarisationPrompt`.
- Environment-variable driven endpoint, deployment, and API version.
- Secure API key retrieval at runtime.
- HTTP call to Azure OpenAI.
- Expression-based AI response parsing.
- TRY/CATCH fallback summary if the model call fails.
- Dataverse write-back to `TicketSummary`, `SummaryGeneratedOn`, `SummarySource`, and `SummaryError`.

Azure OpenAI configuration is externalised through environment variables, including endpoint, deployment name, API version, and summarisation prompt.

The Azure OpenAI API key is handled through secure secret configuration using Azure Key Vault / Power Platform secret environment variables. The key is retrieved at runtime and is not hardcoded in Power Automate flows, documentation, screenshots, or source files.

## Environment Variables

Variable names may be documented publicly; values must not be published.

| Variable | Purpose |
| --- | --- |
| `lai_AOAI_ApiKey` | Secure secret configuration for the Azure OpenAI API key |
| `lai_AOAI_ApiVersion` | Azure OpenAI API version |
| `lai_AOAI_DeploymentName` | Azure OpenAI deployment name |
| `lai_AOAI_Endpoint` | Azure OpenAI endpoint |
| `lai_ITSD_SummarisationPrompt` | Prompt text used by the summarisation child flow |

## Escalation And Notification Design

`Helper - Escalate Ticket` validates the request, checks ownership, checks escalation state, updates Dataverse, and returns a clear outcome.

Expected outcomes:

- `InvalidFormat`
- `NotFound`
- `AlreadyEscalated`
- `NotAuthorized`
- `Success`

`Automation - Notify Manager` uses Dataverse state flags such as `NotificationSent` and escalation fields to support idempotent notification behavior. Teams Adaptive Card acknowledgement writes manager response data back to Dataverse.

## SLA Watcher Design

The SLA watcher / SLA escalation watcher monitors ticket state and tracks reminder and director escalation data.

Relevant fields:

- `ReminderCount`
- `LastReminderOn`
- `EscalatedToDirector`
- `DirectorEscalatedOn`

The watcher pattern is designed for repeat-safe automation and audit-ready Dataverse state tracking.

## Dataverse Implementation Notes

- Dataverse is the system of record.
- `IT Ticket` / `IT Tickets` stores ticket state, identity, escalation, summary, acknowledgement, notification, SLA, and audit fields.
- Dataverse Update row actions must use the Dataverse primary key GUID, not the user-facing `TicketID`.
- `Status` and `Status Reason` remain Dataverse status fields.
- Summary fields support BYOM write-back.
- Acknowledgement and escalation fields support Teams and technician handoff.

## Power Automate Implementation Notes

| Flow | Purpose |
| --- | --- |
| `Helper - Create Ticket` | Creates ticket rows and stores requestor identity |
| `Helper - Check Status` | Returns ticket details after ownership validation |
| `Helper - Escalate Ticket` | Validates and escalates urgent tickets |
| `Automation - Notify Manager` | Sends Teams Adaptive Cards and tracks notification state |
| `SummariseTicket_BYOM` | Generates and writes BYOM summaries |
| SLA watcher / SLA escalation watcher | Tracks reminder and escalation conditions |

The run-only connection binding issue was addressed by using the maker-owned connection, so Copilot-triggered flows can run consistently.

## Governance And ALM Notes

- RBAC / least privilege.
- Field-Level Security on decision, acknowledgement, and escalation fields.
- Environment variables for deployable configuration.
- Connection references for flow portability.
- Unmanaged solution for build.
- Managed solution for deployment.
- No secrets in the repo.
- No raw exported solution files unless intentionally sanitized and public-ready.
