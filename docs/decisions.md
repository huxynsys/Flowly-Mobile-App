# Flowly Decisions

Date: 2026-10-28

This document is the highest authority for Flowly product scope and architecture. Every other document must defer to these decisions. If another document conflicts with a decision here, this document wins. TBD values are unresolved and must be listed in `OPEN_QUESTIONS.md`; do not infer an answer.

## Product and scope

### D-001: MVP boundary
- **Date:** 2026-10-28
- **Status:** Accepted
- **Decision:** The MVP includes email/password authentication, onboarding, scheduling preferences, Tasks, Events, categories, separate Today, Schedule, and Calendar destinations, deterministic daily plan generation, daily plans, Wake Events and replanning, plan version acceptance or keeping the current version, undo of the latest accepted replan by restoring the previous active version, manual Replan My Day, schedule locking, scheduled local notifications, AI daily plan generation and replanning, accessibility baseline, and error and empty states. Anything not included is post-MVP. The explicit post-MVP scope is listed in D-026.
- **Consequences:** Implementation and tickets must distinguish MVP from post-MVP. The explicit AI, monthly recurrence, and OS-level local notification decisions below are part of this MVP boundary. Do not introduce any other feature into MVP without an accepted decision update.

### D-002: Product identity and platforms
- **Date:** 2026-10-28
- **Status:** Accepted
- **Decision:** The product name is Flowly. The client targets Android and iOS and is built with Flutter and Dart.
- **Consequences:** Use Flowly consistently in documentation and product-facing identifiers. Build and test the mobile client for both target platforms. Minimum OS versions are TBD.

### D-003: Backend and client architecture
- **Date:** 2026-10-28
- **Status:** Accepted
- **Decision:** Use Supabase Auth with email/password, Supabase-hosted PostgreSQL, PostgreSQL Row Level Security (RLS) on every user-owned table, authenticated Supabase Edge Functions written in Deno/TypeScript, and versioned Supabase CLI migrations. Keep privileged scheduling, AI calls, and authoritative plan validation server-side. The Flutter client uses authenticated Supabase access for ordinary data access under RLS.
- **Consequences:** Never trust client-supplied user IDs. Every Edge Function must authenticate its caller and preserve user authorization context. Schema changes use Supabase CLI migrations. Do not specify a separate backend or web client.

### D-004: Repository layout
- **Date:** 2026-10-28
- **Status:** Accepted
- **Decision:** Use Flutter/Dart for the client and TypeScript/Deno for Supabase Edge Functions. Use this logical layout:

```text
lib/                              # Flutter application source
├── app/                          # App entry point, router, theme
├── core/                         # Shared errors, time, validation, widgets
│   └── widgets/                  # Shared accessible UI primitives
├── features/                     # Auth, onboarding, Today, schedule, tasks
├── domain/                       # Client-side domain models and interfaces
└── data/                          # Client repositories and data sources
supabase/
├── migrations/                   # Versioned Supabase CLI migrations
└── functions/
    ├── _shared/scheduler/        # Deterministic scheduler and validator
    └── <function-name>/          # Authenticated TypeScript/Deno functions
test/                             # Dart unit and widget tests
integration_test/                 # Flutter integration tests
```

- **Consequences:** Client presentation, domain behavior, data access, privileged server operations, and migrations remain separated. Do not place authoritative scheduling rules exclusively in Flutter widgets.

### D-005: Authentication and ownership
- **Date:** 2026-10-28
- **Status:** Superseded by D-037
- **Decision:** Use Supabase Auth with email/password. PostgreSQL RLS policies enforce ownership on every user-owned table. Edge Functions authenticate requests and preserve the caller's authorization context.
- **Consequences:** Clients cannot access another user's data by changing identifiers. Do not add social sign-in or guest account behavior to MVP unless a later decision accepts it.

## Scheduling model

### D-006: Time storage and user timezone
- **Date:** 2026-10-28
- **Status:** Superseded by D-027
- **Decision:** Store timestamps representing instants in UTC. Each user has an IANA timezone used to interpret and schedule local dates and times, for example `Asia/Karachi`. The default IANA timezone value when one has not been selected is TBD.
- **Consequences:** Convert instants using the user's IANA timezone for display and calendar calculations. Do not use the device timezone as a substitute for the user's saved timezone. Recurrence retains its defined local-time semantics. Resolve the default IANA timezone in `OPEN_QUESTIONS.md`.

