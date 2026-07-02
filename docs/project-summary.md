# Project Summary

## Purpose

The IT Helpdesk Agent is a Power Platform / Copilot Studio portfolio project that demonstrates how an authenticated internal support agent can create, retrieve, and escalate helpdesk tickets using Dataverse and Power Automate. The Entra ID / organizational sign-in experience has been configured and tested successfully.

The project is designed to show more than a basic chatbot. It focuses on trusted identity, ticket ownership checks, escalation auditability, idempotent notification logic, and technician handoff.

## Target Users And Personas

### Employee / Helpdesk User

A signed-in internal user who needs to log an IT issue, check ticket progress, or escalate an urgent problem without needing to understand the backend process.

### IT Technician

A support team member who reviews escalated tickets, updates ticket status, records resolution notes, and manages handoff through the planned `IT Technician Console`.

### Manager / Escalation Owner

A manager or support lead who receives escalation notifications and needs confidence that duplicate notifications are avoided.

### Portfolio Reviewer / Recruiter

A reviewer assessing whether the project demonstrates practical Power Platform, Copilot Studio, Dataverse, Power Automate, security, and governance capability.

## Main User Journeys

1. A user signs in and logs a new ticket.
2. The agent creates a Dataverse `IT Ticket` row.
3. The user receives a `TKT####` ticket reference.
4. The user checks ticket status by entering the ticket reference.
5. The flow verifies that the signed-in user owns the ticket.
6. The user escalates an urgent ticket with a reason.
7. The escalation flow validates the ticket, checks ownership, prevents duplicate escalation, updates Dataverse, and returns a clear outcome.
8. A notification process tracks manager or Teams notification state.
9. A technician reviews escalated tickets in the planned model-driven app.

## Agent Behaviour

The agent is designed to behave like a secure internal IT assistant.

It should:

- Require sign-in before protected actions. This sign-in requirement is built and tested.
- Use system identity variables instead of typed email addresses.
- Ask structured questions when logging a ticket.
- Give clear, non-technical status messages.
- Avoid exposing tickets to users who do not own them.
- Explain invalid ticket formats using `TKT####`.
- Prevent repeated escalation alerts for the same ticket.
- Give calm expectation-setting messages after escalation.
- Use adaptive cards for clean status presentation where useful.

It should not:

- Trust user-entered email addresses for authorization.
- Show ticket details to a different signed-in user.
- Trigger duplicate manager notifications for an already escalated ticket.
- Continue protected actions when the signed-in identity is blank.
- Reference disabled system escalation topics without checking redirects.

## Conversation Flow

### Log A Ticket

User-provided inputs:

- Subject
- Ticket description
- Category
- Impact
- Urgency
- Priority

System identity inputs:

- `RequestorAadObjectId`
- `RequestorUPN`
- `RequestorDisplayName`

Flow called:

- `Helper - Create Ticket`

Expected outputs:

- `TicketID`
- `TicketStatus`
- `Priority`
- `AssignedTeam`

Example confirmation:

```text
Ticket TKT1001 created. Status: New.
```

### Check Ticket Status

User-provided input:

- `TicketID`

System identity input:

- `CurrentUserAadObjectId`

Flow called:

- `Helper - Check Status`

Logic:

1. Look up the ticket by `TicketID`.
2. Return `NotFound` if no ticket exists.
3. Compare `RequestorAadObjectId` with `CurrentUserAadObjectId`.
4. Return `NotAuthorized` if the signed-in user does not own the ticket.
5. Return ticket details if authorized.

Returned details may include:

- `TicketStatus`
- `Priority`
- `Subject`
- `AssignedTeam`
- `Escalated`
- `LastUpdatedOn`
- `ResolutionSummary`

### Urgent Escalation

User-provided inputs:

- `TicketID`
- Escalation reason

System identity input:

- `CurrentUserAadObjectId`

Flow called:

- `Helper - Escalate Ticket`

Recommended routing order:

1. `InvalidFormat`
2. `NotFound`
3. `AlreadyEscalated`
4. `NotAuthorized`
5. `Success`
6. Generic fallback

Dataverse updates on success:

