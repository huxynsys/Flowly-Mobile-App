# Flowlly — Complete Feature Ticket List

> **Relationship to other documents:** `decisions.md` is authoritative for MVP scope and architecture. Ticket priorities must not expand that scope.

**Product:** Flowlly
**Product Type:** Adaptive Life Scheduling & Planning Application
**Document Type:** Feature Ticket List
**Version:** 1.0
**Status:** Development Build Checklist

---

# 1. How to Use This Document

This document converts the Flowlly PRD and TRD into small, independently buildable engineering tickets.

Each ticket contains:

* **Feature Name**
* **Task Description**
* **Acceptance Criteria**
* **Dependencies**
* **Priority**
* **AI Coding Prompt**

Each ticket should be treated as one focused implementation task.

Do not implement multiple unrelated tickets in a single AI coding prompt unless the tickets are explicitly marked as tightly coupled.

---

# 2. Priority Definitions

## Must-Have

Required for the MVP launch.

Without these features, Flowlly's core adaptive scheduling experience is incomplete.

## Should-Have

Important for a strong product but can be implemented after the MVP core works.

## Nice-to-Have

Useful enhancements that should not delay the first functional release.

---

# 3. Recommended Build Order

```text
01. Project Foundation
02. Design System
03. Database
04. Authentication
05. Onboarding
06. User Preferences
07. Tasks
08. Calendar Events
09. Availability
10. Basic Scheduling Engine
11. Daily Plan
12. Today Screen
13. Task Execution
14. I'm Awake
15. Adaptive Replanning
16. Plan Versions
17. AI Planning Layer
18. Notifications
19. Projects
20. Goals
21. Insights
22. Integrations
23. Advanced Personalization
24. Production Hardening
```

---

# 4. EPIC A — Project Foundation

---

## FT-001 — Initialize Flowlly Application

### Feature Name

Project Foundation

### Task Description

Create the initial Flowlly application using the approved production technology stack.

Set up the application with Flutter/Dart, Riverpod, GoRouter, Supabase Auth, Supabase PostgreSQL, RLS, Supabase CLI migrations, and TypeScript/Deno Edge Functions. Use the repository structure defined in `decisions.md`.

### Acceptance Criteria

* Application starts successfully in development mode.
* Dart analysis, formatting, and Flutter tests pass.
* Supabase Auth and the local Supabase development workflow are configured.
* RLS policies and database migrations are documented and testable.
* Documentation specifies that `OPENAI_API_KEY` belongs only in Supabase Edge Function secrets; no real key is needed during bootstrap.
* Source code follows the planned architecture.
* No secrets are committed to the repository.

### Dependencies

None.

### Priority

**Must-Have**

### AI Coding Prompt

> Initialize Flowlly using the Flutter/Dart and Supabase architecture in `decisions.md`. Configure Riverpod, GoRouter, Supabase Auth, PostgreSQL RLS, Supabase CLI migrations, and TypeScript/Deno Edge Functions using the canonical repository layout. Document that `OPENAI_API_KEY` must be provisioned only as a Supabase Edge Function secret; do not request or commit a real key. Do not implement business features yet. Verify Flutter analysis, formatting, tests, and local Supabase startup.

---

# 5. EPIC B — Design System

---

## FT-002 — Establish Flowlly Design Tokens

### Feature Name

Flowlly Design System

### Task Description

Create centralized design tokens from `design.md`. Any referenced Google Stitch screens are unverified until their existence and availability are confirmed.

Define tokens for:

* Colors
* Typography
* Spacing
* Border radius
* Shadows
* Component sizing
* Breakpoints
* Motion

### Acceptance Criteria

* Design tokens are centralized.
* Components do not hardcode repeated design values unnecessarily.
* Light/dark behavior is considered where applicable.
* Tokens can be changed globally.
* Base typography is configured.
* The design system is documented.

### Dependencies

FT-001

### Priority

**Must-Have**

### AI Coding Prompt

> Implement the Flowlly design-token system from `design.md` using Flutter theme extensions and reusable styling primitives. Centralize typography, colors, spacing, radius, elevation, motion, and responsive breakpoints. Do not depend on unverified external screen assets or build application screens yet.

---

## FT-003 — Build Core UI Components

### Feature Name

Core UI Component Library

### Task Description

Create reusable foundational Flutter widgets using Material 3 behavior and Flowlly design tokens.

Components should include:

* Button
* Input
* Textarea
* Select
* Checkbox
* Switch
* Dialog
* Dropdown
* Tabs
* Card
* Badge
* Toast
* Tooltip
* Skeleton
* Alert
* Empty state

### Acceptance Criteria

* Components are reusable.
* Components support keyboard interaction.
* Components have accessible labels and states.
* Components follow Flowlly visual tokens.
* Components do not contain business logic.

### Dependencies

FT-002

### Priority

**Must-Have**

### AI Coding Prompt

> Build reusable Flowlly Flutter widgets using Material 3 behavior and the established design tokens. Implement accessible buttons, forms, dialogs, cards, badges, notifications, loading states, and empty states. Keep widgets generic and free of business logic.

---

# 6. EPIC C — Database

---

## FT-004 — Configure PostgreSQL Database

### Feature Name

Database Foundation

### Task Description

Set up Supabase-hosted PostgreSQL as the primary application database.

Configure:

* Supabase local development
* Authenticated client access
* RLS on all user-owned tables
* Versioned Supabase CLI migrations
* Production-safe configuration

### Acceptance Criteria

* Flutter app can connect to Supabase using the public anon key.
* User-owned tables have enabled and tested RLS policies.
* Supabase CLI migrations can be created and executed.
* Database connection errors are handled safely.
* Client configuration contains no service-role or OpenAI secret.
* No database secrets are committed.

### Dependencies

FT-001

### Priority

**Must-Have**

### AI Coding Prompt

> Configure Supabase-hosted PostgreSQL for Flowlly. Set up local Supabase, versioned CLI migrations, authenticated access, and RLS policy testing according to `decisions.md`. Keep privileged credentials and `OPENAI_API_KEY` out of Flutter. Do not implement application-specific models yet.

---

## FT-005 — Create User Data Model

### Feature Name

User Database Model

### Task Description

Create the database model required to store Flowlly users.

Store:

* ID
* Auth identity reference
* Name
* Timezone
* Locale
* Created timestamp
* Updated timestamp

### Acceptance Criteria

* User records can be created.
* User IDs are unique.
* The profile links to the Supabase Auth user ID; email/password credentials remain managed by Supabase Auth.
* Timezone is stored.
* Timestamps are automatically maintained.

### Dependencies

FT-004

### Priority

**Must-Have**

### AI Coding Prompt

> Implement the Flowlly user profile linked to the Supabase Auth user ID. Store timezone, locale, and timestamps; do not duplicate or manage authentication credentials in the profile table. Add RLS and create/run the required migration.

---

# 7. EPIC D — Authentication

---

## FT-006 — Authentication Provider Integration

### Feature Name

Authentication Foundation

### Task Description

Integrate Supabase Auth with email/password.

The implementation must support secure user sessions.

### Acceptance Criteria

* Authentication provider is configured.
* Users can establish authenticated sessions.
* Supabase Auth sessions can be validated by the Flutter client and Edge Functions.
* Auth credentials are managed by Supabase Auth.
* Unauthenticated users cannot access protected application areas or user-owned data.

### Dependencies

FT-005

### Priority

**Must-Have**

### AI Coding Prompt

> Integrate Supabase Auth into Flowlly using email/password. Implement secure session handling, protected Flutter routes, authenticated Edge Function checks, and database RLS. Keep authentication concerns isolated from scheduling and product logic.

---

## FT-007 — User Registration

### Feature Name

User Sign Up

### Task Description

Create the user registration flow.

### Acceptance Criteria

* User can register with email and password.
* Invalid input produces clear errors.
* Duplicate accounts are handled.
* Successful registration creates the required user record.
* User is redirected appropriately after registration.

### Dependencies

FT-006

### Priority

**Must-Have**

### AI Coding Prompt

> Build the Flowlly registration flow using Supabase Auth. Validate input, handle duplicate accounts and authentication errors, create/link the profile without duplicating credentials, and navigate after registration.

---

## FT-008 — User Login

### Feature Name

User Login

### Task Description

Create the login screen and authentication flow.

### Acceptance Criteria

* User can log in using valid credentials.
* Invalid credentials show a useful error.
* Loading state is displayed.
* Authenticated users are redirected to the application.
* Protected routes remain inaccessible without authentication.

### Dependencies

FT-006

### Priority

**Must-Have**

### AI Coding Prompt

> Build the Flowlly Flutter login screen and Supabase Auth flow. Implement validation, loading states, authentication errors, successful navigation, and protected-route behavior.

---

## FT-009 — Logout

### Feature Name

User Logout

### Task Description

Allow authenticated users to securely log out.