### D-007: Planning day boundary
- **Date:** 2026-10-28
- **Status:** Accepted
- **Decision:** A user's planning day starts at 06:00 in that user's IANA timezone. Activities crossing this boundary belong to the planning day on which they start. Sleep is not a Plan Block; sleep preferences are scheduling constraints.
- **Consequences:** Plan generation, Today views, and day-boundary calculations use the local 06:00 boundary, not midnight UTC.

### D-008: Scheduling granularity and transition buffer
- **Date:** 2026-10-28
- **Status:** Accepted
- **Decision:** Scheduling uses 15-minute granularity and a default 10-minute transition buffer between Plan Blocks. Travel time is post-MVP.
- **Consequences:** Scheduler-generated start and end times align to the accepted granularity. The transition buffer is applied between eligible consecutive Plan Blocks. Do not interpret it as travel time.

### D-009: Deterministic scheduler
- **Date:** 2026-10-28
- **Status:** Accepted
- **Decision:** The deterministic scheduler must produce the same output for the same input. Break ties by higher score, earlier deadline, earlier creation time, then Task ID in lexicographic order. The score formula and weights are TBD.
- **Consequences:** Keep scheduler inputs and tie-breaking stable and testable. Do not invent scoring weights; record them in `OPEN_QUESTIONS.md` before implementation depends on them.

### D-010: Plan item states
- **Date:** 2026-10-28
- **Status:** Accepted
- **Decision:** Plan items use exactly these states: `planned`, `in_progress`, `completed`, `skipped`, `missed`, `protected`, and `superseded`. `protected` identifies a fixed or locked Plan Block. `superseded` identifies an item in a prior Plan Version.
- **Consequences:** Persisted values, APIs, tests, and UI mappings must use these exact state names. Do not add alternate state names without an accepted decision update.

### D-011: Replanning treatment of past and active Plan Blocks
- **Date:** 2026-10-28
- **Status:** Accepted
- **Decision:** When replanning, a Plan Block whose end time is earlier than the current time becomes `missed` if it is not `completed`. A Plan Block currently in progress stays in place until its end unless the user explicitly skips it.
- **Consequences:** Replanning preserves work already underway and records elapsed unfinished work rather than silently rescheduling it.

### D-012: Fixed commitments and schedule locking
- **Date:** 2026-10-28
- **Status:** Accepted
- **Decision:** Fixed Events are hard scheduling constraints. Users may lock schedule commitments; a locked Plan Block is protected from movement by plan generation or replanning. The user chooses which eligible commitments to protect.
- **Consequences:** The scheduler and validator must not move protected commitments. Replanning must retain and report protected Plan Blocks.

## Recurrence

### D-013: MVP recurrence patterns
- **Date:** 2026-10-28
- **Status:** Accepted
- **Decision:** MVP recurrence supports daily, weekly on selected weekdays, and monthly patterns for recurring Tasks. Recurring fixed Events and yearly recurrence are post-MVP.
- **Consequences:** Do not implement custom intervals or yearly recurrence in MVP. Monthly edge behavior for dates that do not exist in every month is TBD.

### D-014: Occurrence identity and exceptions
- **Date:** 2026-10-28
- **Status:** Accepted
- **Decision:** Every recurring series instance is an Occurrence with stable identity independent of its mutable scheduled start time. Store Occurrence exceptions so one Occurrence can be skipped, moved, or modified without silently changing the whole series.
- **Consequences:** A moved Occurrence retains its original identity. Recurrence storage and APIs must distinguish the series from individual Occurrences and their Exceptions. Persistence strategy and detailed time-zone edge behavior are TBD.

### D-015: Recurrence edit scope
- **Date:** 2026-10-28
- **Status:** Accepted
- **Decision:** When editing a recurring Task, the user must be able to choose exactly one scope: this Occurrence; this Occurrence and future Occurrences; or all Occurrences in the series.
- **Consequences:** Apply the selected scope consistently to recurrence rules, Occurrences, Exceptions, and reminders. Do not silently broaden a one-Occurrence edit to the series.

## AI

### D-016: AI daily plan generation and replanning
- **Date:** 2026-10-28
- **Status:** Accepted
- **Decision:** AI is in the MVP only for daily plan generation and Replan My Day. AI requests go only through an authenticated server-side Supabase Edge Function that holds the OpenAI API key. The Flutter client never calls OpenAI directly. AI output must be validated against a schema before it is shown to the user. Token caps and rate limits apply; their numerical values are TBD. The server-side deterministic validator remains authoritative for plan validity and persistence.
- **Consequences:** Never include the OpenAI key in the client, client configuration, logs, or source control. Invalid or schema-nonconforming output is not shown as a plan. Apply documented token and rate limits once their values are approved. AI explanations are post-MVP.

