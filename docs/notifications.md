# Notifications.md

## 1. Purpose

This document defines the functional, technical, security, and reliability requirements for the Schedular app notification system.

It is the authoritative reference for implementing local notifications, schedule reminders, notification preferences, recurring reminders, notification actions, and notification-related error handling.

The system must be reliable, predictable, privacy-conscious, accessible, and consistent across supported Android and iOS versions.

### Core principles

- Notifications must be generated from actual user-created schedule data.
- A notification must never claim an event is happening at an incorrect time.
- Scheduling, editing, completing, deleting, and rescheduling an item must keep its reminders synchronized.
- Notification permissions must be requested in context, not immediately at app launch without a reason.
- Notification delivery must not be assumed to be guaranteed by the operating system.
- Notification-related state must be persisted wherever required for recovery and reconciliation.
- The app must remain usable when notification permission is denied.
- All notification behavior must be testable without relying exclusively on real device delivery.
- No fake notification history, delivery status, or success indicators are allowed.
- Backend-dependent behavior must not be implemented until the backend architecture is approved.

---

## 2. Scope

### In scope

- Local notifications for scheduled items.
- User-configurable reminder offsets.
- Multiple reminders per schedule item, where supported by the approved product requirements.
- Recurring schedule reminders.
- Rescheduling after edits.
- Cancellation after deletion or completion, according to product rules.
- Notification permissions and settings.
- Notification action handling.
- Time-zone and daylight-saving-time behavior.
- Notification reconciliation after app startup and relevant lifecycle changes.
- Notification preferences and quiet hours.
- Error handling, diagnostics, accessibility, and testing.
- Optional remote push notifications if explicitly approved for a future phase.

### Out of scope unless separately approved

- Marketing notifications.
- Promotional campaigns.
- Social notifications.
- Email or SMS reminders.
- AI-generated notification content.
- Remote push notifications requiring a server.
- Notification-based location tracking.
- Notifications that expose sensitive schedule information without user consent.

Do not implement out-of-scope features merely because a library supports them.

---

## 3. Technology and Architecture

### 3.1 Mobile platform

The mobile application uses Flutter and Dart.

The notification implementation must use a platform-compatible notification package selected during dependency approval. The implementation must verify support for the project's minimum Android and iOS versions before the dependency is adopted.

A candidate package is `flutter_local_notifications`, subject to compatibility verification and approval.

The notification package must be isolated behind an application-owned abstraction. Product logic must not depend directly on package-specific APIs.

### 3.2 Proposed architecture

Use the following logical components:

- `NotificationService`: Application-facing interface for notification operations.
- `LocalNotificationService`: Platform-specific local notification adapter.
- `NotificationScheduler`: Converts schedule items and reminder rules into notification plans.
- `ReminderPolicy`: Determines which reminders are valid and when they should fire.
- `NotificationReconciler`: Compares expected reminders with scheduled platform notifications and repairs differences where possible.
- `NotificationActionHandler`: Processes taps and supported notification actions.
- `NotificationPermissionService`: Checks and requests notification permission.
- `NotificationPreferencesRepository`: Reads and persists user preferences.
- `NotificationIdGenerator`: Creates stable, collision-resistant notification identifiers.
- `ScheduleRepository`: Provides the authoritative schedule data.
- `Clock` and `TimeZoneProvider`: Supply controllable time and time-zone information for calculations and tests.

These components describe responsibilities, not mandatory class names. Follow the approved architecture and existing repository conventions.

### 3.3 Dependency direction

Application logic must depend on interfaces or application-owned abstractions rather than directly on platform notification APIs.

The notification layer may depend on:

- Approved Flutter notification packages.
- Platform APIs required by the chosen package.
- The approved schedule and preferences repositories.
- The approved date/time utilities.

The notification package must not become the source of truth for schedule items or reminder preferences.

### 3.4 Backend status

The backend, database provider, authentication mechanism, and remote push infrastructure are not considered finalized by this document.

