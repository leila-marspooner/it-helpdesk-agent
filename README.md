# IT Helpdesk Agent

An authenticated Copilot Studio IT support agent that lets signed-in users log tickets, check ticket status, and escalate urgent issues using Dataverse-backed ownership checks and Power Automate helper flows.

This is a Power Platform portfolio project. It is designed to demonstrate secure agent patterns for an internal helpdesk scenario, not to claim a production deployment.

## Business Problem

Internal helpdesk bots can create risk when they rely on user-entered details, such as typed email addresses, to decide who can view or escalate a ticket. In a business environment, helpdesk automation should use trusted identity, protect ticket visibility, and keep a clear audit trail.

This project explores how a Copilot Studio agent can support a governed helpdesk process by using configured and tested organizational sign-in, Dataverse as the system of record, Power Automate orchestration, and technician handoff through a model-driven app.

## Solution Overview

The solution is centered on an authenticated Copilot Studio agent named `IT Support (Portfolio)`. The Entra ID / organizational sign-in experience has been configured and tested successfully.

The agent supports three main user journeys:

1. Log a new IT ticket.
2. Check the status of an existing ticket.
3. Escalate an urgent ticket.

Protected actions use the signed-in user's identity rather than asking the user to type or confirm their email address. Ticket ownership is checked by comparing `RequestorAadObjectId` on the Dataverse ticket with `CurrentUserAadObjectId` from the signed-in session.

## Architecture Overview

At a high level, the solution uses the components below. For the detailed technical design, see [docs/architecture.md](docs/architecture.md).

| Layer | Role |
| --- | --- |
| Copilot Studio | Authenticated agent experience using Entra ID / organizational sign-in, topic routing, user prompts, and flow calls |
| Power Automate | Helper flows for ticket creation, status checks, escalation, notification handling, and error outputs |
| Dataverse | `IT Ticket` / `IT Tickets` table for ticket details, requestor identity fields, escalation fields, and notification tracking |
| Power Apps | Planned `IT Technician Console` model-driven app for technician triage and handoff |
| Entra ID | Organizational sign-in and trusted user identity for protected actions, configured and tested |

The intended Power Platform solution container is `ITHelpdeskPortfolio`.

## Key Capabilities

- Authenticated Copilot Studio experience using Entra ID / organizational sign-in, configured and tested.
- Identity-based user context, built and tested.
- Dataverse-backed ticket storage through `IT Ticket` / `IT Tickets`.
- Ticket references using the `TKT####` format, for example `TKT1001`.
- Ticket creation through `Helper - Create Ticket`.
- Status lookup through `Helper - Check Status`.
- Ownership checks using `RequestorAadObjectId` and `CurrentUserAadObjectId`.
- Urgent escalation through `Helper - Escalate Ticket`.
- Escalation response handling for `InvalidFormat`, `NotFound`, `AlreadyEscalated`, `NotAuthorized`, and `Success`.
- Idempotent escalation logic so already escalated tickets do not trigger duplicate escalation actions.
- Notification tracking using fields such as `NotificationSent`, `NotificationSentOn`, `NotificationError`, and `EscalationCount`.
- Planned technician handoff through `IT Technician Console`.

## Power Platform Components

### Copilot Studio

`IT Support (Portfolio)` provides the conversational front end. The sign-in requirement is built and tested. The agent is designed to collect structured ticket details, call helper flows, and return clear user-facing responses.

Important topics include:

- Log a Ticket
- Check Ticket Status
- Urgent Escalation
- Sign in system topic
- Fallback and end-of-conversation handling

### Power Automate

The helper flows are designed to keep business logic outside the conversation text and make the agent easier to govern.

| Flow | Purpose |
| --- | --- |
| `Helper - Create Ticket` | Creates a Dataverse ticket with requestor identity fields |
| `Helper - Check Status` | Returns ticket details only when the signed-in user owns the ticket |
| `Helper - Escalate Ticket` | Validates, authorizes, and escalates a ticket once |
| `Automation - Notify Manager` | Watches escalated tickets and sends one manager or Teams notification |

### Dataverse

Dataverse is used as the system of record for helpdesk tickets. Important fields include:

- `TicketID`
- `TicketStatus`
- `Subject`
- `TicketDescription`
- `Priority`
- `Category`
- `Impact`
- `Urgency`
- `AssignedTeam`
- `RequestorAadObjectId`
- `RequestorUPN`
- `RequestorDisplayName`
- `Escalated`
- `EscalatedOn`
- `EscalatedByAadObjectId`
- `EscalationReason`
- `EscalationCount`
- `NotificationSent`
- `NotificationSentOn`
- `NotificationError`
- `ResolutionSummary`
- `LastUpdatedOn`

## Security And Governance Features