### D-017: AI provider secret
- **Date:** 2026-10-28
- **Status:** Accepted
- **Decision:** Use the OpenAI API for the MVP AI feature. Store `OPENAI_API_KEY` only as a Supabase Edge Function secret.
- **Consequences:** The client calls the Flowly Edge Function, not OpenAI. Do not request, commit, or expose a real key in source files or documentation examples.

## Notifications

### D-018: OS-level scheduled local notifications
- **Date:** 2026-10-28
- **Status:** Accepted
- **Decision:** Scheduled local notifications are required in MVP and must be able to fire while Flowly is closed, subject to operating-system permissions and restrictions. Remote push via FCM/APNs is post-MVP. Each Android notification must use an appropriate Android notification channel. On iOS, request notification permission in context and explain why before the system permission prompt. Reconcile and reschedule pending local notifications after device reboot where the platform permits it. If permission is denied, keep Flowly usable, do not claim a reminder is scheduled, and provide a route to system settings where available; do not repeatedly prompt after denial unless the user initiates the action.
- **Consequences:** Notification behavior is implemented through OS-level local scheduling, not merely in-app banners or a server worker. Keep scheduled notifications synchronized with edits, completion, deletion, recurrence changes, and timezone changes. Do not make delivery guarantees beyond operating-system support. Android channel names/importance, package choice, reboot implementation, and exact permission prompt behavior by OS version are TBD.

### D-019: Notification timing and scope
- **Date:** 2026-10-28
- **Status:** Accepted
- **Decision:** MVP reminders are local notifications derived from the user's saved schedule and reminder preferences, with multiple user-selectable reminder offsets. Quiet hours, remote push, email, and SMS reminders are not MVP features. Offset values are TBD.
- **Consequences:** Do not add remote notification infrastructure or quiet hours to MVP. Cancel pending reminders when a Task is completed as defined in D-045, and never show a success state for a reminder that the OS did not accept for scheduling.

## User experience and release

### D-020: Product language
- **Date:** 2026-10-28
- **Status:** Accepted
- **Decision:** Do not use AI branding in the UI. User-facing copy must use guilt-free wording and must not blame or shame users.
- **Consequences:** Apply this language rule to screens, errors, dialogs, and notifications. Use Flowly as the product name.

### D-021: User-owned data and security
- **Date:** 2026-10-28
- **Status:** Accepted
- **Decision:** Every user-owned PostgreSQL table has RLS ownership policies. Never trust client-supplied ownership identifiers. Never put privileged credentials in the Flutter app.
- **Consequences:** Test RLS and Edge Function authorization. Keep service-role credentials server-side only.

### D-022: Deployment targets
- **Date:** 2026-10-28
- **Status:** Accepted
- **Decision:** Build Flowly for Android and iOS with Flutter and use Supabase hosted services. Store-publishing accounts and release procedures must be confirmed before launch.
- **Consequences:** Build and release processes must support the accepted mobile targets. Store account ownership, CI provider, and release process details remain TBD.

## Superseded decisions

### D-023: Previous AI scope
- **Date:** 2026-10-28
- **Status:** Superseded
- **Decision:** The earlier proposal treated AI as generally available for proposing, explaining, or interpreting natural language and limited AI explanations to post-MVP without defining MVP daily plan generation. It is superseded by D-001, D-016, and D-017.
- **Consequences:** Only daily plan generation and Replan My Day use AI in MVP. AI explanations and natural-language task input remain post-MVP.

### D-024: Previous recurrence scope
- **Date:** 2026-10-28
- **Status:** Superseded
- **Decision:** The earlier MVP boundary allowed only simple daily/weekly recurrence and excluded other recurrence patterns. It is superseded by D-013, D-014, and D-015.
- **Consequences:** Monthly recurrence for Tasks is in MVP, with stable Occurrence identity, Exceptions, and all three edit scopes. Yearly recurrence and recurring fixed Events remain post-MVP.

### D-025: Previous notification scope
- **Date:** 2026-10-28
- **Status:** Superseded
- **Decision:** The earlier MVP boundary specified basic in-app reminders and did not require OS-level scheduled notifications. It is superseded by D-018 and D-019.
- **Consequences:** Scheduled local notifications are required in MVP; remote push is post-MVP.

### D-026: Post-MVP feature scope
- **Date:** 2026-10-28
- **Status:** Post-MVP
- **Decision:** AI explanations, natural-language Task input, projects, goals, insights, calendar integrations, recurring fixed Events, yearly recurrence, Task splitting, energy-aware planning, travel time, personalization, and remote push notifications are outside the MVP.
- **Consequences:** Do not implement or include these features in MVP acceptance criteria unless a later accepted decision changes their status.