### Acceptance Criteria

* Logout action is available from the application.
* Session is invalidated.
* User is redirected to authentication.
* Protected pages cannot be accessed after logout.

### Dependencies

FT-006

### Priority

**Must-Have**

### AI Coding Prompt

> Implement secure Flowlly logout behavior. Invalidate the authenticated session, redirect the user to the login page, and ensure protected resources cannot be accessed afterward.

---

# 8. EPIC E — Onboarding

---

## FT-010 — Onboarding Flow

### Feature Name

Initial User Onboarding

### Task Description

Create the onboarding experience that collects the minimum information needed for personalized scheduling.

Collect:

* Name
* Timezone
* Typical wake time
* Typical sleep time
* Planning preferences

### Acceptance Criteria

* New users are directed to onboarding.
* User can complete onboarding.
* Required fields are validated.
* Preferences are persisted.
* Completed users are not repeatedly shown onboarding.

### Dependencies

FT-007, FT-008

### Priority

**Must-Have**

### AI Coding Prompt

> Build the Flowlly onboarding flow. Collect the user's name, timezone, typical wake time, typical sleep time, and basic planning preferences. Persist the data and ensure onboarding is only shown when incomplete.

---

## FT-011 — User Preferences

### Feature Name

Scheduling Preferences

### Task Description

Create the user preference model and UI for scheduling behavior.

Preferences should support:

* Default wake time
* Default sleep time
* Minimum sleep duration
* Preferred work duration
* Default break duration
* Planning style
* Notification preferences

### Acceptance Criteria

* Preferences are stored per user.
* User can edit preferences.
* Scheduler can access preferences.
* Invalid values are rejected.
* Changes take effect on future planning operations.

### Dependencies

FT-005, FT-010

### Priority

**Must-Have**

### AI Coding Prompt

> Implement Flowlly user scheduling preferences. Create the database model, API/server actions, validation, and settings UI. Make the preferences available to the scheduling engine without coupling the UI directly to scheduler implementation.

---

# 9. EPIC F — Task Management

---

## FT-012 — Task Database Model

### Feature Name

Task Model

### Task Description

Create the task model defined by the TRD.

Support:

* Title
* Description
* Status
* Priority
* Estimated duration
* Deadline
* Preferred time
* Flexibility
* Energy level
* Project
* Goal
* Created/updated timestamps

### Acceptance Criteria

* Task records can be created and updated.
* User ownership is enforced.
* Estimated duration is validated.
* Priority is validated.
* Status values are controlled.
* User data isolation is enforced.

### Dependencies

FT-004, FT-005

### Priority

**Must-Have**

### AI Coding Prompt

> Implement the Flowlly task database model from the TRD. Include ownership, title, description, status, priority, estimated duration, deadline, flexibility, preferred time, energy level, project and goal relationships, and timestamps. Add validation and migrations.

---

## FT-013 — Create Task

### Feature Name

Task Creation

### Task Description

Allow users to create tasks.

### Acceptance Criteria

* User can enter a task title.
* User can specify duration.
* User can specify priority.
* User can optionally specify a deadline.
* User can specify flexible/fixed behavior.
* Invalid values show validation errors.
* Created task appears immediately in the task list.

### Dependencies

FT-012

### Priority

**Must-Have**

### AI Coding Prompt

> Build Flowlly task creation functionality. Create the form, validation, server operation, success handling, error handling, and task-list update. Keep the UI separate from task-domain logic.

---

## FT-014 — Edit Task

### Feature Name

Task Editing

### Task Description

Allow users to modify an existing task.

### Acceptance Criteria

* User can edit supported task properties.
* Changes are persisted.
* Validation is applied.
* Unauthorized tasks cannot be edited.
* Updated task appears immediately.

### Dependencies

FT-013

### Priority

**Must-Have**

### AI Coding Prompt

> Implement task editing for Flowlly. Allow users to update task properties, validate changes, authorize ownership, persist updates, and refresh affected UI state.

---

## FT-015 — Complete Task

### Feature Name

Task Completion

### Task Description

Allow users to mark tasks as completed.

### Acceptance Criteria

* User can mark a task complete.
* Completion timestamp is stored.
* UI immediately reflects completion.
* Completed tasks are not scheduled again.
* Completion can trigger replanning where appropriate.

### Dependencies

FT-013

### Priority

**Must-Have**

### AI Coding Prompt

> Implement Flowlly task completion. Persist completion status and timestamp, update the UI optimistically where appropriate, and ensure completed tasks cannot be accidentally scheduled again.

---

## FT-016 — Delete and Archive Tasks

### Feature Name

Task Removal

### Task Description

Allow users to remove tasks without corrupting historical plan data.

### Acceptance Criteria

* User can delete or archive a task.
* Historical completed plan information remains valid.
* Deleted tasks cannot be scheduled.
* Confirmation is shown for destructive actions.

### Dependencies

FT-013

### Priority

**Must-Have**

### AI Coding Prompt

> Implement safe task deletion/archive behavior. Preserve historical plan integrity and prevent deleted tasks from appearing in future scheduling operations.

---

# 10. EPIC G — Calendar Events

---

## FT-017 — Calendar Event Model

### Feature Name

Calendar Event Database Model

### Task Description

Create the calendar event model.

Support:

* Title
* Start time
* End time
* Location
* Fixed status

### Acceptance Criteria

* Events can be persisted.
* User ownership is enforced.
* Start time must precede end time.
* Fixed status is supported.
* Timezone-aware timestamps are supported.
* Recurrence, source-provider metadata, and external event IDs are not required for MVP fixed events.

### Dependencies

FT-004, FT-005

### Priority

**Must-Have**

### AI Coding Prompt

> Implement the Flowlly fixed-event model with timezone-aware start/end times, user ownership, and fixed status. Do not add recurring-event or external-calendar fields to MVP without a later approved requirement.

---

## FT-018 — Create Calendar Event

### Feature Name

Fixed Event Creation

### Task Description

Allow users to create fixed commitments such as classes, meetings, appointments, and work shifts.

### Acceptance Criteria

* User can create an event.
* Start/end times are validated.
* Event appears in the schedule.
* Event is treated as fixed by the scheduler.

### Dependencies

FT-017

### Priority

**Must-Have**

### AI Coding Prompt

> Build Flowlly fixed-event creation. Implement the form, validation, persistence, and schedule integration. Created fixed events must be recognized as hard scheduling constraints.

---

## FT-019 — Edit Calendar Event

### Feature Name

Calendar Event Editing

### Task Description

Allow users to modify calendar events.

### Acceptance Criteria

* User can edit event details.
* Changes are persisted.
* Scheduler recognizes updated times.
* Conflicts are detected.

### Dependencies

FT-018

### Priority

**Must-Have**

### AI Coding Prompt

> Implement editing for Flowlly calendar events. Validate changes, enforce ownership, persist updates, and trigger conflict detection where required.

---

# 11. EPIC H — Availability

---

## FT-020 — Availability Calculation

### Feature Name

Available Time Windows

### Task Description

Build a domain service that calculates the user's available scheduling windows for a specific date.

It must consider:

* Wake time
* Sleep time
* Fixed events
* Locked plan items
* Existing unavailable periods

### Acceptance Criteria

* Available windows are calculated correctly.
* Fixed events are excluded.
* Sleep periods are excluded.
* Windows do not overlap.
* Timezone is respected.
* Empty availability is handled correctly.

### Dependencies

FT-011, FT-017

### Priority

**Must-Have**

### AI Coding Prompt

> Implement a deterministic Flowlly availability-calculation service. Given a date, user preferences, fixed events, and locked items, return valid available scheduling windows. Include timezone-aware date handling and unit tests for overlaps and edge cases.

---

# 12. EPIC I — Scheduling Engine

---

## FT-021 — Scheduling Domain Types

### Feature Name

Scheduler Domain Model

### Task Description

Define the domain types required by the scheduling engine.

Include:

* Scheduling task
* Fixed commitment
* Availability window
* Candidate placement
* Constraint
* Schedule
* Schedule item
* Scheduling result

### Acceptance Criteria

* Types are strongly typed.
* Scheduler does not depend on database-specific models.
* Domain types are documented.
* Conversion between persistence and domain objects is explicit.

### Dependencies

FT-012, FT-017, FT-020

### Priority

**Must-Have**

### AI Coding Prompt

> Create strongly typed domain models for the Flowlly scheduling engine. Keep them independent from persistence models. Implement explicit mapping between persisted records and scheduler domain objects.

---

## FT-022 — Hard Constraint Validator

### Feature Name

Scheduling Constraint Validation

### Task Description

Implement validation for hard scheduling constraints.

Hard constraints include:

* Fixed events
* Sleep periods
* Unavailable periods
* Locked items
* Task dependencies

### Acceptance Criteria