Local notifications must be designed so that a future approved backend can coexist with them without requiring the scheduling domain to be rewritten.

Do not introduce Firebase Cloud Messaging, Supabase Edge Functions, a background server, or another remote notification service without explicit architectural approval.

---

## 4. Notification Types

The initial implementation should support only notification types required by the approved product scope.

| Type | Purpose | Initial status |
|---|---|---|
| Schedule reminder | Remind a user before a scheduled item | Required |
| Due-time reminder | Notify the user at the scheduled time when configured | Optional, subject to product requirements |
| Recurring schedule reminder | Remind a user about an occurrence of a recurring item | Required when recurrence is supported |
| Overdue notification | Alert the user after an item becomes overdue | Not enabled by default; requires product approval |
| Daily summary | Summarize upcoming schedule items | Requires product approval |
| Remote push | Deliver a server-originated notification | Future scope, subject to approval |
| Marketing notification | Promotional communication | Out of scope |

A notification type must not be enabled merely because the platform or dependency supports it.

---

## 5. Reminder Data Model

Reminder configuration must be represented independently from the operating system's scheduled notification records.

The following is a proposed logical model. Adapt field names to the approved database schema.

### 5.1 Reminder configuration

A reminder rule may contain:

| Field | Description |
|---|---|
| `id` | Stable reminder-rule identifier |
| `schedule_item_id` | Parent schedule item |
| `offset_minutes` | Number of minutes before the occurrence |
| `enabled` | Whether the rule is active |
| `created_at` | Creation timestamp |
| `updated_at` | Last modification timestamp |

Requirements:

- Validate offsets before scheduling.
- Reject unsupported negative or excessively large values according to approved product limits.
- Prevent duplicate reminder rules where the product considers them equivalent.
- Preserve reminder configuration when editing unrelated schedule fields.
- Ensure deleting a schedule item also removes its associated reminder rules.

If the approved schema uses a different reminder model, update this document and the implementation together.

### 5.2 Scheduled notification record

Where persistence is required, track a logical notification plan separately from the platform notification identifier.

Recommended fields:

| Field | Description |
|---|---|
| `id` | Stable internal identifier |
| `schedule_item_id` | Related schedule item |
| `occurrence_key` | Identifier for a specific occurrence |
| `reminder_rule_id` | Related reminder rule, if applicable |
| `scheduled_at_utc` | Calculated delivery instant |
| `notification_id` | Platform notification identifier |
| `status` | Current scheduling state |
| `last_reconciled_at` | Last reconciliation timestamp |
| `updated_at` | Last state change |

Suggested internal states:

- `pending`
- `scheduled`
- `cancelled`
- `skipped`
- `failed`

These states describe the app's scheduling workflow. A `scheduled` status means the app successfully submitted the notification to the platform API; it does not prove that the operating system delivered it or that the user saw it.

Do not report local notifications as delivered or read unless a platform capability actually confirms the relevant event.

---

## 6. Notification Scheduling Rules

### 6.1 Basic calculation

For a one-time schedule item:

`notification_time = event_start_time - reminder_offset`

Calculate the event's actual instant using its approved time-zone semantics before subtracting the reminder offset.

Example:

- Event start: 3:00 PM.
- Reminder offset: 15 minutes.
- Expected notification time: 2:45 PM.

Do not schedule a reminder if its calculated time is invalid or outside the product's allowed scheduling window.

### 6.2 Past reminders

When a reminder time is already in the past:

- Do not silently deliver a stale reminder as if it were current.
- Mark it as skipped or otherwise record the appropriate internal outcome.
- Apply any approved catch-up behavior explicitly.
- Do not automatically reschedule it for the current time unless that behavior is an approved product requirement.

A past event should not generate a misleading upcoming reminder.

### 6.3 Multiple reminders

When multiple reminder rules are supported:

- Calculate each rule independently.
- Give each logical reminder a stable identity.
- Prevent duplicate platform schedules for the same logical reminder.
- Reconcile all applicable reminders after a schedule item changes.
- Cancel obsolete reminders before or as part of the replacement operation.
- Avoid leaving a mixture of old and new reminders when a rescheduling operation partially fails.

