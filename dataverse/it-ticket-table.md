# IT Ticket / IT Tickets Table

## Purpose

`IT Ticket` / `IT Tickets` is the Dataverse table used as the system of record for the V2 IT Helpdesk Agent.

Ticket references use `TKT####`, for example `TKT1001`.

## User-Facing / Ticket Fields

- `TicketID`
- `Subject`
- `TicketDescription`
- `TicketStatus`
- `Status`
- `Status Reason`
- `Priority`
- `Impact`
- `Urgency`
- `Category`
- `AssignedTeam`
- `ResolutionSummary`
- `ConversationalSummary`

## Requestor Identity

- `RequestorAadObjectId`
- `RequestorUPN`
- `RequestorDisplayName`
- `Requestor Profile`

## Escalation

- `Escalated`
- `EscalatedOn`
- `EscalatedByAadObjectId`
- `EscalationReason`
- `EscalationReasonDetail`
- `EscalationCount`
- `EscalatedToDirector`
- `DirectorEscalatedOn`

## Notification / Teams

- `NotificationSent`
- `NotificationSentOn`
- `NotificationError`
- `ManagerNotified`
- `ManagerNotifiedOn`
- `ManagerNotificationError`
- `CardSentOn`

## Acknowledgement

- `AcknowledgedOn`
- `AcknowledgedByAadObjectId`
- `AcknowledgedByDisplayName`
- `AcknowledgedByEmail`

## SLA / Reminder

- `ReminderCount`
- `LastReminderOn`

## AI Summary

- `TicketSummary`
- `SummaryGeneratedOn`
- `SummarySource`
- `SummaryError`

## Governance Notes

- `RequestorAadObjectId` is compared with `CurrentUserAadObjectId` for ownership checks.
- Escalation, acknowledgement, and decision fields should use RBAC / least privilege and Field-Level Security where appropriate.
- Audit-ready fields support notification, acknowledgement, escalation, SLA, and summary tracking.
- Do not publish real ticket data, user data, tenant details, or environment details.