* Scheduler rejects overlapping fixed items.
* Flexible tasks cannot overwrite fixed commitments.
* Sleep windows are protected.
* Locked items cannot be moved.
* Violations produce structured errors.

### Dependencies

FT-021

### Priority

**Must-Have**

### AI Coding Prompt

> Implement the Flowlly hard-constraint validator. Ensure schedules cannot violate fixed events, sleep periods, locked items, unavailable periods, or dependency constraints. Return structured validation results suitable for API and UI consumption.

---

## FT-023 — Task Priority Scoring

### Feature Name

Task Scheduling Score

### Task Description

Implement a configurable scoring system for flexible tasks.

Consider:

* Priority
* Deadline urgency
* Duration
* Dependencies
* Preferences
* Energy fit

### Acceptance Criteria

* Tasks receive deterministic scores.
* Scoring factors are configurable.
* Scores can be tested independently.
* Scheduler can rank tasks using scores.

### Dependencies

FT-021

### Priority

**Must-Have**

### AI Coding Prompt

> Implement a deterministic and configurable task scoring system for Flowlly. Score flexible tasks using priority, deadline urgency, duration, dependency importance, user preferences, and energy compatibility. Add unit tests.

---

## FT-024 — Basic Schedule Generator

### Feature Name

Daily Schedule Generation

### Task Description

Build the first complete scheduling algorithm.

The algorithm must:

1. Load fixed commitments.
2. Calculate available windows.
3. Rank flexible tasks.
4. Place tasks.
5. Respect hard constraints.
6. Avoid unnecessary fragmentation.
7. Validate the resulting schedule.

### Acceptance Criteria

* A valid daily schedule can be generated.
* Fixed events are protected.
* Flexible tasks are placed when feasible.
* Impossible tasks are reported.
* No schedule overlaps occur.
* Output is deterministic for the same input.
* Scheduling uses 15-minute granularity.
* Candidates are ordered by higher score, then earlier deadline, earlier creation time, and task ID lexicographically, as defined in `decisions.md`.

### Dependencies

FT-020, FT-022, FT-023

### Priority

**Must-Have**

### AI Coding Prompt

> Build the first deterministic Flowlly daily scheduling engine. Use availability windows, hard constraints, and task scoring to produce a feasible schedule. Do not use AI. Return both scheduled items and unscheduled/conflicting tasks. Add comprehensive unit tests.

---

## FT-025 — Task Splitting

### Feature Name

Flexible Task Splitting

### Task Description

Allow long flexible tasks to be split across multiple available windows.

### Acceptance Criteria

* Splitting only occurs for tasks that allow it.
* Total scheduled duration does not exceed the requested duration.
* Each segment respects constraints.
* Segments are linked to the original task.
* Unscheduled remainder is reported.

### Dependencies

FT-024

### Priority

**Should-Have**

### AI Coding Prompt

> Extend the Flowlly scheduling engine with configurable task splitting. Allow eligible tasks to occupy multiple available windows while preserving total required duration and all hard constraints.

---

# 13. EPIC J — Daily Plans

---

## FT-026 — Daily Plan Database Model

### Feature Name

Daily Plan Model

### Task Description

Create persistence models for daily plans and plan items.

### Acceptance Criteria

* A plan belongs to one user and date.
* Plan items can reference tasks or events.
* Start/end times are stored.
* Plan version is tracked.
* Status is tracked.

### Dependencies

FT-024

### Priority

**Must-Have**

### AI Coding Prompt

> Implement Flowlly daily plan and plan-item database models. Support user/date ownership, task/event references, start/end times, status, and versioning. Add database constraints and migrations.

---

## FT-027 — Generate Today's Plan

### Feature Name

Initial Daily Plan Generation

### Task Description

Create the application flow that generates today's plan using the scheduling engine and persists it.

### Acceptance Criteria

* User can generate today's plan.
* Existing fixed commitments are included.
* Flexible tasks are scheduled.
* Unscheduled tasks are identified.
* Generated plan is persisted.
* Plan version is created.

### Dependencies

FT-026

### Priority

**Must-Have**

### AI Coding Prompt

> Connect the Flowlly scheduling engine to the application layer and implement today's plan generation. Load user context, tasks, events, and preferences, generate a validated schedule, persist it as a plan version, and return the result.

---

## FT-028 — Plan Versioning

### Feature Name

Plan Version History

### Task Description

Create versioning for daily plans.

### Acceptance Criteria

* Initial plan is version 1.
* Replanning creates a new version.
* Previous versions remain accessible.
* Each version has creation metadata.
* Current version can be identified.

### Dependencies

FT-026

### Priority

**Must-Have**

### AI Coding Prompt

> Implement Flowlly daily plan versioning. Every significant plan generation or replanning operation must create a new immutable plan version while preserving previous versions for history and debugging.

---

# 14. EPIC K — Today Experience

---

## FT-029 — Today Page Layout

### Feature Name

Today Dashboard

### Task Description

Build the primary Flowlly Today screen according to the approved Google Stitch design.

The screen should show:

* Greeting
* Current time
* Day progress
* Current task
* Next task
* Upcoming tasks
* Schedule timeline
* Main actions

### Acceptance Criteria

* Today page is responsive.
* Current task is visually dominant.
* Next task is clearly visible.
* Timeline reflects actual plan data.
* Loading and empty states exist.
* UI follows Stitch-approved design direction.

### Dependencies

FT-003, FT-027

### Priority

**Must-Have**

### AI Coding Prompt

> Implement the Flowlly Today dashboard based on the approved Google Stitch visual design. Connect it to real daily-plan data. Build reusable components for greeting, progress, current task, next task, timeline, loading states, and empty states. Do not hardcode schedule data.

---

## FT-030 — Current Task Detection

### Feature Name

Current Task

### Task Description

Determine which plan item is currently active based on the user's local time.

### Acceptance Criteria

* Current task is calculated from actual current time.
* Past tasks are not shown as current.
* Future tasks are not shown as current.
* Empty current state is handled.
* Timezone is respected.

### Dependencies

FT-027

### Priority

**Must-Have**

### AI Coding Prompt

> Implement deterministic current-plan-item detection for Flowlly using the user's timezone and current time. Return the active item, next item, or appropriate empty state.

---

## FT-031 — Day Progress

### Feature Name

Daily Progress Indicator

### Task Description

Calculate and display how far the user has progressed through their planned day.

### Acceptance Criteria

* Progress reflects the current time and plan.
* Progress cannot become negative.
* Progress cannot exceed 100%.
* Completed-task information can be displayed separately.
* UI updates appropriately.

### Dependencies

FT-027

### Priority

**Should-Have**

### AI Coding Prompt

> Implement Flowlly daily progress calculations and UI. Clearly distinguish elapsed-day progress from task-completion progress where both are displayed.

---

# 15. EPIC L — Task Execution

---

## FT-032 — Start Current Task

### Feature Name

Start Task

### Task Description

Allow users to explicitly start their current task.

### Acceptance Criteria

* User can start a task.
* Task execution state is persisted.
* UI shows the task as active.
* Starting a task does not modify fixed commitments.

### Dependencies

FT-015, FT-029

### Priority

**Must-Have**

### AI Coding Prompt

> Implement task-start functionality for the Flowlly Today screen. Persist execution state and update the UI while keeping scheduling logic separate from presentation.

---

## FT-033 — Skip Task

### Feature Name

Skip Task

### Task Description

Allow users to skip a scheduled task.

### Acceptance Criteria

* User can skip a task.
* Skip state is persisted.
* The task is not accidentally marked complete.
* The plan item state is `skipped`, distinct from `completed`, `missed`, and `superseded`.
* User can trigger replanning afterward.

### Dependencies

FT-015, FT-029

### Priority

**Must-Have**

### AI Coding Prompt

> Implement task skipping in Flowlly. Persist a distinct skipped state, preserve history, remove the item from active execution, and make the action available as a replanning trigger.

---

# 16. EPIC M — I'm Awake

---

## FT-034 — Wake Event Model

### Feature Name

Wake Event

### Task Description

Create persistence for planned and actual wake times.

### Acceptance Criteria

* Planned wake time is recorded.
* Actual wake time is recorded.
* User ownership is enforced.
* Date and timezone are handled correctly.
* Wake events can reference replanning operations.

### Dependencies

FT-005, FT-026

### Priority

**Must-Have**

### AI Coding Prompt

> Implement the Flowlly wake-event model. Store planned and actual wake times with timezone-aware dates and connect wake events to the resulting replanning operation.

---

## FT-035 — I'm Awake UI

### Feature Name

I'm Awake Button

### Task Description

Add the primary “I'm Awake” interaction to the Today experience.

### Acceptance Criteria

* Button is clearly visible when appropriate.
* User can press it once.
* Current local time is captured.
* Duplicate wake events are handled safely.
* User receives feedback after pressing.

### Dependencies

FT-034, FT-029

### Priority

**Must-Have**