The app must report scheduling failures honestly and attempt safe recovery.

### 6.4 Notification scheduling horizon

The app must not assume that the operating system can schedule an unlimited number of notifications.

Implement a configurable scheduling horizon if platform or product constraints require one.

For example, if the approved horizon covers only the next several days, future occurrences outside that window must be regenerated through a reliable reconciliation process.

The exact horizon must be selected after verifying platform limits and expected scheduling volume.

---

## 7. Notification Lifecycle

Every change to a schedule item must trigger an appropriate reminder reconciliation operation.

### 7.1 Creating a schedule item

1. Validate the schedule item and reminder rules.
2. Persist the schedule item.
3. Persist the reminder configuration.
4. Calculate applicable notification plans.
5. Schedule valid notifications.
6. Persist scheduling outcomes where required.
7. Surface actionable errors if scheduling fails.

Schedule persistence and notification scheduling are separate operations. A notification failure must not silently erase an otherwise valid schedule item.

### 7.2 Editing a schedule item

Changes to any of the following must trigger reconciliation:

- Start date or time.
- Time zone, when applicable.
- Recurrence rules.
- Reminder offsets.
- Reminder enabled state.
- Completion state, when completion affects reminders.
- Any other field that changes reminder eligibility.

Do not recreate reminders unnecessarily when an unrelated field changes.

### 7.3 Deleting a schedule item

- Cancel all pending notifications belonging to the item.
- Remove or deactivate associated reminder rules according to the approved data-retention policy.
- Remove obsolete scheduling records where appropriate.
- Ensure reconciliation cannot recreate reminders for a deleted item.

### 7.4 Completing an item

Apply the approved product rule consistently.

The default proposed behavior is to cancel future reminders for a completed one-time item. For recurring items, completing one occurrence must not cancel future occurrences unless the user completes or disables the entire series.

### 7.5 Restoring or undoing a deletion

If deletion recovery is supported:

- Restore the schedule data first.
- Recalculate reminder eligibility.
- Reschedule valid reminders.
- Do not blindly restore stale platform notification identifiers.

### 7.6 Idempotency

Repeated scheduling requests for the same logical reminder must not create duplicate notifications.

Use a deterministic logical identity based on the schedule item, occurrence, and reminder rule. Use a stable notification identifier or a controlled mapping from logical identity to platform identifier.

Do not use random identifiers on every reconciliation run.

---

## 8. Recurring Events

Recurring schedules require occurrence-aware reminder planning.

### 8.1 Occurrence identity

Each occurrence must have a deterministic key derived from the recurring schedule identity and the occurrence's logical date/time or another approved recurrence identifier.

Do not identify an occurrence only by its display title.

### 8.2 Recurrence calculations

- Use the approved recurrence engine as the source of occurrence generation.
- Calculate reminders separately for each applicable occurrence.
- Respect recurrence end dates, count limits, exceptions, and skipped occurrences.
- Apply modified occurrence times when the product supports editing individual occurrences.
- Avoid scheduling notifications for cancelled or excluded occurrences.
- Reconcile reminders when a series or an individual occurrence changes.

### 8.3 Exceptions

The notification scheduler must respect recurrence exceptions such as:

- A skipped occurrence.
- A cancelled occurrence.
- An occurrence moved to another date.
- An occurrence with a modified start time.
- A completed occurrence, where applicable.

Editing a single occurrence must not unintentionally modify the entire series.

### 8.4 Future occurrence planning

Do not attempt to create an unlimited number of platform notifications for a long-running recurring series.

Generate reminders within the supported scheduling horizon and refresh that horizon through app startup, schedule changes, and any approved background or lifecycle mechanisms.

The system must remain correct even when background refresh does not occur on time.

---

## 9. Date, Time Zone, and Daylight Saving Time

Time handling is a critical correctness requirement.

### 9.1 Time-zone semantics

