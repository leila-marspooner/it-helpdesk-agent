# Helper - Check Status

## Purpose

`Helper - Check Status` returns ticket information only when the signed-in user owns the requested ticket.

## Trigger

Called from the `IT Support Assistant` Copilot Studio agent.

## Inputs

- `UserTicketID`
- `CurrentUserAadObjectId`

## Processing

1. Normalize and validate the ticket reference.
2. Look up the Dataverse ticket row.
3. Compare `RequestorAadObjectId` with `CurrentUserAadObjectId`.
4. Return ticket information only when the signed-in user owns the ticket.

## Outcomes

- `NotFound`
- `NotAuthorized`
- Successful ticket status response

## Protected Data

The ownership check prevents users from viewing another requestor's ticket details. This flow is part of the V2 identity-based security model.