This project is intentionally focused on secure agent design patterns:

- Entra ID / organizational sign-in for protected actions, configured and tested.
- No reliance on user-typed email addresses for authorization.
- AAD Object ID based ticket ownership checks.
- Dataverse audit fields for escalation and notification state.
- Idempotent escalation handling with `AlreadyEscalated`.
- Notification tracking to avoid duplicate manager alerts.
- Least-privilege role thinking for helpdesk users and technicians.
- Solution-based packaging through `ITHelpdeskPortfolio`.
- Connection references and environment variables as planned ALM improvements.

## Demo Walkthrough

A short portfolio demo can show:

1. The user signs in to `IT Support (Portfolio)`.
2. The user logs a new IT issue.
3. `Helper - Create Ticket` creates a Dataverse row.
4. The generated `TKT####` ticket reference is returned to the user.
5. The user checks ticket status.
6. `Helper - Check Status` verifies ownership using `CurrentUserAadObjectId`.
7. The user escalates an urgent ticket with a reason.
8. `Helper - Escalate Ticket` validates the ticket, checks ownership, prevents duplicate escalation, and updates Dataverse.
9. `Automation - Notify Manager` sends or prepares escalation notification tracking.
10. A technician reviews escalated tickets in the planned `IT Technician Console`.

## Screenshots

Screenshots are not included yet. Recommended placeholders:

| Screenshot | Status |
| --- | --- |
| Copilot Studio authentication setting | Needed |
| `IT Support (Portfolio)` agent overview | Needed |
| Log a Ticket topic | Needed |
| Check Ticket Status topic | Needed |
| Urgent Escalation topic | Needed |
| `Helper - Create Ticket` flow inputs | Needed |
| `Helper - Check Status` ownership check | Needed |
| `Helper - Escalate Ticket` validation and idempotency logic | Needed |
| Dataverse `IT Ticket` columns | Needed |
| Example ticket row with private data redacted | Needed |
| `IT Technician Console` escalated tickets view | Planned / needed when built |
| Manager or Teams notification proof | Needed when safe to share |

Before adding screenshots, redact personal email addresses, tenant URLs, environment names that should not be public, real user details, secrets, tokens, and private ticket content.

## Current Status

| Area | Status |
| --- | --- |
| V2 authenticated helpdesk design | Defined |
| Entra ID / organizational sign-in | Configured and tested |
| Sign-in requirement | Built and tested |
| Identity-based user context | Built and tested |
| Dataverse ticket table | Built / being validated |
| Identity-based ownership checks | Built / needs final screenshot evidence |
| `Helper - Create Ticket` | Built / needs final screenshot evidence |
| `Helper - Check Status` | Built / needs final screenshot evidence |
| `Helper - Escalate Ticket` | Built and debugged |
| AAD Object ID authorization pattern | Built / needs final screenshot evidence |
| `TKT####` ticket reference standard | Built / standardized |
| `Automation - Notify Manager` | Work in progress / needs final verification |
| `IT Technician Console` | Planned / work in progress |
| Security roles | Planned / needs verification |
| Solution export/import validation | Planned |

## Known Gaps And Future Improvements

- Confirm final Dataverse logical names before relying on Power Automate expressions.
- Confirm whether the table display name is consistently `IT Ticket` or `IT Tickets` across all assets.
- Clean up any duplicate or mistyped Dataverse columns.
- Confirm final status model for resolved and closed tickets.
- Verify watcher flow trigger conditions for `Automation - Notify Manager`.
- Verify least-privilege security roles are implemented, not only documented.
- Confirm all flows are included in `ITHelpdeskPortfolio`.
- Add connection references and environment variables where appropriate.
- Export, unpack, and validate the solution for ALM.
- Add safe screenshots and a simple architecture diagram.
- Consider SLA tracking, VIP routing, technician acknowledgement, escalation history, dashboard reporting, and user-facing status-change notifications.

## Repository Structure

```text
.
├── agents/          # Public agent exports or sanitized notes, when available
├── architecture/    # Public architecture diagrams and related assets
├── assets/          # Public images or supporting repo assets
├── dataverse/       # Sanitized Dataverse schema notes or exports, when available
├── demo/            # Demo notes, scripts, and safe sample data
├── docs/            # Public project documentation
├── flows/           # Sanitized Power Automate flow documentation or exports
├── screenshots/     # Redacted public screenshots
└── source-notes/    # Private local notes, ignored by Git
```

## Portfolio Disclaimer

This repository documents a portfolio/demo Power Platform project. It demonstrates enterprise-style patterns for authenticated agent design, Dataverse authorization checks, escalation handling, and technician handoff. It should be reviewed as a learning and portfolio project unless a separate production deployment, security review, and operational readiness process are completed.