Each schedule item must follow the time-zone policy defined by the approved scheduling model.

Distinguish between:

- An absolute instant.
- A local wall-clock time in a specified time zone.
- An all-day date without a time component.

Do not treat these concepts as interchangeable.

### 9.2 Time-zone changes

When the device's time zone changes:

- Re-evaluate schedule items whose semantics depend on local wall-clock time.
- Preserve absolute instants for schedules defined as fixed instants.
- Recalculate applicable notification plans.
- Avoid duplicate schedules during reconciliation.

The exact behavior must follow the schedule model's documented time-zone policy.

### 9.3 Daylight-saving transitions

Test both daylight-saving time transitions in supported time zones.

The system must define behavior for:

- Local times that do not exist because clocks move forward.
- Local times that occur twice because clocks move backward.
- Events scheduled near midnight.
- Recurrences crossing a time-zone offset change.

Use a documented resolution policy for nonexistent or ambiguous local times. Do not silently rely on inconsistent platform defaults.

### 9.4 All-day items

All-day schedule items must not accidentally receive a midnight notification unless explicitly configured.

All-day items should use date-based semantics and an approved default reminder time, if one exists.

### 9.5 Clock changes

If the device clock changes, reconciliation must recalculate affected reminders whenever the app can reliably detect the change or next runs.

Do not assume background execution will always be available immediately after a clock change.

### 9.6 Time representation

- Store absolute delivery instants in UTC where appropriate.
- Preserve the schedule's intended time zone when required by the domain model.
- Use a consistent date/time library approved for Flutter.
- Avoid manually parsing or formatting time-zone offsets.
- Never use display-formatted strings as the source of truth for scheduling calculations.

---

## 10. Permissions and User Consent

### 10.1 Permission strategy

Do not request notification permission automatically at first launch without explaining its value.

Request permission when the user enables reminders or performs a relevant action that requires notification capability.

Before the request, provide a concise explanation of the reminder behavior.

### 10.2 Permission states

The app must distinguish between:

- Permission not yet requested.
- Permission granted.
- Permission denied.
- Permission permanently denied or requiring system settings, where the platform exposes that distinction.
- Permission state unknown or temporarily unavailable.

Do not treat an unverified permission state as granted.

### 10.3 Permission denied

If permission is denied:

- Allow the user to continue using the scheduling features.
- Preserve the user's reminder configuration unless they explicitly disable it.
- Explain that operating-system notifications are unavailable.
- Provide a route to the appropriate system settings when supported.
- Re-check permission when the app resumes from the background.

Do not repeatedly prompt users who have already denied permission.

### 10.4 Android-specific requirements

Verify the selected notification package against the supported Android SDK range.

The implementation must account for:

- Android notification runtime permission requirements on supported versions.
- Notification channels.
- Channel importance and sound settings.
- Exact-alarm restrictions, if exact scheduling is required.
- Device reboot behavior.
- Battery optimization and background restrictions.
- Platform-specific notification scheduling limits.

Do not request exact-alarm privileges unless the product genuinely requires precision that ordinary scheduling cannot provide and the applicable platform policy permits it.

Do not claim that reminders are guaranteed to occur at an exact second.

### 10.5 iOS-specific requirements

Verify the selected package against the supported iOS versions.

The implementation must account for:

- Notification authorization.
- Alert, sound, and badge authorization where applicable.
- Changes made through iOS Settings.
- Notification presentation behavior while the app is in the foreground.
- Platform scheduling limits.
- Supported notification actions and categories.
- App lifecycle limitations.

Do not assume that iOS will execute arbitrary application code at every reminder time.

---

## 11. Notification Channels and Presentation

### 11.1 Android channels

Use a small, intentional set of channels based on meaningful user-facing categories.

A basic reminder channel may be sufficient for the initial release.

Each channel must have:

- A stable identifier.
- A clear user-facing name.
- A concise description.
- An appropriate importance level.
- Sound and vibration behavior consistent with the product's preferences.

Channel identifiers must not change unnecessarily between releases.