### AI Coding Prompt

> Implement the Flowlly “I'm Awake” interaction on the Today screen. Capture the user's local current time, persist the wake event, prevent duplicate submissions, show loading feedback, and prepare the system for replanning.

---

## FT-036 — Confirm Actual Wake Time

### Feature Name

Wake Time Confirmation

### Task Description

Allow users to confirm or correct the automatically captured wake time.

### Acceptance Criteria

* Captured current time is shown.
* User can adjust the time.
* Invalid times are rejected.
* Confirmed time is persisted.
* Replanning uses the confirmed value.

### Dependencies

FT-035

### Priority

**Must-Have**

### AI Coding Prompt

> Add a confirmation step to the Flowlly “I'm Awake” workflow. Show the detected local wake time, allow correction, validate the selected time, persist it, and use the final value for replanning.

---

# 17. EPIC N — Adaptive Replanning

---

## FT-037 — Replanning Domain Model

### Feature Name

Replanning Run

### Task Description

Create the persistence model for replanning operations.

Store:

* Trigger
* Input snapshot
* Output snapshot
* Status
* Start/end times
* Explanation
* Resulting plan version

### Acceptance Criteria

* Every replan has a unique ID.
* Trigger is recorded.
* Status is tracked.
* Resulting plan version is linked.
* Failed replans can be diagnosed.

### Dependencies

FT-028, FT-034

### Priority

**Must-Have**

### AI Coding Prompt

> Implement the Flowlly replanning-run model. Store trigger, input/output snapshots, status, timestamps, explanation, and resulting plan-version references. Ensure the model supports debugging failed replans.

---

## FT-038 — Replanning Engine

### Feature Name

Adaptive Schedule Replanning

### Task Description

Implement the deterministic replanning workflow.

It must:

1. Load the current plan.
2. Record the new current state.
3. Protect fixed commitments.
4. Remove invalid future placements.
5. Identify remaining tasks.
6. Calculate available time.
7. Reschedule flexible tasks.
8. Validate the new schedule.
9. Create a new plan version.

### Acceptance Criteria

* Late wake-up triggers replanning.
* Fixed commitments remain protected.
* Past flexible items are not scheduled.
* Remaining tasks are considered.
* New plan is valid.
* Previous plan remains preserved.
* Replanning creates a new version.

### Dependencies

FT-024, FT-028, FT-037

### Priority

**Must-Have**

### AI Coding Prompt

> Implement Flowlly's deterministic adaptive replanning engine. Given the current plan and a changed user state such as a late wake time, rebuild only the affected portion of the remaining day while protecting hard constraints. Persist the result as a new immutable plan version and create a replanning-run record. Do not use AI for this ticket.

---

## FT-039 — Replan After Wake-Up

### Feature Name

Wake-Triggered Replanning

### Task Description

Connect the “I'm Awake” action to the replanning engine.

### Acceptance Criteria

* Pressing “I'm Awake” records the wake event.
* Replanning starts automatically.
* User sees a loading state.
* New plan is generated.
* Fixed commitments remain intact.
* User sees the updated schedule.

### Dependencies

FT-035, FT-038

### Priority

**Must-Have**

### AI Coding Prompt

> Connect Flowlly's confirmed wake event to the adaptive replanning engine. When the user confirms that they are awake, automatically generate a new plan for the remaining day, persist the new plan version, and return the updated schedule to the UI.

---

## FT-040 — Replan Summary

### Feature Name

Schedule Change Summary

### Task Description

Show users what changed during replanning.

Categorize changes as:

* Moved
* Postponed
* Removed
* Protected
* Split

### Acceptance Criteria

* Changes are derived from actual plan versions.
* User can understand major schedule changes.
* Fixed commitments are clearly identified.
* No fabricated explanations are shown.

### Dependencies

FT-039

### Priority

**Must-Have**

### AI Coding Prompt

> Build the Flowlly replanning summary. Compare the previous and new plan versions and generate a deterministic list of moved, postponed, removed, split, and protected items. Display the changes clearly without inventing reasons.

---

## FT-041 — Accept New Plan

### Feature Name

Accept Replanned Schedule

### Task Description

Allow the user to accept the newly generated plan.

### Acceptance Criteria

* New plan becomes current.
* User returns to Today.
* Previous version remains in history.
* Acceptance is recorded.

### Dependencies

FT-040

### Priority

**Must-Have**

### AI Coding Prompt

> Implement Flowlly plan acceptance. Make the newly generated version the active plan, preserve previous versions, and record the acceptance action.

---

## FT-042 — Keep Existing Plan

### Feature Name

Reject Replanned Schedule

### Task Description

Allow the user to keep the previous plan.

### Acceptance Criteria

* Previous plan remains active.
* New proposed version remains available as history/proposal where appropriate.
* User can continue using the original schedule.

### Dependencies

FT-040

### Priority

**Must-Have**

### AI Coding Prompt

> Implement the Flowlly option to keep the existing schedule after replanning. Preserve both versions appropriately while maintaining the previous version as the active plan.

---

# 18. EPIC O — Manual Replanning

---

## FT-043 — Manual Replan

### Feature Name

Replan My Day

### Task Description

Add a manual action allowing users to request schedule recalculation.

### Acceptance Criteria

* User can trigger replanning manually.
* Current plan is used as input.
* Fixed commitments remain protected.
* New plan is validated.
* Changes are displayed before acceptance.

### Dependencies

FT-038, FT-040

### Priority

**Must-Have**

### AI Coding Prompt

> Implement a “Replan My Day” action for Flowlly. Trigger the deterministic replanning engine using the current state, protect hard constraints, create a new plan version, and show the resulting changes for user review.

---

# 19. EPIC P — AI Planning

---

## FT-044 — AI Service Abstraction

### Feature Name

AI Provider Layer

### Task Description

Create the OpenAI-backed AI service interface used by Supabase Edge Functions.

The application should not directly couple business logic to a specific AI provider.

### Acceptance Criteria

* OpenAI calls are isolated behind a server-side Edge Function service interface.
* `OPENAI_API_KEY` is read from Supabase Edge Function secrets only.
* AI failures are handled gracefully.
* Provider can be replaced later.
* Requests and responses are typed.
* Any AI schedule output is a proposal, must pass the deterministic validator, and cannot be persisted by AI.

### Dependencies

FT-001

### Priority

**Should-Have**

### AI Coding Prompt

> Create an OpenAI service adapter for Supabase Edge Functions. Keep `OPENAI_API_KEY` in Supabase secrets, define typed request/response contracts, implement error handling, and ensure AI schedule output is only a proposal that passes deterministic validation before the server can persist an accepted change.

---

## FT-045 — Structured AI Planning Proposal

### Feature Name

AI Schedule Proposal

### Task Description

Allow AI to produce structured scheduling suggestions without directly modifying the database.

### Acceptance Criteria

* AI output follows a strict schema.
* Invalid AI output is rejected.
* AI output is only a proposal; the deterministic validator checks it, and the server decides whether user-confirmed changes can be persisted.
* Proposal includes reasoning metadata.
* Proposal can be passed to the deterministic validation layer.

### Dependencies

FT-044, FT-022

### Priority

**Should-Have**

### AI Coding Prompt

> Implement structured AI planning proposals for Flowlly. Define a strict schema, validate every response through the deterministic validator, and ensure the server—not AI—decides what user-confirmed change is persisted.

---

## FT-046 — AI Schedule Explanation

### Feature Name

AI Replanning Explanation

### Task Description

Generate concise natural-language explanations for meaningful schedule changes.

### Acceptance Criteria

* Explanation is based on actual plan changes.
* Explanation does not invent events.
* Explanation is concise.
* AI failure does not prevent scheduling.
* User can still view deterministic change information.
* The structured AI response is a proposal tied only to validator-approved changes; it cannot introduce or persist a schedule change.
* Any suggested schedule change is a proposal and must pass deterministic validation.

### Dependencies

FT-045, FT-040

### Priority

**Should-Have**

### AI Coding Prompt

> Implement post-MVP AI explanations for Flowlly schedule changes. Treat structured AI output as a proposal tied only to validator-approved changes. Any suggested schedule change must pass deterministic validation; the server remains responsible for persistence.

---

## FT-047 — Natural Language Task Creation

### Feature Name

AI Task Parsing

### Task Description

Allow users to create tasks using natural language.

Example:

> “Study database systems for two hours tomorrow afternoon.”

Convert the input into structured task fields.

### Acceptance Criteria

* AI extracts task title.
* Duration is extracted when available.
* Date/time preference is extracted when available.
* Ambiguous information is not silently invented.
* AI output is a task proposal, passes schema and deterministic business validation, and requires user confirmation before the server saves it.

### Dependencies

FT-044, FT-012

### Priority

**Should-Have**

### AI Coding Prompt

