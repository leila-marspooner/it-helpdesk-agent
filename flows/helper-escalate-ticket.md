# Helper - Escalate Ticket

## Purpose

`Helper - Escalate Ticket` escalates urgent tickets after validating the ticket reference, ticket existence, ownership, and current escalation state.

## Trigger

Called from `IT Support (Portfolio)`.

## Inputs

- `UserTicketID`
- `CurrentUserAadObjectId`
- Escalation reason

## Processing

1. Normalize the ticket reference.
2. Validate the `TKT####` format.
3. Look up the Dataverse ticket row.
4. Check whether the ticket is already escalated.
5. Compare `RequestorAadObjectId` with `CurrentUserAadObjectId`.
6. Update Dataverse escalation fields.
7. Return a clear outcome to Copilot Studio.

## Outcomes

- `InvalidFormat`
- `NotFound`
- `AlreadyEscalated`
- `NotAuthorized`
- `Success`

## Dataverse Updates

On success, the flow updates escalation fields such as:

- `Escalated`
- `EscalatedOn`
- `EscalatedByAadObjectId`
- `EscalationReason`
- `EscalationReasonDetail`
- `EscalationCount`
- `NotificationSent`

## Implementation Note

Dataverse Update row actions must use the Dataverse primary key GUID, not the user-facing `TicketID`.