## Decisions from answered open questions

### D-027: User timezone initialization (OQ-001)
- **Date:** 2026-10-28
- **Status:** Accepted
- **Decision:** Store instants in UTC and initialize each user's IANA timezone from the device's current IANA timezone during account setup, superseding D-006's previous default-timezone rule.
- **Consequences:** Use the initialized, saved IANA timezone to interpret and schedule local dates and times.

### D-028: Scheduler priority rules (OQ-002)
- **Date:** 2026-10-28
- **Status:** Accepted
- **Decision:** Use an explicitly approved ordered set of scheduler priority rules without weighted factors; the rules remain TBD.
- **Consequences:** Do not implement weighted scheduler factors until the missing scoring rules are approved.

### D-029: Task deadline semantics (OQ-003)
- **Date:** 2026-10-28
- **Status:** Accepted
- **Decision:** A Task deadline is an exact instant stored and evaluated in UTC and displayed in the user's IANA timezone.
- **Consequences:** Store and compare Task deadlines as UTC instants.

### D-030: All-day Event support (OQ-004)
- **Date:** 2026-10-28
- **Status:** Accepted
- **Decision:** MVP supports timed Events only; all-day Events are not supported.
- **Consequences:** Do not expose all-day Event creation in MVP.

### D-031: MVP categories (OQ-005)
- **Date:** 2026-10-28
- **Status:** Accepted
- **Decision:** Categories are included in MVP, and deleting a category leaves associated items without a category.
- **Consequences:** Include categories in MVP forms and schema, and preserve associated items when a category is deleted.

### D-032: Task and Event deletion (OQ-006)
- **Date:** 2026-10-28
- **Status:** Accepted
- **Decision:** Delete Tasks and Events by soft deletion and provide an undo or restore path.
- **Consequences:** Keep soft-deleted Tasks and Events recoverable through the undo or restore path.

### D-033: Monthly recurrence date fallback (OQ-007)
- **Date:** 2026-10-28
- **Status:** Accepted
- **Decision:** If a monthly recurrence date does not exist in a month, use that month's last calendar day for the Occurrence.
- **Consequences:** Generate the monthly Occurrence on the month's last calendar day when its nominal date is absent.

### D-034: Daylight-saving recurrence behavior (OQ-008)
- **Date:** 2026-10-28
- **Status:** Accepted
- **Decision:** Resolve a nonexistent recurrence local time to the next valid time and resolve a repeated local time to its earlier instant.
- **Consequences:** Apply this resolution consistently when generating recurrence Occurrences.

### D-035: Recurrence end conditions (OQ-009)
- **Date:** 2026-10-28
- **Status:** Accepted
- **Decision:** MVP recurrence supports an end date and a maximum occurrence count.
- **Consequences:** Recurring Tasks must allow either configured end condition as defined by the recurrence rule.

### D-036: Occurrence generation strategy (OQ-010)
- **Date:** 2026-10-28
- **Status:** Accepted
- **Decision:** Calculate Occurrences on demand for requested date ranges and persist Exceptions and per-Occurrence state.
- **Consequences:** Do not materialize the complete recurrence series as stored Occurrence records.

### D-037: Guest schedule access (OQ-011)
- **Date:** 2026-10-28
- **Status:** Accepted
- **Decision:** Allow a local guest schedule without authentication and require Supabase email/password authentication only for cloud persistence or synchronization, superseding D-005's restriction on guest behavior.
- **Consequences:** Keep guest scheduling local and require authentication before cloud persistence or synchronization.

### D-038: Calendar destination (OQ-012)
- **Date:** 2026-10-28
- **Status:** Accepted
- **Decision:** Include a separate Calendar destination in MVP.
- **Consequences:** Provide a distinct Calendar navigation destination in MVP.

### D-039: Completion scope (OQ-013)
- **Date:** 2026-10-28
- **Status:** Accepted
- **Decision:** Only Tasks can be marked completed; Events remain commitments.
- **Consequences:** Do not provide Event completion actions.

### D-040: Task priority scale (OQ-014)
- **Date:** 2026-10-28
- **Status:** Accepted
- **Decision:** Use a fixed ordered Task priority scale with a documented default; the scale and default remain TBD.
- **Consequences:** Do not assign priority values or a default until they are approved.

### D-041: AI usage limits (OQ-015)
- **Date:** 2026-10-28
- **Status:** Accepted
- **Decision:** Apply per-user token budgets and request limits over a defined time window; numerical values remain TBD.
- **Consequences:** Enforce the approved per-user limits after their values are defined.