> Implement post-MVP natural-language task parsing. Return a structured task proposal, explicitly represent uncertainty, validate it against deterministic business rules, and require user confirmation before the server saves it.

---

# 20. EPIC Q — Notifications

---

## FT-048 — Notification Model

### Feature Name

Notification Preferences and Records

### Task Description

Create the data model for basic in-app reminder preferences and records.

### Acceptance Criteria

* User notification preferences can be stored.
* In-app reminder type and delivery status are tracked.
* Notifications belong to the correct user.
* The MVP implementation does not send push, email, or browser notifications.

### Dependencies

FT-005

### Priority

**Must-Have**

### AI Coding Prompt

> Implement in-app reminder preferences and records for Flowlly. Support user ownership, type, scheduling metadata, delivery status, and configurable preferences. Do not implement push, email, or browser delivery in the MVP.

---

## FT-049 — Task Reminder

### Feature Name

Task Reminder

### Task Description

Notify users in-app before an upcoming scheduled task.

### Acceptance Criteria

* Reminder timing can be configured.
* In-app reminder is shown at the correct local time.
* Duplicate notifications are prevented.
* User can disable reminders.
* Push, email, and browser reminders are excluded from the MVP.

### Dependencies

FT-048, FT-027

### Priority

**Must-Have**

### AI Coding Prompt

> Implement basic in-app scheduled task reminders using the user's timezone and notification preferences. Prevent duplicates and respect disabled settings; do not implement push or email.

---

## FT-050 — Replanning Notification

### Feature Name

Replanning Completion Notification

### Task Description

Notify the user when background replanning finishes.

### Acceptance Criteria

* Notification only occurs for applicable background replans.
* User can open the updated plan.
* Failed replans do not generate misleading success messages.

### Dependencies

FT-048, FT-038

### Priority

**Nice-to-Have**

### AI Coding Prompt

> Implement optional notifications for completed background replanning operations. Ensure notifications accurately reflect success or failure and link users to the relevant plan.

---

# 21. EPIC R — Projects

---

## FT-051 — Project Model

### Feature Name

Project Management Model

### Task Description

Create project records that group related tasks.

### Acceptance Criteria

* Projects belong to users.
* Tasks can belong to projects.
* Project names are required.
* Projects can be archived.

### Dependencies

FT-012

### Priority

**Should-Have**

### AI Coding Prompt

> Implement Flowlly project management at the domain and database level. Projects should group tasks without directly controlling scheduling logic.

---

## FT-052 — Project UI

### Feature Name

Projects Screen

### Task Description

Build a project list and project detail experience.

### Acceptance Criteria

* User can create projects.
* User can view projects.
* User can see associated tasks.
* User can archive projects.

### Dependencies

FT-051

### Priority

**Should-Have**

### AI Coding Prompt

> Build the Flowlly Projects screen and detail view using reusable components and the established design system. Display project tasks and progress without duplicating scheduling logic.

---

# 22. EPIC S — Goals

---

## FT-053 — Goal Model

### Feature Name

Goal Management Model

### Task Description

Create goals that can contain projects and tasks.

### Acceptance Criteria

* Goals belong to users.
* Projects can be associated with goals.
* Goals can be active or archived.
* Goal metadata is persisted.

### Dependencies

FT-051

### Priority

**Should-Have**

### AI Coding Prompt

> Implement the Flowlly goal model and relationships with projects. Keep goal management separate from scheduling logic while allowing the scheduler to access goal context.

---

## FT-054 — Goals UI

### Feature Name

Goals Screen

### Task Description

Create the Goals experience.

### Acceptance Criteria

* User can create goals.
* User can view active goals.
* User can associate projects with goals.
* Archived goals are retained historically.

### Dependencies

FT-053

### Priority

**Should-Have**

### AI Coding Prompt

> Build the Flowlly Goals screen and goal detail experience using the established design system. Support creation, viewing, project association, and archiving.

---

# 23. EPIC T — Task Dependencies

---

## FT-055 — Task Dependency Model

### Feature Name

Task Dependencies

### Task Description

Allow tasks to depend on other tasks.

### Acceptance Criteria

* Dependencies can be created.
* Circular dependencies are prevented.
* Scheduler can access dependency relationships.
* Completed dependencies are recognized.

### Dependencies

FT-012

### Priority

**Should-Have**

### AI Coding Prompt

> Implement Flowlly task dependencies. Add database relationships, circular-dependency validation, and scheduler-accessible dependency information.

---

# 24. EPIC U — Locked Schedule Items

---

## FT-056 — Lock Schedule Item

### Feature Name

Schedule Locking

### Task Description

Allow users to lock fixed commitments they choose so that replanning treats them as hard constraints.

### Acceptance Criteria

* User can lock/unlock eligible items.
* Locked state is persisted.
* Scheduler cannot move locked items without explicit user action.
* UI clearly displays lock state.
* MVP locking protects fixed commitments the user chooses; locking arbitrary flexible items is not required.

### Dependencies

FT-027

### Priority

**Must-Have**

### AI Coding Prompt

> Implement schedule-item locking in Flowlly. Persist lock state, expose it in the UI, and update scheduler constraint handling so locked items behave as hard constraints.

---

# 25. EPIC V — Recurring Tasks

---

## FT-057 — Recurring Task Model

### Feature Name

Recurring Tasks

### Task Description

Support simple daily and weekly recurring tasks only. Advanced recurrence is post-MVP.

Examples:

* Exercise every Monday
* Weekly planning
* Daily reading

### Acceptance Criteria

* Daily or weekly recurrence can be persisted.
* Occurrences can be calculated for a planning day.
* Infinite database records are not generated unnecessarily.
* Weekday-only, custom-day, custom-interval, and complex exception rules are excluded.

### Dependencies

FT-012

### Priority

**Must-Have**

### AI Coding Prompt

> Implement simple daily and weekly recurring tasks using recurrence rules rather than generating unlimited task records. Keep advanced recurrence rules out of scope.

---

# 26. EPIC W — Schedule Conflict Handling

---

## FT-058 — Conflict Detection

### Feature Name

Schedule Conflict Detection

### Task Description

Detect conflicts between:

* Tasks
* Events
* Sleep
* Locked items
* Deadlines
* Dependencies

### Acceptance Criteria

* Overlaps are detected.
* Conflicts have structured types.
* UI can display conflict information.
* Scheduler cannot silently create invalid plans.

### Dependencies

FT-022, FT-024

### Priority

**Must-Have**

### AI Coding Prompt

> Implement structured schedule conflict detection for Flowlly. Detect temporal overlaps, deadline conflicts, sleep conflicts, locked-item conflicts, and dependency conflicts. Return structured conflict objects suitable for UI and scheduling validation.

---

## FT-059 — Conflict Resolution UI

### Feature Name

Schedule Conflict Resolution

### Task Description

Show users actionable options when the scheduler cannot satisfy all requirements.

### Acceptance Criteria

* Conflict explanation is understandable.
* User sees available resolution options.
* Options do not silently modify unrelated data.
* User can choose to postpone, split, or modify where applicable.

### Dependencies

FT-058

### Priority

**Should-Have**

### AI Coding Prompt

> Build the Flowlly conflict-resolution UI. Present structured scheduling conflicts and appropriate user actions such as move, split, postpone, or edit. Keep user decisions explicit.

---

# 27. EPIC X — Calendar Integration

---

## FT-060 — Calendar Integration Abstraction

### Feature Name

External Calendar Adapter

### Task Description

Create an abstraction for external calendar providers.

### Acceptance Criteria

* Provider interface is defined.
* Application code does not depend directly on one provider.
* Events can be imported through the interface.
* Sync errors are represented.

### Dependencies

FT-017

### Priority

**Nice-to-Have**

### AI Coding Prompt

> Create a provider-independent calendar integration interface for Flowlly. Define methods for authentication, event retrieval, event creation where supported, and synchronization status. Do not implement a specific provider yet.

---

## FT-061 — Google Calendar Integration

### Feature Name

Google Calendar Sync

### Task Description

Integrate Google Calendar using the calendar adapter.

### Acceptance Criteria

* User can connect Google Calendar.
* Calendar events can be imported.
* External events are mapped to Flowlly events.
* Sync respects user timezone.
* Duplicate events are avoided.

### Dependencies

FT-060

### Priority

**Nice-to-Have**

### AI Coding Prompt

> Implement Google Calendar synchronization through the existing Flowlly calendar adapter. Import events safely, map external IDs, respect timezones, prevent duplicates, and handle authorization/sync failures.

---

# 28. EPIC Y — Insights

---

## FT-062 — Completion Analytics

### Feature Name

Productivity Insights

### Task Description

Create basic insights from user scheduling activity.

Show:

* Tasks completed
* Tasks postponed
* Tasks skipped
* Plans generated
* Plans replanned

### Acceptance Criteria

* Data is calculated accurately.
* User sees historical trends.
* No unsupported productivity claims are presented.
* Empty-state behavior exists.

