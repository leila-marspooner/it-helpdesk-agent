# SLA_Watcher_Escalations

## Purpose

`SLA_Watcher_Escalations` monitors ticket state and supports reminder and director escalation patterns.

## Dataverse Fields

Relevant fields include:

- `ReminderCount`
- `LastReminderOn`
- `EscalatedToDirector`
- `DirectorEscalatedOn`
- `TicketStatus`
- `Priority`
- `Urgency`

## Processing Pattern

The watcher evaluates ticket state and updates Dataverse reminder or escalation fields when conditions are met.

The pattern is designed to be repeat-safe and audit-ready. State fields prevent repeated processing for the same condition.

## Governance Notes

SLA and escalation fields should be protected with RBAC / least privilege and Field-Level Security where appropriate.
