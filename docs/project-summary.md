# Project Summary

## Purpose

IT Helpdesk Agent is a V2-only Power Platform / Copilot Studio portfolio project. It demonstrates an authenticated IT support agent that creates tickets, checks ticket status, escalates urgent issues, sends Teams Adaptive Card notifications, writes acknowledgement actions back to Dataverse, and generates BYOM Azure OpenAI ticket summaries.

The project is a portfolio/demo project demonstrating production-aligned patterns, not a live production ITSM system.

## Core Identifiers

| Item | Value |
| --- | --- |
| Repo / project name | IT Helpdesk Agent |
| Agent name | `IT Support (Portfolio)` |
| Solution name | `ITHelpdeskPortfolio` |
| Publisher prefix | `lai` |
| Dataverse table | `IT Ticket` / `IT Tickets` |
| Ticket format | `TKT####`, for example `TKT1001` |

Legacy ticket formats such as `TKT-####` and `TKT--####` are excluded from this public V2 documentation except where explicitly marked as legacy.

## Target Users

| Persona | Need |
| --- | --- |
| Employee / requester | Create tickets, check their own ticket status, and escalate urgent issues |
| IT technician | Review, triage, acknowledge, and update support tickets |
| Manager / escalation owner | Receive escalation notifications and acknowledge escalated work |
| Portfolio reviewer | Assess Power Platform, Copilot Studio, Dataverse, Power Automate, AI, and governance capability |

## Built V2 User Journeys

1. User signs in through Entra ID / organizational sign-in.
2. User logs a ticket through `IT Support (Portfolio)`.
3. `Helper - Create Ticket` creates a Dataverse `IT Ticket` row.
4. User receives a `TKT####` ticket reference.
5. User checks ticket status.
6. `Helper - Check Status` compares `RequestorAadObjectId` with `CurrentUserAadObjectId`.
7. User escalates an urgent ticket.
8. `Helper - Escalate Ticket` validates ownership and escalation state.
9. `Automation - Notify Manager` sends a Teams Adaptive Card.
10. Acknowledgement actions write back to Dataverse.
11. `SummariseTicket_BYOM` generates a summary and writes it to Dataverse.
12. SLA watcher automation tracks reminder and escalation conditions.

## Agent Behaviour

`IT Support (Portfolio)` is designed as a secure internal IT assistant.

Built behavior:

- Requires Entra ID / organizational sign-in.
- Uses identity-based user context.
- Collects structured ticket details.
- Calls Power Automate helper flows.
- Uses `TKT####` ticket references.
- Does not use typed email for authorization.
- Shows ticket information only after ownership validation.
- Supports urgent escalation.
- Routes known outcomes such as `InvalidFormat`, `NotFound`, `NotAuthorized`, `AlreadyEscalated`, and `Success`.

## Power Platform Components

| Component | Built Role |
| --- | --- |
| Copilot Studio | Authenticated V2 agent, structured intake, status checks, urgent escalation |
| Power Automate | Helper flows, notification watcher, Teams write-back, SLA watcher, BYOM summarisation |
| Dataverse | System of record for ticket state, identity, escalation, summary, acknowledgement, notification, SLA, and audit fields |
| Microsoft Teams | Manager Adaptive Card notification and acknowledgement actions |
| Azure OpenAI / Azure AI Foundry | BYOM model deployment for ticket summarisation |
| Azure Key Vault / Power Platform secret environment variables | Secure API key handling |

## Power Automate Flows

- `Helper - Create Ticket`
- `Helper - Check Status`
- `Helper - Escalate Ticket`
- `Automation - Notify Manager`
- `SummariseTicket_BYOM`
- SLA watcher / SLA escalation watcher

## Dataverse Summary

Dataverse `IT Ticket` / `IT Tickets` stores:

- User-facing ticket fields.
- Requestor identity fields.
- Escalation fields.
- Notification and Teams card fields.
- Acknowledgement fields.
- SLA / reminder fields.
- AI summary fields.
- Dataverse status fields.

See [dataverse/it-ticket-table.md](../dataverse/it-ticket-table.md) for the grouped column list.

## BYOM Summarisation

`SummariseTicket_BYOM` is a Power Automate child flow that sends ticket content to Azure OpenAI / Azure AI Foundry and writes a summary back to Dataverse.

Azure OpenAI configuration is externalised through environment variables, including endpoint, deployment name, API version, and summarisation prompt.

The Azure OpenAI API key is handled through secure secret configuration using Azure Key Vault / Power Platform secret environment variables. The key is retrieved at runtime and is not hardcoded in Power Automate flows, documentation, screenshots, or source files.

Environment variable names documented in this repo:

- `lai_AOAI_ApiKey`
- `lai_AOAI_ApiVersion`
- `lai_AOAI_DeploymentName`
- `lai_AOAI_Endpoint`
- `lai_ITSD_SummarisationPrompt`

No values are published.

## Governance And Safety Features

- Entra ID / organizational sign-in configured and tested.
- Identity-based user context built and tested.
- `RequestorAadObjectId` compared with `CurrentUserAadObjectId`.
- No typed email used for authorization.
- RBAC / least privilege.
- Field-Level Security on decision, acknowledgement, and escalation fields.
- Environment variables and connection references.
- Secure secret handling for Azure OpenAI API key.
- Unmanaged solution for build, managed solution for deployment.
- Audit-ready Dataverse state tracking.
- Idempotent watcher patterns using `NotificationSent` and escalation flags.
- No secrets or private tenant-specific details in the repo.

## Current Status

| Area | Status |
| --- | --- |
| V2 authenticated helpdesk design | Built |
| Entra ID / organizational sign-in | Configured and tested |
| Identity-based user context | Built and tested |
| Copilot Studio ticket intake | Built |
| Check ticket status | Built |
| Urgent escalation | Built |
| `Helper - Create Ticket` | Built |
| `Helper - Check Status` | Built |
| `Helper - Escalate Ticket` | Built |
| `Automation - Notify Manager` | Built |
| Teams Adaptive Card acknowledgement write-back | Built |
| `SummariseTicket_BYOM` | Built |
| Azure OpenAI secure configuration pattern | Built |
| SLA watcher / escalation watcher | Built |
| `IT Technician Console` | Built |
| RBAC / Field-Level Security design | Built |
| ALM approach | Documented |

## Follow-Up Improvements

- Add redacted screenshots to the public repo.
- Add sanitized solution export evidence if appropriate.
- Add public sample ticket data.
- Add more detailed ALM deployment notes.
- Add solution checker output when public-safe.
