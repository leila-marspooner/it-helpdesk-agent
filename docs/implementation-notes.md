# Implementation Notes

## Overview

This document summarizes the implementation approach for the IT Helpdesk Agent portfolio project. It focuses on the Power Platform design choices behind `IT Support (Portfolio)`, including authenticated access, Dataverse ticket storage, Power Automate helper flows, ticket ownership checks, and escalation handling.

The project is documented as a portfolio/demo build. It demonstrates enterprise-style patterns, but it should not be treated as production-ready without further validation, security review, and deployment testing.

## Key Design Decisions

| Area | Implementation Approach |
| --- | --- |
| Agent experience | `IT Support (Portfolio)` provides the authenticated Copilot Studio front end |
| Authentication | Entra ID / organizational sign-in is configured and tested |
| Solution container | `ITHelpdeskPortfolio` is used as the Power Platform solution name |
| System of record | Dataverse `IT Ticket` / `IT Tickets` stores ticket and escalation data |
| Ticket reference | User-facing ticket references use the `TKT####` format, for example `TKT1001` |
| Authorization | User-typed email is not used as the trusted authorization mechanism |
| Ownership check | `RequestorAadObjectId` is compared with `CurrentUserAadObjectId` |
| Escalation handling | `AlreadyEscalated` prevents duplicate escalation updates |
| Technician handoff | `IT Technician Console` is planned / work in progress |

## Identity And Ownership Model

The authenticated experience uses Entra ID / organizational sign-in. The sign-in requirement and identity-based user context are built and tested.

Protected ticket actions use identity values from the signed-in session rather than relying on a user-typed email address. When a ticket is created, the requestor identity is stored on the Dataverse row.

Important identity fields:

- `RequestorAadObjectId`
- `RequestorUPN`
- `RequestorDisplayName`
- `CurrentUserAadObjectId`

For status checks and escalation, Power Automate compares `RequestorAadObjectId` with `CurrentUserAadObjectId` before returning or updating protected ticket data.

Sign-in establishes the user's identity, while Power Automate enforces record-level ticket ownership checks before returning or updating protected ticket data.

## Ticket Reference Standard

The final user-facing ticket reference format is:

```text
TKT####
```

Example:

```text
TKT1001
```

The no-dash format is the current standard for public documentation and demo examples.

## Escalation Handling

`Helper - Escalate Ticket` is built and debugged. The escalation flow is designed to validate the request, protect ticket ownership, and prevent duplicate escalation updates.

Expected escalation outcomes:

- `InvalidFormat`
- `NotFound`
- `AlreadyEscalated`
- `NotAuthorized`
- `Success`

On successful escalation, Dataverse stores escalation audit fields such as:

- `Escalated`
- `EscalatedOn`
- `EscalatedByAadObjectId`
- `EscalationReason`
- `EscalationCount`
- `NotificationSent`

The `AlreadyEscalated` outcome supports idempotency by preventing repeated escalation processing for the same ticket.

## Dataverse Implementation Notes

Dataverse is used as the system of record for helpdesk tickets, requestor identity, escalation state, and notification tracking.

Key implementation details:

- Ticket data is stored in `IT Ticket` / `IT Tickets`.
- Requestor identity fields support ownership checks.
- Escalation fields support auditability.
- Notification fields support duplicate notification prevention.
- Dataverse Update row actions must use the Dataverse primary key GUID, not the user-facing `TicketID`.
- Display names and logical names should be checked before editing Power Automate expressions.

The Dataverse ticket table is built / being validated. Final logical names, table naming consistency, and status field usage should be confirmed before publishing detailed schema exports.

## Power Automate Implementation Notes

The agent calls Power Automate helper flows for ticket operations.

| Flow | Current Status |
| --- | --- |
| `Helper - Create Ticket` | Built / needs final screenshot evidence |
| `Helper - Check Status` | Built / needs final screenshot evidence |
| `Helper - Escalate Ticket` | Built and debugged |
| `Automation - Notify Manager` | Work in progress / needs final verification |

Implementation notes:

- `Helper - Create Ticket` stores signed-in requestor identity fields on the ticket.
- `Helper - Check Status` returns ticket details only after ownership validation.
- `Helper - Escalate Ticket` uses validation, lookup, idempotency, ownership check, update, and return flags.
- `Automation - Notify Manager` is intended to use `NotificationSent` to avoid duplicate alerts, but final trigger behavior still needs verification.
- A run-only connection binding issue was identified and fixed by using the maker-owned connection.

## Current Verification Status

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
| `Automation - Notify Manager` | Work in progress / needs final verification |
| `IT Technician Console` | Planned / work in progress |
| Security roles | Planned / needs verification |
| Solution export/import validation | Planned |

## Follow-Up Items

- Confirm final Dataverse logical names for all fields used in expressions.
- Confirm whether the table display name is consistently `IT Ticket` or `IT Tickets` across assets.
- Verify `Automation - Notify Manager` trigger conditions and notification retry behavior.
- Confirm least-privilege security roles for helpdesk users and technicians.
- Confirm all flows are included in `ITHelpdeskPortfolio`.
- Add connection references and environment variables where appropriate.
- Export and validate the solution package.
- Add redacted screenshots and public demo assets.
- Complete or document the planned `IT Technician Console`.
