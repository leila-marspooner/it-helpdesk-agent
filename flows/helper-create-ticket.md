# Helper - Create Ticket

## Purpose

`Helper - Create Ticket` creates a Dataverse `IT Ticket` row from the authenticated Copilot Studio agent.

## Trigger

Called from the `IT Support Assistant` Copilot Studio agent.

## Inputs

- Subject
- Ticket description
- Category
- Impact
- Urgency
- Priority
- `RequestorAadObjectId`
- `RequestorUPN`
- `RequestorDisplayName`

## Processing

The flow creates a new row in `IT Ticket` / `IT Tickets` and stores the requestor identity from the signed-in session.

The flow does not rely on a typed email address for authorization.

## Outputs

- `TicketID`
- `TicketStatus`
- Priority
- Assigned team

Ticket references use `TKT####`, for example `TKT1001`.

## Dataverse Notes

The flow stores the original requestor identity so later status checks and escalation actions can compare `RequestorAadObjectId` with `CurrentUserAadObjectId`.