- `Escalated` = Yes
- `EscalatedOn` = current time
- `EscalatedByAadObjectId` = `CurrentUserAadObjectId`
- `EscalationReason` = user-provided reason
- `EscalationCount` incremented
- `LastUpdatedOn` = current time
- `NotificationSent` = false, so the watcher flow can process it

## Power Platform Components

| Component | Use |
| --- | --- |
| Copilot Studio | Authenticated agent experience using Entra ID / organizational sign-in, topics, questions, branching, adaptive cards, and flow calls |
| Power Automate | Ticket creation, ticket lookup, authorization checks, escalation updates, idempotency, notification tracking, and flow outputs |
| Dataverse | Ticket system of record, identity fields, escalation audit fields, and technician app data source |
| Power Apps | Planned `IT Technician Console` model-driven app for escalated ticket triage |
| Solutions / ALM | `ITHelpdeskPortfolio` solution packaging, connection references, and future deployment notes |

## Dataverse Tables

### IT Ticket / IT Tickets

Primary table for ticket records.

Important columns:

- `Subject`
- `TicketID`
- `TicketDescription`
- `TicketStatus`
- `Priority`
- `Category`
- `Impact`
- `Urgency`
- `AssignedTeam`
- `LastUpdatedOn`
- `ResolutionSummary`
- `RequestorAadObjectId`
- `RequestorUPN`
- `RequestorDisplayName`
- `Escalated`
- `EscalatedOn`
- `EscalatedByAadObjectId`
- `EscalationReason`
- `EscalationReasonDetail`
- `EscalationCount`
- `NotificationSent`
- `NotificationSentOn`
- `NotificationError`
- `ManagerNotified`
- `ManagerNotifiedOn`
- `ManagerNotificationError`
- `AcknowledgedByAadObjectId`
- `AcknowledgedByDisplayName`
- `AcknowledgedByEmail`
- `AcknowledgedOn`
- `ConversationalSummary`
- `TicketSummary`
- `SummaryGeneratedOn`
- `SummarySource`
- `SummaryError`
- `Status`
- `Status Reason`

Notes:

- Dataverse also includes system `Status` and `Status Reason` fields.
- Resolved or closed tickets may be represented through Dataverse status fields rather than a custom closed-ticket column.
- Logical names should be confirmed before editing Power Automate expressions.

### Requestor Profile

`Requestor Profile` is an optional supporting table idea for richer user information.

Potential future uses:

- VIP routing
- Department-based assignment
- Manager escalation
- Preferred contact method

## Power Automate Flows

### Helper - Create Ticket

Purpose: create a new Dataverse ticket from the agent.

Inputs:

- `Subject`
- `TicketDescription`
- `Category`
- `Impact`
- `Urgency`
- `Priority`
- `RequestorAadObjectId`
- `RequestorUPN`
- `RequestorDisplayName`

Outputs:

- `TicketID`
- `TicketStatus`
- `Priority`
- `AssignedTeam`

### Helper - Check Status

Purpose: return ticket status only when the signed-in user owns the ticket.

Inputs:

- `UserTicketID`
- `CurrentUserAadObjectId`

Outputs:

- `NotFound`
- `NotAuthorized`
- `TicketStatus`
- `Priority`
- `Subject`
- `AssignedTeam`
- `Escalated`
- `LastUpdatedOn`
- `ResolutionSummary`

### Helper - Escalate Ticket

Purpose: escalate a ticket securely and only once.

Inputs:

- `UserTicketID`
- `CurrentUserAadObjectId`
- `EscalationReason`

Outputs:

- `InvalidFormat`
- `NotFound`
- `AlreadyEscalated`
- `NotAuthorized`
- `Success`

Important implementation note:

- The Dataverse Update row action must use the Dataverse primary key GUID, not the user-facing `TicketID`.

### Automation - Notify Manager

Purpose: send one manager or Teams notification after a ticket is escalated.

Trigger:

- Dataverse row modified

Intended condition:

- `Escalated` is true
- `NotificationSent` is not true

On success:

- Send notification.
- Set `NotificationSent` to true.
- Set `NotificationSentOn` to the current time.

On failure:

- Write `NotificationError`.
- Leave `NotificationSent` false so retry is possible.

## Inputs And Outputs

### User Inputs

- Ticket subject
- Ticket description
- Category
- Impact
- Urgency
- Priority
- Ticket reference
- Escalation reason

### System Inputs

- AAD Object ID
- UPN / email
- Display name

### User-Facing Outputs

- Ticket creation confirmation
- Ticket status card
- Invalid ticket format guidance
- Not found message
- Not authorized message
- Already escalated message
- Escalation success confirmation

## Governance And Safety Features

- Entra ID / organizational sign-in required for protected actions, configured and tested.
- Identity-based user context is built and tested.
- Identity values come from system variables.
- User-typed email is not trusted for authorization.
- Ticket ownership is checked using AAD Object ID.
- Escalation updates include audit fields.
- `AlreadyEscalated` prevents duplicate escalation logic.
- `NotificationSent` helps avoid duplicate manager notifications.
- Technician access is separated from employee self-service.
- Least-privilege roles are part of the intended design.
- Solution packaging uses `ITHelpdeskPortfolio`.
- Connection references and environment variables are planned ALM improvements.

## Human Review Points

- Technician reviews escalated tickets in `IT Technician Console`.
- Manager or support lead receives escalation notification.
- Technician updates assignment, status, and resolution notes.
- Optional acknowledgement fields can record who accepted or reviewed the escalation.
- Optional manager notification fields can track when escalation proof was sent.

## Error Handling

| Scenario | Intended Result |
| --- | --- |
| Ticket ID does not begin with `TKT` | Return `InvalidFormat` |
| Ticket does not exist | Return `NotFound` |
| Signed-in user does not own ticket | Return `NotAuthorized` |
| Ticket is already escalated | Return `AlreadyEscalated` |
| Notification fails | Store `NotificationError` and allow retry |
| `EscalationCount` is blank | Use a safe default before incrementing |
| Dataverse update needs row ID | Use primary key GUID, not `TicketID` |

## Current Build Status

| Area | Status |
| --- | --- |
| V2 authenticated helpdesk design | Defined |
| Entra ID / organizational sign-in | Configured and tested |
| Sign-in requirement | Built and tested |
| Identity-based user context | Built and tested |
| Dataverse ticket table | Built / being validated |
| Dataverse identity and escalation fields | Built / being validated |
| User email removed as authorization mechanism | Built |
| Identity-based ownership checks | Built / needs final screenshot evidence |
| `Helper - Create Ticket` | Built / needs final screenshot evidence |
| `Helper - Check Status` | Built / needs final screenshot evidence |
| `Helper - Escalate Ticket` | Built and debugged |
| Run-only connection issue | Fixed in context by using maker-owned connection |
| Ticket format `TKT####` | Built / standardized |
| `IT Technician Console` | Planned / work in progress |
| `Automation - Notify Manager` | Work in progress / needs final verification |
| Notification watcher trigger conditions | Work in progress / needs verification |
| Security roles | Planned / needs verification |
| Solution export/import validation | Planned |

## Known Gaps

- Confirm final Dataverse logical names.
- Confirm consistent table display name: `IT Ticket` or `IT Tickets`.
- Clean up duplicate or mistyped columns if present.
- Confirm final resolved/closed status model.
- Confirm adaptive card fields for status responses.
- Confirm `Automation - Notify Manager` trigger conditions.
- Confirm role permissions are implemented.
- Confirm all flows are inside `ITHelpdeskPortfolio`.
- Confirm environment variables and connection references.
- Confirm solution export/import works cleanly.
- Confirm disabled system topics are not still referenced.

## Future Improvements

- SLA timer and breach alerts.
- VIP user routing through `Requestor Profile`.
- Department-based assignment.
- Manager lookup.
- Technician acknowledgement flow.
- Director escalation path.
- Escalation history table.
- Power BI or Dataverse charts.
- AI-generated ticket summaries.
- Adaptive card action buttons for technicians.
- User-facing notification when ticket status changes.
- Solution checker output.
- Test scripts and sample data.
- Dev/Test/Prod ALM pipeline notes.