Android users may control channel behavior independently of app settings. The app must not falsely assume that changing an in-app preference overrides system channel settings.

### 11.2 iOS categories

Define notification categories only when the app supports meaningful actions, such as opening an item or marking a supported task complete.

Keep category identifiers stable and versioned when behavior changes require it.

### 11.3 Foreground behavior

When a notification fires while the app is open:

- Follow the approved platform presentation policy.
- Avoid duplicate in-app alerts and operating-system notifications for the same event.
- Respect user preferences.
- Keep the current screen and schedule state consistent.

### 11.4 Notification content

Notification content must be concise and useful.

Example:

**Title:** Upcoming reminder

**Body:** Your scheduled item starts in 15 minutes.

When showing the actual item title is approved, the notification may use a title such as:

**Title:** Schedule reminder

**Body:** Team meeting starts in 15 minutes.

Do not fabricate event titles, locations, participant names, or details.

Avoid placing sensitive schedule information in notifications by default when a privacy-preserving alternative is available.

### 11.5 Localization

All notification strings must use the approved localization system.

Do not hardcode user-facing English strings throughout the scheduling implementation.

Date and time formatting must follow the user's locale and the event's time-zone semantics.

---

## 12. Notification Actions and Navigation

Notification taps must route to the relevant schedule item or occurrence.

### 12.1 Payload requirements

Notification payloads should contain only the minimum identifiers needed to resolve the intended destination.

A payload may contain:

- Schedule item identifier.
- Occurrence identifier or occurrence key.
- Notification action identifier.
- Payload schema version.

Do not place passwords, access tokens, private account details, or unnecessary schedule contents in payloads.

### 12.2 Tap handling

When a user taps a notification:

1. Parse and validate the payload.
2. Resolve the referenced schedule item.
3. Verify that the item still exists and is accessible to the current user.
4. Resolve the relevant occurrence if the item is recurring.
5. Navigate to the correct destination.
6. Handle deleted, unavailable, or malformed references safely.

If the item no longer exists, show an appropriate fallback screen rather than crashing.

### 12.3 Action buttons

Only implement actions approved by product requirements.

If a notification supports marking an item complete:

- Verify that the item and occurrence still exist.
- Apply the same completion rules as the main app.
- Prevent duplicate completion side effects.
- Reconcile affected future reminders.
- Persist the action before reporting success.

Action handling must be safe when triggered more than once.

### 12.4 Authentication and authorization

If the app requires authentication, a notification tap must not bypass authentication or data-access checks.

If a referenced item is unavailable because the user signed out or lost access, require the appropriate authentication or display a safe fallback.

---

## 13. Notification Preferences

Preferences must be stored in the approved persistence layer and must survive ordinary app restarts.

### 13.1 Proposed preferences

- Global reminder enablement.
- Default reminder offset.
- Sound preference, subject to platform capabilities.
- Vibration preference, where supported.
- Quiet hours, if approved.
- Per-item reminder enablement.
- Optional notification preview privacy setting.

Do not introduce preferences that have no implemented behavior.

### 13.2 Preference precedence

Use a documented precedence model. The proposed order is:

1. Operating-system permission and restrictions.
2. Explicit per-item or per-reminder configuration.
3. User-level notification preferences.
4. Product defaults.

This order is conceptual and must be reconciled with the final product settings model before implementation.

### 13.3 Quiet hours

Quiet hours are an optional feature and must not be implemented until approved.

If enabled, define:

- Start and end times.
- Applicable time zone.
- Whether quiet hours cross midnight.
- Whether reminders are delayed, suppressed, or delivered silently.
- What happens when an event falls entirely within quiet hours.
- Whether urgent or explicitly exempt categories exist.

Do not silently shift reminders to another time without communicating the behavior.

---

## 14. Persistence and Reconciliation

### 14.1 Source of truth

The approved schedule repository and reminder configuration are the source of truth.

Operating-system notification queues are execution mechanisms, not the authoritative schedule database.

### 14.2 Reconciliation triggers