### Dependencies

FT-015, FT-028

### Priority

**Should-Have**

### AI Coding Prompt

> Build basic Flowlly scheduling insights using persisted task and plan history. Display completion, postponement, skipping, planning, and replanning metrics accurately without making unsupported claims about productivity.

---

## FT-063 — Schedule Accuracy Insights

### Feature Name

Planning Accuracy

### Task Description

Compare estimated task durations against actual execution where available.

### Acceptance Criteria

* Estimates are stored.
* Actual duration can be measured when execution tracking exists.
* Differences can be displayed.
* Historical trends are available.

### Dependencies

FT-032, FT-062

### Priority

**Nice-to-Have**

### AI Coding Prompt

> Implement Flowlly planning-accuracy analytics by comparing estimated and actual task durations. Store only necessary data and present historical differences clearly.

---

# 29. EPIC Z — Personalization

---

## FT-064 — User Behavior History

### Feature Name

Scheduling Behavior History

### Task Description

Capture relevant scheduling behavior to support future personalization.

Track:

* Typical wake time
* Completion duration
* Postponement frequency
* Common scheduling conflicts
* Preferred work windows

### Acceptance Criteria

* Data is derived from actual user actions.
* Data can be deleted with user data.
* Historical information is separated from core task state.
* Collection is privacy-conscious.

### Dependencies

FT-015, FT-034, FT-062

### Priority

**Nice-to-Have**

### AI Coding Prompt

> Implement privacy-conscious Flowlly scheduling behavior history. Derive behavioral metrics from actual application events without collecting unnecessary personal information. Keep this data separate from core task and plan records.

---

## FT-065 — Personalized Scheduling

### Feature Name

Personalized Scheduler

### Task Description

Allow historical behavior to influence soft scheduling preferences.

### Acceptance Criteria

* Historical behavior only affects soft constraints.
* Hard constraints remain unchanged.
* User can disable personalization.
* Scheduling remains deterministic for the same learned profile.

### Dependencies

FT-064

### Priority

**Nice-to-Have**

### AI Coding Prompt

> Extend the Flowlly scheduler to optionally use learned user behavior as soft scheduling preferences. Never allow personalization to override hard constraints. Provide a user-controlled personalization setting.

---

# 30. EPIC AA — Mobile Experience

---

## FT-066 — Responsive Mobile Layout

### Feature Name

Mobile Today Experience

### Task Description

Optimize the primary Flowlly workflow for mobile screens.

### Acceptance Criteria

* Today screen works on mobile.
* Current task is easy to access.
* “I'm Awake” is easy to access.
* Timeline is usable.
* Touch targets meet accessibility expectations.
* No horizontal overflow occurs.

### Dependencies

FT-029

### Priority

**Must-Have**

### AI Coding Prompt

> Optimize the Flowlly Today experience for mobile. Follow the approved Stitch mobile design, prioritize the current task and “I'm Awake” action, ensure touch-friendly controls, and eliminate horizontal overflow.

---

# 31. EPIC AB — Accessibility

---

## FT-067 — Accessibility Audit

### Feature Name

Accessibility Compliance

### Task Description

Audit the application for accessibility issues.

### Acceptance Criteria

* Keyboard navigation works.
* Focus states are visible.
* Form controls have accessible names.
* Dialogs are accessible.
* Color contrast is acceptable.
* Screen-reader semantics are present.
* Reduced-motion preferences are respected.

### Dependencies

FT-003, FT-029

### Priority

**Must-Have**

### AI Coding Prompt

> Perform a comprehensive accessibility audit of the current Flowlly UI and fix issues related to keyboard navigation, focus management, semantic markup, labels, dialogs, contrast, screen readers, and reduced motion.

---

# 32. EPIC AC — Error Handling

---

## FT-068 — Global Error Handling

### Feature Name

Application Error System

### Task Description

Create consistent application error handling.

### Acceptance Criteria

* API errors have structured responses.
* UI displays understandable messages.
* Internal errors do not expose stack traces.
* Errors are logged server-side.
* Network failures have recovery states.

### Dependencies

FT-001

### Priority

**Must-Have**

### AI Coding Prompt

> Implement a centralized Flowlly error-handling system across API, server, and UI layers. Use structured error types, safe user-facing messages, server-side logging, and appropriate recovery states.

---

# 33. EPIC AD — Security

---

## FT-069 — Authorization Audit

### Feature Name

Resource Authorization

### Task Description

Audit all user-owned resources and ensure server-side ownership validation.

### Acceptance Criteria

* Users cannot access another user's tasks.
* Users cannot access another user's events.
* Users cannot access another user's plans.
* Users cannot access another user's goals/projects.
* APIs reject unauthorized resource IDs.

### Dependencies

FT-012, FT-017, FT-026

### Priority

**Must-Have**

### AI Coding Prompt

> Perform a server-side authorization audit across Flowlly resources. Verify ownership for tasks, events, plans, projects, goals, preferences, wake events, and replanning runs. Add tests proving cross-user access is rejected.

---

## FT-070 — Rate Limiting

### Feature Name

API Rate Limiting

### Task Description

Protect MVP authentication and replanning endpoints from abuse. Add limits to AI and natural-language endpoints when those post-MVP capabilities are introduced.

Prioritize:

* Authentication
* Replanning

### Acceptance Criteria

* Rate limits are applied server-side.
* Excess requests receive structured errors.
* Limits can be configured.
* AI endpoints are protected.

### Dependencies

FT-068

### Priority

**Must-Have**

### AI Coding Prompt

> Implement configurable server-side rate limiting for Flowlly authentication, AI, replanning, and expensive endpoints. Return appropriate structured responses and avoid exposing internal implementation details.

---

# 34. EPIC AE — Observability

---

## FT-071 — Application Logging

### Feature Name

Structured Logging

### Task Description

Implement structured backend logging.

### Acceptance Criteria

* Important application operations are logged.
* Logs include useful context.
* Sensitive information is excluded.
* Errors include diagnostic metadata.
* Logging works in development and production.

### Dependencies

FT-001

### Priority

**Must-Have**

### AI Coding Prompt

> Implement structured logging for Flowlly. Add useful context for authentication, scheduling, replanning, AI operations, database failures, and integrations while preventing unnecessary sensitive data from entering logs.

---

## FT-072 — Replanning Observability

### Feature Name

Scheduling Diagnostics

### Task Description

Make scheduling and replanning failures diagnosable.

### Acceptance Criteria

* Replanning runs record status.
* Input and output snapshots can be inspected securely.
* Failure reasons are stored.
* Scheduler validation failures are traceable.

### Dependencies

FT-037, FT-038, FT-071

### Priority

**Must-Have**

### AI Coding Prompt

> Add diagnostic observability to Flowlly scheduling and replanning. Record safe input/output metadata, status, failure reasons, validation errors, and timing information without storing unnecessary sensitive data.

---

# 35. EPIC AF — Testing

---

## FT-073 — Scheduler Unit Tests

### Feature Name

Scheduling Engine Tests

### Task Description

Create comprehensive unit tests for the scheduling engine.

### Acceptance Criteria

Tests cover:

* Fixed events
* Flexible tasks
* Sleep
* Deadlines
* Priorities
* Conflicts
* Locked items
* Task splitting
* Empty availability
* Late wake-up

### Dependencies

FT-024, FT-038

### Priority

**Must-Have**

### AI Coding Prompt

> Build comprehensive unit tests for the Flowlly scheduling and replanning engines. Cover normal schedules, late wake-ups, conflicting events, unavailable time, deadlines, locked items, task splitting, and impossible schedules.

---

## FT-074 — API Integration Tests

### Feature Name

Backend Integration Tests

### Task Description

Test core APIs against the real application/database layer.

### Acceptance Criteria

Tests cover:

* Authentication
* Task creation
* Event creation
* Plan generation
* Wake event
* Replanning
* Plan acceptance

### Dependencies

FT-039, FT-041

### Priority

**Must-Have**

### AI Coding Prompt

> Implement integration tests for Flowlly's core backend flows. Test authentication, tasks, events, daily planning, wake events, replanning, and plan acceptance against the application database layer.

---

## FT-075 — End-to-End Core Flow

### Feature Name

Core User Journey E2E

### Task Description

Create an end-to-end test covering the primary user journey.

### Acceptance Criteria

The test successfully performs:

```text
Register
→ Onboarding
→ Create Task
→ Create Event
→ Generate Plan
→ I'm Awake
→ Replan
→ Accept Plan
→ Complete Task
```

### Dependencies

FT-074

### Priority

**Must-Have**

### AI Coding Prompt

> Build a complete end-to-end test for the primary Flowlly journey: registration, onboarding, task creation, event creation, plan generation, “I'm Awake,” adaptive replanning, plan acceptance, and task completion.

---

# 36. EPIC AG — Google Stitch Implementation Workflow

