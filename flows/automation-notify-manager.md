# Automation - Notify Manager

## Purpose

`Automation - Notify Manager` sends Teams Adaptive Card notifications for escalated tickets and tracks notification state in Dataverse.

## Trigger Pattern

The watcher monitors Dataverse ticket state.

The idempotent pattern uses fields such as:

- `Escalated`
- `NotificationSent`
- `ManagerNotified`

This prevents repeated notification processing for the same escalation state.

Evidence:

- [Escalation watcher overview](../screenshots/escalation-watcher-flow-overview.png)
- [Escalation watcher switch branches](../screenshots/escalation-watcher-flow-switch-branches.png)
- [Escalation watcher default branch](../screenshots/escalation-watcher-flow-switch-default.png)

## Teams Adaptive Card Write-Back

The notification card supports manager acknowledgement. Acknowledgement actions write back to Dataverse fields such as:

- `AcknowledgedOn`
- `AcknowledgedByAadObjectId`
- `AcknowledgedByDisplayName`
- `AcknowledgedByEmail`
- `CardSentOn`

Evidence: [Teams Adaptive Card urgent escalation screenshot](../screenshots/teams-adaptive-card-urgent-escalation.webp).

## Error Tracking

Notification errors are tracked with fields such as:

- `NotificationError`
- `ManagerNotificationError`

## Governance Notes

Acknowledgement, escalation, and decision fields should be protected with RBAC / least privilege and Field-Level Security where appropriate.