Reconciliation should run after:

- App startup.
- A schedule item is created, updated, or deleted.
- Reminder settings change.
- A recurring series or occurrence changes.
- Notification permission changes, where detectable.
- The app resumes from the background when a permission or schedule refresh is appropriate.
- A device restart, where supported and necessary.
- A time-zone or clock change, where detectable and relevant.
- A recoverable scheduling failure.

Avoid performing an expensive full reconciliation on every ordinary UI rebuild.

### 14.3 Reconciliation algorithm

A reconciliation operation should:

1. Load eligible schedule items and reminder rules.
2. Calculate expected reminder plans within the supported horizon.
3. Compare expected plans with the app's persisted scheduling records.
4. Cancel obsolete or invalid platform notifications.
5. Schedule missing or changed notifications.
6. Persist the resulting states.
7. Record actionable errors without exposing sensitive data.
8. Remain safe if interrupted and executed again.

### 14.4 Partial failure

If cancellation succeeds but replacement scheduling fails:

- Keep the schedule item intact.
- Record the scheduling failure.
- Attempt recovery through a later reconciliation.
- Avoid marking the replacement as successfully scheduled.
- Inform the user when the failure materially affects reminders.

Use transaction boundaries and retry logic appropriate to the persistence architecture.

Do not claim that database writes and operating-system scheduling form a single atomic transaction.

### 14.5 Device reboot

Verify whether the chosen notification package and operating system retain scheduled notifications across device restarts.

If explicit rescheduling is required, implement the appropriate platform integration and verify its compatibility with the approved architecture.

Do not promise reboot recovery until it has been tested on supported devices.

---

## 15. Reliability and Delivery Guarantees

The system must distinguish among three separate outcomes:

1. The app calculated a reminder.
2. The app submitted a notification schedule request successfully.
3. The operating system presented the notification to the user.

The first two can be tracked by the app under appropriate conditions. The third may not be observable or guaranteed.

### Required behavior

- Use idempotent scheduling operations.
- Persist sufficient information for reconciliation.
- Recover from transient failures where safe.
- Avoid uncontrolled retry loops.
- Avoid duplicate notifications during retries.
- Never fabricate delivery confirmation.
- Respect platform scheduling limits.
- Communicate material failures in understandable language.
- Keep scheduling logic independent from UI rendering.

### Important limitation

Local notifications are best-effort operating-system services. Doze mode, battery restrictions, permission changes, device shutdown, platform limits, and other conditions may delay or prevent delivery.

If the product eventually requires server-originated delivery, multi-device synchronization, or stronger delivery observability, define a separate remote notification architecture. Do not describe local notifications as guaranteed delivery.

---

## 16. Background Execution

Background execution must follow the limitations of each mobile platform.

Do not assume the app can run continuously in the background.

The implementation must:

- Use native scheduled notification capabilities where appropriate.
- Avoid depending on periodic background execution to deliver every reminder.
- Use background tasks only for supported maintenance or reconciliation work.
- Avoid long-running background processes without a justified and approved requirement.
- Handle app termination and device restart according to verified platform behavior.
- Ensure that opening the app can repair stale reminder schedules.

Any background execution dependency must be documented and tested on real devices.

---

## 17. Security and Privacy

Notifications can reveal information on a lock screen. Treat their content and payloads as potentially visible outside the authenticated app.

### Requirements

- Do not include credentials, authentication tokens, or secrets in notification content or payloads.
- Minimize sensitive data in notification titles and bodies.
- Validate notification payloads before navigation or action execution.
- Re-check access permissions when opening a referenced schedule item.
- Do not expose another user's schedule data through notification identifiers.
- Do not trust payload values as proof of authorization.
- Avoid logging full notification content when it could expose private information.
- Do not send schedule data to a remote notification provider unless that behavior is approved and documented.
- Follow the approved data-retention and account-deletion policies.

Notification IDs must not be used as authentication credentials.

---

## 18. Error Handling and Diagnostics

### 18.1 Expected error cases