### D-042: AI response schema (OQ-016)
- **Date:** 2026-10-28
- **Status:** Accepted
- **Decision:** Validate AI output as a structured set of proposed changes to the current Plan Version.
- **Consequences:** Do not show proposed changes until they pass schema validation.

### D-043: Reminder multiplicity (OQ-017)
- **Date:** 2026-10-28
- **Status:** Accepted
- **Decision:** Support multiple user-selectable reminder offsets in MVP, updating D-019.
- **Consequences:** Allow users to select multiple offsets without assuming specific offset values.

### D-044: Quiet hours (OQ-018)
- **Date:** 2026-10-28
- **Status:** Accepted
- **Decision:** Quiet hours are excluded from MVP.
- **Consequences:** Do not include quiet-hours configuration or behavior in MVP.

### D-045: Reminder cancellation on Task completion (OQ-019)
- **Date:** 2026-10-28
- **Status:** Accepted
- **Decision:** Cancel all pending reminders for a completed Task and its Occurrences.
- **Consequences:** Remove pending local notifications for the completed Task and its Occurrences.

### D-046: Android notification channel and package selection (OQ-020)
- **Date:** 2026-10-28
- **Status:** Accepted
- **Decision:** Use one user-visible reminders channel and select the local-notification package after Android and iOS compatibility review; the package name remains TBD.
- **Consequences:** Do not adopt a notification package until compatibility review is complete.

### D-047: Notification reboot recovery (OQ-021)
- **Date:** 2026-10-28
- **Status:** Accepted
- **Decision:** Use app-owned platform integration for notification reboot recovery where the selected package does not provide it.
- **Consequences:** Ensure pending local notifications can be reconciled and rescheduled after reboot where the platform permits it.

### D-048: Minimum operating-system versions (OQ-022)
- **Date:** 2026-10-28
- **Status:** Accepted
- **Decision:** Set separate minimum supported versions for Android and iOS; the version values remain TBD.
- **Consequences:** Do not declare platform support below the approved minimum versions.

### D-049: Account retention (OQ-023)
- **Date:** 2026-10-28
- **Status:** Accepted
- **Decision:** Disable an account and retain its user-owned data for a defined period before deletion; the retention period remains TBD.
- **Consequences:** Do not permanently delete account data until the approved retention period has elapsed.

### D-050: MVP localization (OQ-024)
- **Date:** 2026-10-28
- **Status:** Accepted
- **Decision:** Ship MVP in English only with localization-ready strings.
- **Consequences:** Keep user-facing strings suitable for localization without adding additional MVP languages.

### D-051: Flutter and Dart version policy (OQ-025)
- **Date:** 2026-10-28
- **Status:** Accepted
- **Decision:** Pin exact Flutter and Dart versions in repository tooling; the versions remain TBD.
- **Consequences:** Do not pin or assume version numbers until they are approved.

### D-052: State management and navigation packages (OQ-026)
- **Date:** 2026-10-28
- **Status:** Accepted
- **Decision:** Use Riverpod for state management and GoRouter for navigation.
- **Consequences:** Use these packages in the Flutter client.

### D-053: CI provider (OQ-027)
- **Date:** 2026-10-28
- **Status:** Accepted
- **Decision:** Use a hosted CI provider and require build, analysis, test, and migration checks; the provider remains TBD.
- **Consequences:** Do not configure a CI provider until its selection is approved.

### D-054: Coverage and performance thresholds (OQ-028)
- **Date:** 2026-10-28
- **Status:** Accepted
- **Decision:** Set explicit numeric coverage and performance thresholds; the threshold values remain TBD.
- **Consequences:** Do not enforce numeric coverage or performance gates until their values are approved.

### D-055: Schedule item ordering (OQ-029)
- **Date:** 2026-10-28
- **Status:** Accepted
- **Decision:** Order scheduled items by start time and then stable ID, and show unscheduled Tasks separately ordered by deadline and priority.
- **Consequences:** Apply this ordering consistently to Today and schedule item lists.

## Values still required

- Scoring rules: TBD (OQ-002)
- Priority scale and default: TBD (OQ-014)
- Token caps and rate limits: TBD (OQ-015)
- Minimum Android and iOS versions: TBD (OQ-022)
- Account retention period: TBD (OQ-023)
- Notification package name: TBD (OQ-020)
- Flutter and Dart versions: TBD (OQ-025)
- CI provider: TBD (OQ-027)
- Coverage and performance thresholds: TBD (OQ-028)
