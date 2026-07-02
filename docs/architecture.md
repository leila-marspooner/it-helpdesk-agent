# Architecture

## High-Level Architecture

The IT Helpdesk Agent uses Copilot Studio as the user-facing agent, Power Automate as the orchestration layer, Dataverse as the system of record, and a planned model-driven app for technician handoff. The authenticated Copilot Studio experience using Entra ID / organizational sign-in has been configured and tested.

```text
Signed-in user
    |
    v
IT Support (Portfolio)
    |
    v
Power Automate helper flows
    |
    v
Dataverse IT Ticket / IT Tickets
    |
    +--> Automation - Notify Manager
    |
    +--> IT Technician Console
```

The design goal is to keep the user experience simple while moving authorization, ticket updates, escalation handling, and audit tracking into governed Power Platform services.

## Copilot Studio Role

`IT Support (Portfolio)` is the authenticated conversation layer. The sign-in requirement is built and tested, and identity-based user context is built and tested.

It is responsible for:

- Requiring organizational sign-in for protected actions.
- Collecting ticket details in a structured way.
- Calling `Helper - Create Ticket`, `Helper - Check Status`, and `Helper - Escalate Ticket`.
- Passing signed-in identity values into flows.
- Presenting clear outcomes to the user.
- Handling known return states such as `NotFound`, `NotAuthorized`, `AlreadyEscalated`, `InvalidFormat`, and `Success`.

The agent should not perform authorization by asking the user to type or confirm their email address.

## Power Automate Role

Power Automate contains the process logic that supports the agent.

### Helper - Create Ticket

Creates a new Dataverse ticket and stores requestor identity fields:

- `RequestorAadObjectId`
- `RequestorUPN`
- `RequestorDisplayName`

It returns the user-facing `TicketID`, status, priority, and assigned team.

### Helper - Check Status

Looks up a ticket by `TicketID` and checks whether the signed-in user owns it.

Authorization pattern:

```text
RequestorAadObjectId == CurrentUserAadObjectId
```

If the check fails, the flow returns `NotAuthorized` instead of ticket details.

### Helper - Escalate Ticket

Validates and escalates a ticket.

The intended logic is:

1. Normalize the ticket reference.
2. Validate the `TKT####` style format, for example `TKT1001`.
3. Look up the ticket in Dataverse.
4. Return `NotFound` if no row exists.
5. Check `AlreadyEscalated` before making another update.
6. Check ownership using `CurrentUserAadObjectId`.
7. Update escalation fields when valid and authorized.
8. Return a clear status flag to Copilot Studio.

Important implementation detail:

- Dataverse Update row must use the Dataverse primary key GUID, not the user-facing `TicketID`.

### Automation - Notify Manager

The watcher flow is intended to send one notification after escalation.

Trigger condition:

- `Escalated` is true.
- `NotificationSent` is not true.

After notification, the flow should update:

- `NotificationSent`
- `NotificationSentOn`

If notification fails, it should store `NotificationError` and leave `NotificationSent` false so the notification can be retried.

## Dataverse Role

Dataverse stores tickets and the audit fields needed for secure self-service.

The central table is `IT Ticket` / `IT Tickets`.

Main groups of fields:

| Field Group | Examples |
| --- | --- |
| Ticket details | `TicketID`, `Subject`, `TicketDescription`, `TicketStatus`, `Priority`, `Category`, `Impact`, `Urgency` |
| Assignment | `AssignedTeam`, `ResolutionSummary`, `LastUpdatedOn` |
| Requestor identity | `RequestorAadObjectId`, `RequestorUPN`, `RequestorDisplayName` |
| Escalation audit | `Escalated`, `EscalatedOn`, `EscalatedByAadObjectId`, `EscalationReason`, `EscalationCount` |
| Notification tracking | `NotificationSent`, `NotificationSentOn`, `NotificationError` |
| Review tracking | `AcknowledgedByAadObjectId`, `AcknowledgedByDisplayName`, `AcknowledgedOn` |

Dataverse also provides security roles, auditability, and a shared data model for the model-driven app.

## Model-Driven App / Technician Console Role

`IT Technician Console` is the planned technician-facing model-driven app.

Its intended role is to:

- Show escalated tickets.
- Let technicians triage priority work.
- Review requestor and escalation audit fields.
- Update ticket status, assignment, and resolution notes.
- Provide a controlled handoff point between the agent and human support staff.

This component should be presented as planned or work in progress until screenshots and implementation evidence are added.

## Authentication And Authorization

The project uses Entra ID / organizational sign-in for protected actions. This authenticated experience is configured and tested.

The important design decision is to use trusted identity values from the signed-in session instead of user-entered email addresses. The identity-based user context is built and tested; ownership checks are built but still need final public screenshot evidence before being presented visually in the repo.

Key fields:

- `RequestorAadObjectId`: stored on the ticket when it is created.
- `CurrentUserAadObjectId`: passed from the current signed-in session.

For status checks and escalation, the flow compares those values before returning or changing protected ticket details.

Expected outcomes:

| Outcome | Meaning |
| --- | --- |
| `Success` | The action was valid and authorized |
| `InvalidFormat` | The ticket reference does not match the expected format |
| `NotFound` | No matching ticket was found |
| `AlreadyEscalated` | The ticket was already escalated and should not be escalated again |
| `NotAuthorized` | The signed-in user does not own the ticket |

## Escalation And Notification Logic

Escalation is designed to be idempotent. That means the same ticket should not trigger repeated escalation updates or duplicate manager notifications.

On successful escalation:

- `Escalated` is set to true.
- `EscalatedOn` is populated.
- `EscalatedByAadObjectId` is recorded.
- `EscalationReason` is stored.
- `EscalationCount` is incremented.
- `NotificationSent` is set to false so the watcher can process the notification.

The watcher flow then sends or tracks the notification and marks `NotificationSent` true only after notification succeeds.

## ALM / Solution Packaging Notes

The intended solution name is `ITHelpdeskPortfolio`.

Recommended ALM approach:

- Build and test in an unmanaged development solution.
- Use connection references for deployable flows.
- Use environment variables for values that differ by environment.
- Confirm Dataverse logical names before editing expressions.
- Export and unpack the solution before committing platform artifacts.
- Use managed solutions for test or production-style promotion.
- Keep private notes and raw exports out of the public repo unless sanitized.

Current ALM status should be treated as work in progress until solution export, import validation, and connection-reference checks are complete.