The system must handle:

- Permission denied.
- Permission revoked after scheduling.
- Invalid schedule time.
- Invalid reminder offset.
- Unsupported scheduling precision.
- Platform scheduling failure.
- Notification cancellation failure.
- Missing or deleted schedule items.
- Malformed notification payloads.
- Duplicate action requests.
- Database or persistence failure.
- Time-zone calculation failure.
- Device restart or lifecycle-related reconciliation failure.
- Platform notification limit constraints.

### 18.2 Error handling rules

- Do not crash the app because a reminder cannot be scheduled.
- Do not silently discard meaningful failures.
- Do not display raw platform exceptions to users.
- Provide an actionable message where appropriate.
- Keep internal diagnostics useful but privacy-safe.
- Retry only operations that are safe to retry.
- Record enough diagnostic information to investigate persistent failures.

### 18.3 Suggested user messages

Use clear, non-alarmist wording.

- “Notifications are turned off. Enable them in your device settings to receive reminders.”
- “This reminder could not be scheduled. Your schedule item is still saved.”
- “Your reminder settings have changed. Open the app to refresh your reminders.”

Only display these messages when the corresponding condition is actually known.

---

## 19. Performance and Resource Management

- Schedule only eligible notifications within the supported horizon.
- Avoid unnecessary full-queue cancellation and recreation.
- Batch calculations and persistence operations where appropriate.
- Avoid blocking the Flutter UI isolate with expensive recurrence calculations.
- Do not start redundant reconciliation jobs.
- Use deterministic identifiers and efficient lookups.
- Avoid excessive background work.
- Profile large recurring schedules and bulk edits.
- Keep logs bounded and avoid storing unneeded notification history.

Performance optimization must not compromise correctness or make scheduling behavior unpredictable.

---

## 20. Accessibility and User Experience

- Notification text must be understandable without relying on color or icons.
- Notification actions must have clear labels.
- Important settings must be accessible through the normal app settings flow.
- Permission denial must not block core scheduling functionality.
- Error messages must describe the issue and a practical next step.
- Notification text must support localization and appropriate text expansion.
- Sound and vibration must follow platform conventions and user preferences.
- Avoid unnecessary interruptions and duplicate reminders.

Use standard platform notification presentation wherever possible.

---

## 21. Testing Requirements

Notification functionality must be tested at multiple layers.

### 21.1 Unit tests

Test:

- Reminder-time calculations.
- Multiple reminder offsets.
- Invalid offsets.
- Past reminders.
- Recurrence occurrence generation.
- Recurrence exceptions.
- Time-zone changes.
- Daylight-saving transitions.
- All-day schedule behavior.
- Stable notification ID generation.
- Notification payload validation.
- Eligibility of completed, deleted, and cancelled items.
- Quiet-hours calculations if implemented.
- Reconciliation plan generation.
- Duplicate scheduling prevention.

Use an injectable clock and controllable time-zone dependencies. Do not make core unit tests depend on the actual current time.

### 21.2 Service tests

Use a fake notification platform adapter to test:

- Scheduling.
- Cancellation.
- Rescheduling.
- Partial failures.
- Retry behavior.
- Duplicate requests.
- Permission-state handling.
- Persistence failures.
- Reconciliation after stale or missing records.

Verify both successful operations and failure paths.

### 21.3 Widget tests

Test:

- Notification permission explanations.
- Permission-denied UI.
- Reminder configuration controls.
- Reminder enablement and disablement.
- Settings persistence.
- Scheduling error messages.
- Notification-related navigation states.

### 21.4 Integration tests

Verify on supported Android and iOS devices:

- Permission request flows.
- Actual notification scheduling.
- Notification presentation.
- Notification taps and navigation.
- Supported action buttons.
- Editing and deleting schedule items.
- App termination and reopening.
- Reboot behavior where required.
- Permission revocation and restoration.
- Time-zone and clock-change handling where feasible.

Simulator results alone are insufficient for validating platform-specific timing, power management, and reboot behavior.