---

## FT-076 — Create Stitch Screen Specification

### Feature Name

Google Stitch Screen System

### Task Description

Create the complete Stitch screen specification for the Flowlly MVP.

Screens should include:

* Landing
* Login
* Sign up
* Onboarding
* Today
* Schedule
* Tasks
* Task creation
* Task detail
* I'm Awake
* Replanning
* Replan summary
* Settings
* Empty states
* Loading states
* Error states

### Acceptance Criteria

* All MVP screens are represented.
* Desktop and mobile concepts exist.
* Primary user flow is visually consistent.
* Components are visually reusable.
* The Today experience clearly prioritizes current activity.

### Dependencies

FT-002

### Priority

**Should-Have**

### AI Coding Prompt

> Create the complete Flowlly MVP UI specification in Google Stitch. Design all required screens, responsive states, empty/loading/error states, and the primary “I'm Awake → Replan” flow. Maintain a calm, intelligent, personal scheduling-product aesthetic rather than a generic project-management dashboard.

---

## FT-077 — Translate Stitch Designs to Flutter UI

### Feature Name

Stitch-to-Flutter Implementation

### Task Description

Translate approved Stitch designs into production Flutter widgets.

### Acceptance Criteria

* Production UI follows approved Stitch hierarchy.
* Components are reusable.
* Responsive behavior is implemented.
* Business logic is not embedded in visual components.
* Design tokens are used.
* Accessibility requirements are preserved.

### Dependencies

FT-003, FT-076

### Priority

**Should-Have**

### AI Coding Prompt

> Translate the approved Flowlly Google Stitch designs into production Flutter widgets. Preserve visual hierarchy and interaction patterns while converting repeated UI into reusable widgets. Do not blindly copy generated markup and do not place business logic inside presentation components.

---

# 37. EPIC AH — Production Readiness

---

## FT-078 — Environment Configuration

### Feature Name

Production Environment Setup

### Task Description

Create separate configuration for development, staging, and production.

### Acceptance Criteria

* Environment variables are documented.
* Secrets are not committed.
* Development/staging/production configurations are separated.
* Missing required environment variables fail clearly.

### Dependencies

FT-001

### Priority

**Must-Have**

### AI Coding Prompt

> Implement robust Flowlly environment configuration for development, staging, and production. Validate required environment variables at startup and ensure secrets are never exposed to the client or committed to source control.

---

## FT-079 — Database Backup Strategy

### Feature Name

Database Backup

### Task Description

Configure automated PostgreSQL backups for production.

### Acceptance Criteria

* Automated backups exist.
* Retention is configured.
* Recovery procedure is documented.
* Production database can be restored using the documented process.

### Dependencies

FT-004

### Priority

**Must-Have**

### AI Coding Prompt

> Configure and document the production PostgreSQL backup and recovery strategy for Flowlly. Define backup frequency, retention, restoration procedure, and recovery verification.

---

## FT-080 — Production Deployment

### Feature Name

Production Deployment

### Task Description

Deploy the Flowlly application to production.

### Acceptance Criteria

* Production application loads successfully.
* HTTPS is enabled.
* Database connection works.
* Authentication works.
* Core APIs work.
* Environment variables are configured securely.
* Production build succeeds.

### Dependencies

FT-078, FT-079, FT-075

### Priority

**Must-Have**

### AI Coding Prompt

> Deploy Flowlly to the selected production hosting platform. Configure HTTPS, environment variables, database connectivity, authentication, and production builds. Verify the complete core user journey in the deployed environment.

---

# 38. EPIC — Final MVP Polish

---

## FT-081 — Loading States

### Feature Name

Application Loading UX

### Task Description

Add appropriate loading states throughout the application.

### Acceptance Criteria

Loading states exist for:

* Page loading
* Task creation
* Plan generation
* Replanning
* Post-MVP AI operations, when introduced
* Calendar operations

### Dependencies

FT-029, FT-039

### Priority

**Must-Have**

### AI Coding Prompt

> Add polished loading states throughout Flowlly. Prioritize Today, plan generation, “I'm Awake,” replanning, and task mutations. Add AI-operation loading states only when those post-MVP capabilities are introduced. Use skeletons or meaningful progress indicators rather than generic spinners where appropriate.

---

## FT-082 — Empty States

### Feature Name

Empty State System

### Task Description

Create helpful empty states for users who have not created tasks, events, projects, or plans.

### Acceptance Criteria

* Empty states explain what is happening.
* Each relevant empty state provides a next action.
* Empty states match the Stitch visual language.
* Empty states do not feel like errors.

### Dependencies

FT-003, FT-029

### Priority

**Must-Have**

### AI Coding Prompt

> Implement reusable Flowlly empty-state components for tasks, schedule, projects, goals, and plans. Each state should explain the situation and provide an appropriate next action.

---

## FT-083 — Error and Recovery States

### Feature Name

Recovery UX

### Task Description

Design and implement recovery experiences for failed operations.

### Acceptance Criteria

* Failed plan generation is recoverable.
* Failed replanning is recoverable.
* AI failure does not break core scheduling.
* Network errors provide retry options.
* Users do not lose existing schedules because of a failed operation.

### Dependencies

FT-068, FT-038

### Priority

**Must-Have**

### AI Coding Prompt

> Implement resilient recovery UX throughout Flowlly. Ensure failed scheduling, replanning, AI, and network operations preserve the user's existing valid plan and provide clear retry or recovery actions.

---

# 39. EPIC AJ — Final Core Flow

---

## FT-084 — Complete Adaptive Day Flow

### Feature Name

Adaptive Daily Planning

### Task Description

Connect all MVP systems into the complete adaptive-day experience.

The final flow is:

```text
User
 ↓
Login
 ↓
Onboarding
 ↓
Create Tasks
 ↓
Create Fixed Events
 ↓
Generate Day
 ↓
View Today
 ↓
I'm Awake
 ↓
Actual Wake Time
 ↓
Replanning
 ↓
Review Changes
 ↓
Accept Plan
 ↓
Execute Tasks
 ↓
Complete / Skip
 ↓
Optional Replan
```

### Acceptance Criteria

* All steps work together.
* User data persists correctly.
* Fixed events remain protected.
* Late wake-up causes appropriate replanning.
* Previous plans remain recoverable.
* UI remains responsive.
* Errors do not destroy valid plan state.
* Core journey passes E2E tests.

### Dependencies

FT-075, FT-081, FT-082, FT-083

### Priority

**Must-Have**

### AI Coding Prompt

> Integrate the complete Flowlly adaptive-day experience. Verify that authentication, onboarding, tasks, fixed events, daily planning, Today, “I'm Awake,” wake recording, adaptive replanning, plan review, plan acceptance, task execution, and error recovery work together as one coherent product. Fix integration issues without introducing unnecessary architectural coupling.

---

## FT-085 — Plan Item State Model

### Feature Name

Plan Item Lifecycle States

### Task Description

Implement the plan item states defined in `decisions.md`.

### Acceptance Criteria

* Plan items support `planned`, `in_progress`, `completed`, `skipped`, `missed`, `protected`, and `superseded`.
* State transitions preserve history and cannot silently convert a skipped or missed item into a completed item.
* Fixed and locked items are represented as protected.
* Replanning applies the defined missed, in-progress, and superseded rules.

### Dependencies

FT-021, FT-026

### Priority

**Must-Have**

### AI Coding Prompt

> Implement the plan item lifecycle states exactly as defined in `decisions.md`. Keep transitions explicit, preserve history, and add tests for replanning state changes.

---

## FT-086 — Manual Plan Block Editing

### Feature Name

Flexible Plan Block Editing

### Task Description

Allow users to move, resize, and delete flexible plan blocks manually.

### Acceptance Criteria

* Users can move, resize, and delete flexible blocks.
* Protected fixed or locked items cannot be changed through flexible-block controls.
* Every edit is checked by the deterministic validator before the server persists it.
* Valid edits are shown to the user and recorded as a new plan version.

### Dependencies

FT-022, FT-026, FT-029

### Priority

**Must-Have**

### AI Coding Prompt

> Implement manual move, resize, and delete operations for flexible plan blocks. Validate each proposed change deterministically, protect fixed and locked items, and persist accepted edits as a new plan version.

---

## FT-087 — Undo Last Replan

### Feature Name

Restore Previous Active Plan

### Task Description

Allow the user to undo the latest accepted replan by restoring the previous active plan version.

### Acceptance Criteria

* Undo restores the immediately previous active version without deleting plan history.
* The restored version becomes active and its state changes are consistent with the current time.
* The operation is authorized for the session user, idempotent, and auditable.
* The UI confirms the result and provides recovery feedback on failure.

### Dependencies

FT-028, FT-041

### Priority

**Must-Have**

### AI Coding Prompt