### 21.5 Essential regression scenarios

At minimum, test these cases:

1. Create an item with a reminder.
2. Edit the start time and verify the old reminder is replaced.
3. Change the reminder offset.
4. Delete the item and verify its reminder is cancelled.
5. Complete one occurrence of a recurring item.
6. Skip or move a recurring occurrence.
7. Deny notification permission.
8. Revoke permission after reminders have been scheduled.
9. Restart the device.
10. Open a notification for an item that no longer exists.
11. Trigger the same action more than once.
12. Change the device time zone.
13. Exercise a daylight-saving transition.
14. Simulate a platform scheduling failure.
15. Run reconciliation repeatedly and verify no duplicates are created.

---

## 22. Definition of Done

A notification feature is complete only when all applicable criteria are satisfied.

- [ ] Behavior matches approved product requirements.
- [ ] Reminder calculations use the approved scheduling and time-zone model.
- [ ] Notification permissions are handled correctly.
- [ ] Notification identifiers are stable and collision-resistant.
- [ ] Scheduling and cancellation are idempotent.
- [ ] Edits, deletion, completion, and recurrence exceptions are handled correctly.
- [ ] Reconciliation repairs recoverable scheduling inconsistencies.
- [ ] Failure states are recorded and communicated appropriately.
- [ ] Notification payloads are validated.
- [ ] Privacy and authorization requirements are satisfied.
- [ ] Unit tests cover calculations and edge cases.
- [ ] Service tests cover success, failure, and retry behavior.
- [ ] Relevant widget and integration tests pass.
- [ ] Supported Android and iOS behavior has been verified.
- [ ] Localization and accessibility requirements are met.
- [ ] Documentation is updated.
- [ ] No unsupported delivery guarantees are claimed.

---

## 23. Implementation Workflow

When implementing or modifying notification functionality:

1. Read `AGENTS.md`, `master_rules.md`, `coding-standards.md`, `database-schema.md`, `design.md`, and the approved scheduling requirements.
2. Inspect the existing schedule model, recurrence logic, persistence layer, and navigation architecture.
3. Confirm platform targets and the approved notification package.
4. Identify the affected reminder lifecycle and failure cases.
5. Define or update the relevant interfaces and data models.
6. Implement pure scheduling calculations before platform integration.
7. Add tests for calculations and recurrence edge cases.
8. Implement the platform adapter and permission flow.
9. Implement reconciliation and recovery behavior.
10. Verify behavior on supported Android and iOS configurations.
11. Update documentation and platform configuration.
12. Report what changed, which tests passed, which tests were not run, and any remaining limitations.

Do not replace the existing notification architecture or add new dependencies without first inspecting the repository and confirming the need.

---

## 24. Open Decisions

The following decisions require confirmation before their associated features are treated as finalized:

- Minimum supported Android and iOS versions.
- Approved notification package and version.
- Whether reminder offsets are configurable or selected from predefined values.
- Maximum number of reminders per schedule item.
- Maximum supported scheduling horizon.
- Whether exact scheduling is a genuine product requirement.
- Default behavior for completed recurring occurrences.
- Whether quiet hours are included in the initial release.
- Whether all-day items can have reminders and at what default time.
- Whether notification actions can complete or snooze items.
- Whether notification content may reveal schedule titles on the lock screen.
- Whether local notifications are sufficient for the first release.
- Whether remote push notifications are required in a future phase.
- Whether notification scheduling records belong in the local database, remote database, or both.

Until these decisions are approved, implement only the behavior necessary for the confirmed product scope and keep unresolved choices isolated behind configuration or interfaces.

---

## 25. Final Rule

The notification system must prioritize correctness, recoverability, privacy, and honest status reporting.

A saved schedule item is not the same as a successfully scheduled notification, and a scheduled notification is not proof of delivery.

Every reminder must be derived from authoritative schedule data, use the correct time semantics, respect user permissions, and remain safe to reconcile repeatedly.

Never sacrifice schedule integrity to make a notification appear successful.