> Implement undo for the latest accepted replan by reactivating the prior plan version. Preserve version history, enforce session ownership, make retries safe, and cover state behavior with tests.

---

## FT-088 — Privacy-Safe Analytics Event Tracking

### Feature Name

Product Event Tracking

### Task Description

Implement the event list defined in `implementation_plan.md` §47 without collecting sensitive content.

### Acceptance Criteria

* Track only the documented product events.
* Events contain no task titles, descriptions, event names, prompt text, or other sensitive user content.
* Event access and retention follow the security and privacy rules.
* Analytics failure does not block product actions.

### Dependencies

FT-071

### Priority

**Should-Have**

### AI Coding Prompt

> Implement privacy-safe tracking for the event names in `implementation_plan.md` §47. Do not include sensitive user content, and ensure tracking failures never block core product actions.

---

## FT-089 — Scheduler Specification Tests

### Feature Name

Scheduler Scenario Fixtures

### Task Description

Create fixtures for every critical scheduler case in TRD §76 and implementation plan §45.

### Acceptance Criteria

* Every documented case has a deterministic input fixture and expected result.
* Fixtures cover normal and late wake, fixed commitments, deadlines, impossible schedules, manual locks, completed work, and AI/network failures.
* Tests assert 15-minute scheduling granularity and deterministic tie-breaking from `decisions.md`.
* Tests assert the defined plan item states and in-progress/missed behavior.

### Dependencies

FT-024, FT-038

### Priority

**Must-Have**

### AI Coding Prompt

> Add scheduler specification fixtures for every case in TRD §76 and implementation plan §45. Assert deterministic output, 15-minute granularity, tie-breaking, constraint behavior, and plan-item transitions; do not change product rules to fit a test.

---

# 40. Launch Priority Summary

Must-have tickets implement or verify the MVP in `decisions.md`; they are not permission to expand product scope.

## Must-Have for MVP

```text
FT-001  Project Foundation
FT-002  Design Tokens
FT-003  Core UI Components
FT-004  PostgreSQL
FT-005  User Model
FT-006  Authentication
FT-007  Registration
FT-008  Login
FT-009  Logout
FT-010  Onboarding
FT-011  User Preferences

FT-012  Task Model
FT-013  Create Task
FT-014  Edit Task
FT-015  Complete Task
FT-016  Delete/Archive Task

FT-017  Calendar Event Model
FT-018  Create Event
FT-019  Edit Event

FT-020  Availability
FT-021  Scheduler Domain
FT-022  Hard Constraints
FT-023  Task Scoring
FT-024  Schedule Generator

FT-026  Daily Plan
FT-027  Generate Today's Plan
FT-028  Plan Versioning

FT-029  Today
FT-030  Current Task
FT-032  Start Task
FT-033  Skip Task

FT-034  Wake Event
FT-035  I'm Awake
FT-036  Confirm Actual Wake Time
FT-037  Replanning Domain
FT-038  Replanning Engine
FT-039  Wake Replanning
FT-040  Replan Summary
FT-041  Accept Plan
FT-042  Keep Current Plan
FT-043  Manual Replan

FT-048  In-App Reminder Model
FT-049  In-App Task Reminder
FT-056  Schedule Locking
FT-057  Simple Daily/Weekly Tasks
FT-058  Conflict Detection
FT-066  Mobile Experience
FT-067  Accessibility
FT-068  Error Handling
FT-069  Authorization
FT-070  Rate Limiting
FT-071  Logging
FT-072  Replanning Diagnostics

FT-073  Scheduler Tests
FT-074  API Tests
FT-075  E2E Tests

FT-078  Environment Configuration
FT-079  Database Backup
FT-080  Production Deployment

FT-081  Loading States
FT-082  Empty States
FT-083  Recovery UX
FT-084  Complete Adaptive Day Flow
FT-085  Plan Item State Model
FT-086  Manual Plan Block Editing
FT-087  Undo Last Replan
FT-089  Scheduler Specification Tests
```

---

# 41. Should-Have After Core MVP

```text
FT-025  Task Splitting
FT-031  Day Progress
FT-044  AI Service Abstraction
FT-045  AI Planning Proposal
FT-046  AI Explanations
FT-047  Natural Language Tasks
FT-076  Google Stitch Screens
FT-077  Stitch → Production UI
FT-051  Projects
FT-052  Project UI
FT-053  Goals
FT-054  Goals UI
FT-055  Task Dependencies
FT-059  Conflict Resolution UI
FT-062  Completion Analytics
FT-088  Privacy-Safe Analytics Event Tracking
```

---

# 42. Nice-to-Have

```text
FT-050  Replanning Notifications
FT-060  Calendar Adapter
FT-061  Google Calendar
FT-063  Planning Accuracy
FT-064  Behavior History
FT-065  Personalized Scheduling
```

---

# 43. Suggested AI Coding Workflow

Each ticket should be executed using the following workflow:

```text
1. Select ONE ticket
        ↓
2. Read the ticket
        ↓
3. Check dependencies
        ↓
4. Inspect existing implementation
        ↓
5. Implement only the requested scope
        ↓
6. Run type checking
        ↓
7. Run linting
        ↓
8. Run relevant tests
        ↓
9. Manually verify the feature
        ↓
10. Update documentation if required
        ↓
11. Commit changes
        ↓
12. Move to next ticket
```

---

# 44. Rules for AI Coding Agents

When implementing any Flowlly ticket, the coding agent should follow these rules:

### Rule 1 — Respect Dependencies

Do not implement a ticket by bypassing unfinished dependencies.

### Rule 2 — Do Not Rewrite Existing Architecture

Reuse existing abstractions unless there is a documented reason to change them.

### Rule 3 — No Business Logic in UI

Flutter widgets should render state and trigger actions.

Scheduling logic belongs in the scheduling domain.

### Rule 4 — AI Does Not Control the Database

AI output must pass through schema validation and deterministic business rules.

### Rule 5 — Preserve User Control

AI suggestions must not silently overwrite user decisions.

### Rule 6 — Protect Fixed Commitments

Fixed events and locked items must be treated as hard constraints.

### Rule 7 — Preserve Plan History

Never destroy a previous valid plan during replanning.

### Rule 8 — Test Scheduling Logic

Every scheduling-rule change should have unit tests.

### Rule 9 — Respect Timezones

Never assume UTC represents the user's local day.

### Rule 10 — Keep Tickets Small

Implement one ticket at a time.

### Rule 11 — Do Not Add Unrequested Features

Avoid scope creep.

### Rule 12 — Follow Stitch, Don't Fight It

The production UI should preserve the approved visual hierarchy and interaction model established in Google Stitch while translating it into maintainable Flutter widgets.

---

# 45. Critical Product Flow

The most important feature in the entire application is:

```text
                USER'S REAL LIFE
                      │
                      ▼
              "I'm Awake"
                      │
                      ▼
              Actual Wake Time
                      │
                      ▼
             Current Day State
                      │
          ┌───────────┴───────────┐
          │                       │
     Fixed Events            Flexible Tasks
          │                       │
          │                 Re-prioritize
          │                       │
          └───────────┬───────────┘
                      ▼
              Scheduling Engine
                      │
                      ▼
                Valid Plan
                      │
                      ▼
       Optional post-MVP AI Explanation
                      │
                      ▼
             User Reviews Changes
                      │
              ┌───────┴───────┐
              ▼               ▼
          Accept            Keep
              │               │
              └───────┬───────┘
                      ▼
                Execute Day
```

This flow should receive the highest engineering and UX attention because it is the feature that differentiates Flowlly from a conventional calendar/task manager.

---

# 46. Definition of MVP Completion

Flowlly MVP is ready for initial users when the full product scope in `decisions.md` is implemented. The following are supporting release checks, not additional product scope:

```text
[✓] User can create an account
[✓] User can complete onboarding
[✓] User can define sleep/wake preferences
[✓] User can create tasks
[✓] User can create fixed events
[✓] Every capability in `decisions.md` passes its ticket acceptance criteria
[✓] Session-scoped ownership is enforced for user-owned data
[✓] Scheduler specification and core E2E tests pass
[✓] Error and empty states are recoverable and accessible
[✓] Production deployment works
```

---

# 47. Final Build Principle

Flowlly should be built in this order:

```text
FOUNDATION
     ↓
DATA
     ↓
TASKS + EVENTS
     ↓
DETERMINISTIC SCHEDULING
     ↓
DAILY PLAN
     ↓
TODAY EXPERIENCE
     ↓
"I'M AWAKE"
     ↓
ADAPTIVE REPLANNING
     ↓
AI
     ↓
NOTIFICATIONS
     ↓
PROJECTS + GOALS
     ↓
PERSONALIZATION
     ↓
INTEGRATIONS
```

The **deterministic scheduling engine must exist before AI becomes responsible for planning suggestions**.

The AI should make Flowlly smarter, but the scheduling engine should make Flowlly reliable.
