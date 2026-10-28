# Flowlly — Implementation Plan

> **Relationship to other documents:** `decisions.md` is authoritative for MVP scope and architecture. This plan must defer to it wherever scope or architecture differs.

> **Product:** Flowlly
> **Type:** Adaptive life scheduling planner
> **Goal:** Build a production-ready scheduling app that continuously adapts a user's day around commitments, tasks, wake time, priorities, and available time.

---

## 1. Implementation Philosophy

Flowlly should not behave like a traditional calendar.

A traditional calendar answers:

> “What events do I have?”

Flowlly should answer:

> “Given everything happening today, what should I do now?”

The implementation must therefore separate:

1. **Source data**

   * Tasks
   * Events
   * Goals
   * Preferences
   * Wake/sleep information

2. **Scheduling logic**

   * Availability
   * Constraints
   * Priorities
   * Deadlines
   * Duration
   * Dependencies

3. **AI reasoning**

   * Understand intent
   * Explain changes
   * Suggest adjustments
   * Generate planning proposals

4. **Canonical schedule**

   * The actual plan shown to the user

AI must never be allowed to directly and blindly mutate the canonical schedule.

The deterministic scheduling layer validates every AI-generated proposal before it becomes an actual plan.

---

# 2. Product Architecture

## 2.1 High-Level Architecture

```text
┌─────────────────────────────────────────────┐
│                  Flowlly UI                 │
│                                             │
│ Today │ Schedule │ Tasks │ Projects │ Goals │
└──────────────────────┬──────────────────────┘
                       │
                       ▼
┌─────────────────────────────────────────────┐
│              Application Layer              │
│                                             │
│ Auth │ Tasks │ Events │ Plans │ Notifications│
└──────────────────────┬──────────────────────┘
                       │
             ┌─────────┴─────────┐
             ▼                   ▼
┌──────────────────────┐ ┌────────────────────┐
│ Deterministic        │ │ AI Planning Layer  │
│ Scheduling Engine    │ │                    │
│                      │ │ Proposal generation│
│ Constraints          │ │ Explanation        │
│ Availability         │ │ Suggestions        │
│ Priorities           │ │ Natural language   │
└──────────┬───────────┘ └─────────┬──────────┘
           │                       │
           └───────────┬───────────┘
                       ▼
              ┌─────────────────┐
              │ Plan Validator  │
              └────────┬────────┘
                       │
                       ▼
              ┌─────────────────┐
              │ PostgreSQL DB   │
              └─────────────────┘
```

---

# 3. Recommended Technology Stack

## Client

* Flutter and Dart
* Material 3 with the custom Flowlly system in `design.md`
* Riverpod for state management and dependency injection
* GoRouter for navigation and route guards
* `supabase_flutter` for authenticated Supabase access
* Freezed and `json_serializable` for immutable models and serialization
* `intl` and `timezone` for local-time and timezone handling

## Backend and database

Use Supabase Auth, Supabase-hosted PostgreSQL with Row Level Security (RLS), versioned Supabase CLI migrations, and TypeScript/Deno Supabase Edge Functions. Keep privileged operations and the authoritative scheduler/validator server-side. Do not put scheduling rules exclusively in Flutter widgets or client code.

## Authentication and authorization

Use Supabase Auth with email/password. Enable and test RLS policies on every user-owned table. Edge Functions must authenticate requests and preserve user authorization; never trust a client-provided user ID. Use privileged service credentials only in server-side functions when strictly necessary.

## AI

Use the OpenAI API only through authenticated, rate-limited Supabase Edge Functions. The function reads `OPENAI_API_KEY` from Supabase secrets; the Flutter app must never receive or store that key. AI output is untrusted proposal data and must pass schema and deterministic scheduler validation before the server can persist an accepted change.

## Notifications

Start with basic in-app reminders only; push and email notifications are post-MVP.

---

# 4. Repository Structure

Use the canonical Flutter and Supabase structure from `decisions.md`:

```text
lib/                              # Flutter application source
├── app/                          # App entry point, router, theme
├── core/                         # Shared errors, time, validation, widgets
│   └── widgets/                  # Shared accessible UI primitives
├── features/                     # Auth, onboarding, Today, schedule, tasks
├── domain/                       # Client-side domain models and interfaces
└── data/                         # Client repositories and data sources
supabase/
├── migrations/                   # Versioned database migrations
└── functions/
    ├── _shared/scheduler/        # Deterministic scheduler and validator
    └── <function-name>/          # Authenticated TypeScript/Deno functions
test/
integration_test/
```

---

# 5. Phase 0 — Project Foundation

## Goal

Create a clean, secure, production-ready foundation before implementing product functionality.

### Tasks

1. Initialize the Flutter project and Supabase backend.
2. Configure Dart analysis, formatting, Riverpod, and GoRouter.
3. Configure Supabase Auth and the Flutter client.
4. Configure RLS and versioned Supabase CLI migrations.
5. Scaffold authenticated Edge Functions and server-only scheduler boundaries.
6. Configure Flutter unit, widget, and integration tests.
7. Configure CI checks.

### Acceptance Criteria

* Project starts locally.
* Dart analysis and formatting checks pass.
* Tests execute.
* Database connection works.
* Environment variables are documented.
* Secrets are not committed.

---

# 6. Phase 1 — Design System

Flowlly must use the supplied `design.md` as the visual source of truth.

The UI must not look like a generic AI-generated SaaS dashboard.

## Visual Direction

```text
Porcelain canvas
        ↓
White surfaces
        ↓
Subtle glass surfaces
        ↓
Ink typography
        ↓
Single magenta action color
        ↓
Soft depth
```

## Core characteristics

* Minimal
* Editorial
* Calm
* Spacious
* Slightly glassmorphic
* Product-focused
* High typography quality
* Few colors
* No excessive gradients
* No excessive shadows
* No dashboard-card overload

## Glassmorphism Rules

Glass should be used selectively.

Good:

```css
background: rgba(255,255,255,.58);
backdrop-filter: blur(20px);
border: 1px solid rgba(255,255,255,.72);
```

Avoid:

* Excessive transparency
* Strong blur everywhere
* Neon glass
* Rainbow gradients
* Glass buttons everywhere
* Cards stacked inside cards

Glass should communicate hierarchy, not decoration.

## Implementation

Create reusable tokens:

```text
colors
typography
spacing
radii
shadows
glass surfaces
buttons
inputs
cards
badges
navigation
timeline
```

The core colors must match `design.md` and `development-status.md`: canvas `#F5F6F7`, ink `#151618`, accent `#C85BD3`.

---

# 7. Phase 2 — Authentication

## Features

* Register
* Login
* Logout
* Password reset
* Session persistence
* Protected routes

Optional:

* OAuth providers are post-MVP.

## Rules

Every protected request must identify the authenticated user.

Every database query involving user-owned data must verify ownership.

Never rely solely on frontend route protection.

---

# 8. Phase 3 — User Profile and Preferences

Create:

```text
User
UserPreferences
```

Preferences should include:

```text
name
timezone
wake_time
sleep_time
default_day_start
default_day_end
planning_style
preferred_focus_duration
break_preference
notification_preferences
```

Later:

```text
energy_patterns
work_preferences
calendar_preferences
```

---

# 9. Phase 4 — Task System

## Task Model

Minimum fields:

```text
id
user_id
title
description
status
priority
estimated_duration
deadline
preferred_start
preferred_end
is_flexible
splittable
project_id
created_at
updated_at
completed_at
```

## Statuses

```text
TODO
IN_PROGRESS
COMPLETED
CANCELLED
```

## Priority

```text
LOW
MEDIUM
HIGH
URGENT
```

## User Actions

Users can:

* Create task
* Edit task
* Complete task
* Delete task
* Set duration
* Set deadline
* Set priority
* Mark task flexible
* Add preferred time
* Assign project

---

# 10. Phase 5 — Calendar / Commitment System

Flowlly must distinguish between:

### Fixed commitments

Examples:

* University class
* Meeting
* Doctor appointment
* Flight
* Dinner reservation

### Flexible activities

Examples:

* Study
* Gym
* Reading
* Coding
* Assignment
* Personal project

Fixed commitments are protected during replanning.

Flexible activities may move.

---

# 11. Phase 6 — Daily Planning Engine

This is the core of Flowlly.

Create the authoritative deterministic scheduler in `supabase/functions/_shared/scheduler/` (see `decisions.md`).

```text
supabase/functions/_shared/scheduler/
├── availability/
├── constraints/
├── conflict/
├── planning/
├── replanning/
├── scoring/
├── types/
└── validation/
```

---

# 12. Scheduling Inputs

The planner receives:

```text
date
current_time
wake_time
sleep_time
fixed_events
tasks
deadlines
priorities
durations
dependencies
preferences
available_time
completed_items
remaining_items
```

---

# 13. Scheduling Constraints

## Hard Constraints

The planner must never violate:

* Fixed events
* Overlapping commitments
* User sleep boundaries
* Impossible time ranges
* Task dependencies
* Completed tasks
* Invalid dates

## Soft Constraints

The planner should attempt to optimize:

* Priority
* Deadline proximity
* Preferred time
* Focus periods
* Breaks
* User preferences
* Task continuity
* Reasonable workload

---

# 14. Scheduling Algorithm

Initial deterministic algorithm:

```text
1. Load user's day
2. Load fixed commitments
3. Determine current time
4. Determine available windows
5. Preserve completed items as completed; do not schedule them again
6. Keep in-progress items until their end unless explicitly skipped
7. Mark ended, incomplete items as missed
8. Preserve protected fixed and locked items
9. Calculate remaining workload
10. Rank tasks
11. Place highest-priority tasks
12. Respect deadlines
13. Insert breaks
14. Apply the default 10-minute transition buffer
15. Validate schedule at 15-minute granularity
16. Produce the deterministic plan
```

Break ties by higher score, earlier deadline, earlier creation time, then task ID lexicographically, as defined in `decisions.md`. Task splitting is post-MVP.

Do not initially attempt sophisticated machine learning.

A reliable constraint-based engine is more valuable than an unpredictable "AI scheduler."

---

# 15. The “I'm Awake” Feature

This is one of Flowlly's defining features.

## Flow

```text
User opens Flowlly
        ↓
Today screen
        ↓
"I'm Awake"
        ↓
Record actual wake time
        ↓
Determine missed blocks
        ↓
Protect fixed commitments
        ↓
Recalculate remaining availability
        ↓
Run scheduling engine
        ↓
Optional AI explanation
        ↓
Show proposed plan
        ↓
User accepts
        ↓
New plan becomes active
```

---

# 16. Wake Event

Create:

```text
WakeEvent
```

Fields:

```text
id
user_id
date
planned_wake_time
actual_wake_time
created_at
```

The system must preserve wake history.

This allows future insights such as:

```text
Your average wake time this week was 10:42 AM.
```

---

# 17. Replanning Engine

Create:

```text
supabase/functions/_shared/scheduler/replanning/
```

Input:

```text
current_time
wake_time
remaining_tasks
remaining_events
completed_tasks
user_preferences
```

Output:

```text
PlanProposal
```

Example:

```json
{
  "date": "2026-09-19",
  "blocks": [
    {
      "type": "task",
      "taskId": "task_123",
      "start": "13:00",
      "end": "14:00"
    }
  ]
}
```

---

# 18. Plan Versioning

Never overwrite the previous plan blindly.

Use:

```text
Plan
PlanVersion
```

Every major replanning event creates a new version.

Example:

```text
Plan Version 1
      ↓
User wakes late
      ↓
Plan Version 2
      ↓
User accepts
```

This enables:

* Rollback
* History
* Debugging
* Analytics
* User trust

---

# 19. AI Planning Layer

AI is an assistant to the scheduling engine.

It should not become the scheduling engine.

## AI responsibilities

AI can:

* Interpret natural language
* Explain scheduling decisions
* Suggest changes
* Convert vague goals into tasks
* Summarize the day
* Suggest priorities
* Help break down projects

## AI should not independently:

* Delete user data
* Modify fixed commitments
* Bypass validation
* Access another user's data
* Execute arbitrary database queries

---

# 20. AI Server Proxy

All AI requests must pass through an authenticated Supabase Edge Function.

Example:

```text
POST /functions/v1/ai-plan
```

Architecture:

```text
Client
  ↓
Supabase Edge Function: ai-plan
  ↓
Authenticate user
  ↓
Validate request
  ↓
Apply rate limit
  ↓
Build controlled prompt
  ↓
Call OpenAI API using server-side OPENAI_API_KEY
  ↓
Validate response
  ↓
Return structured result
```

Never expose:

`OPENAI_API_KEY` is stored only in Supabase Edge Function secrets. Never bundle it in Flutter or expose it to the client.

---

# 21. AI Output Format

Prefer structured JSON instead of free-form output.

Example:

```json
{
  "summary": "Your afternoon was compressed because you woke later than planned.",
  "changes": [
    {
      "type": "MOVE_TASK",
      "task_id": "task_123",
      "reason": "Moved into the next available focus block."
    }
  ]
}
```

Treat this structured response as a proposal. Validate its schema, pass any schedule changes through the deterministic scheduler, and let the server persist only valid changes after applicable user confirmation. Validate the response schema with Zod before use.

---

# 22. AI Token Controls

Every AI request must have:

* Maximum input size
* Maximum output tokens
* Timeout
* Per-user rate limit
* Per-user usage tracking
* Cost monitoring

Example policy:

```text
AI planning:
10 requests/minute/user

Maximum output:
controlled per endpoint

Maximum prompt:
strict character/token limit
```

OpenAI limits should be configured centrally rather than hardcoded throughout the application.

---

# 23. Today Screen

Today is the primary product surface.

It should answer:

> “What should I do right now?”

## Structure

```text
Greeting
Current time
Day progress

NOW
Current activity

NEXT
Next activity

LATER
Upcoming important activities

Today's remaining workload

I'm Awake / Replan

Timeline
```

Avoid:

* Dense analytics
* Huge card grids
* Excessive charts
* Chatbot-first UI

---

# 24. Schedule Screen

Provide:

* Day timeline
* Week timeline
* Fixed events
* Flexible tasks
* Completed tasks
* Current-time indicator

Users should be able to:

* Open a task
* Open an event
* Move flexible blocks
* Inspect why a block exists

---

# 25. Task Screen

Provide:

```text
All
Today
Upcoming
Completed
```

Task creation should remain fast.

A user should be able to create:

```text
"Finish database assignment"
```

without filling ten fields.

Advanced properties should be progressive disclosure.

---

# 26. Projects

Projects group related tasks.

Example:

```text
Website
├── Research
├── Design
├── Development
└── Deployment
```

Projects are not required for the first minimal release but should be architected into the data model.

---

# 27. Goals

Goals represent longer-term outcomes.

Example:

```text
Goal:
Build portfolio

Projects:
Portfolio website
Open-source project
Resume

Tasks:
...
```

Goals should not directly control scheduling.

They influence prioritization through associated tasks.

---

# 28. Notifications

Start with:

* Task reminder
* Upcoming event
* Current task reminder
* Missed schedule notification
* Replan notification

Do not spam users.

Notification preferences must be configurable.

---

# 29. Insights

Insights should be based on actual behavior.

Examples:

```text
Average wake time
Task completion rate
Planned vs actual time
Rescheduling frequency
Most productive periods
Overloaded days
```

Avoid judgmental language.

Prefer:

> “You completed most focus sessions between 2 PM and 6 PM.”

Instead of:

> “You are unproductive in the morning.”

---

# 30. Error Handling

Every major operation must have a defined state.

## Loading

Show appropriate loading UI.

## Empty

Provide useful next action.

Example:

```text
No tasks yet.

Add your first task and Flowlly
will help place it into your day.
```

## Validation

Show inline validation.

## Network Error

```text
We couldn't connect right now.
Your existing plan is still available.
```

## AI Error

```text
AI planning is temporarily unavailable.
Your current schedule has not been changed.
```

## Replanning Error

Never destroy the current plan.

```text
We couldn't generate a new plan.
Your current plan is unchanged.
```

---

# 31. Security Implementation

Follow `security_rules.md` as a mandatory engineering contract.

Minimum implementation:

* Environment variables
* Authentication
* Authorization
* Ownership checks
* Server-side validation
* Rate limiting
* Secure cookies
* HTTP security headers
* Restricted CORS
* Safe database queries
* AI server proxy
* AI output validation
* Generic production errors
* Audit logging
* Dependency auditing

---

# 32. Database Security

Every user-owned table must include:

```text
user_id
```

unless ownership is inherited through a parent relation.

Every query must enforce ownership.

Conceptually:

```text
WHERE user_id = authenticated_user_id
```

Never trust:

```text
user_id
```

provided by the client.

The authenticated session determines ownership.

---

# 33. API Design

Use predictable endpoints.

Flutter reads and writes user-owned records through the authenticated Supabase client under RLS. Call Edge Functions for server-authoritative operations:

```text
generate-plan
replan-day
ai-plan
ai-explain
```

Edge Functions must authenticate the user, validate inputs, enforce rate limits where relevant, and preserve RLS authorization. Do not create redundant functions for ordinary database CRUD.

---

# 34. API Response Standard

Use consistent responses.

Success:

```json
{
  "data": {},
  "error": null
}
```

Error:

```json
{
  "data": null,
  "error": {
    "code": "TASK_NOT_FOUND",
    "message": "Task not found."
  }
}
```

Never expose:

* Stack traces
* SQL errors
* API keys
* Internal provider errors
* Sensitive implementation details

---

# 35. State Management

Use Riverpod for shared application state and dependency injection. Keep local widget state local and use Flutter form controls for form state.

```text
Shared application state → Riverpod
Local presentation state → widget-local state
Form state → Flutter form APIs
```

Do not put unrelated application state into one global provider.

---

# 36. Optimistic UI

Use optimistic updates for low-risk actions:

* Complete task
* Toggle task status
* Mark notification read

Do not use optimistic updates for high-risk operations such as:

* Full replanning
* Bulk deletion
* Account changes

---

# 37. Replanning UX

When the user requests replanning:

```text
User action
     ↓
Replanning state
     ↓
Analyze commitments
     ↓
Optimize remaining time
     ↓
Validate new plan
     ↓
Show changes
```

Show what changed.

Example:

```text
Your plan changed

Moved:
Database Assignment
2:00 PM → 3:30 PM

Protected:
Database Class
5:00 PM

Removed:
Optional reading session
```

---

# 38. Plan Acceptance

The user should understand significant changes before they become active.

Buttons:

```text
Accept New Plan
Keep Current Plan
```

For minor automatic adjustments, configurable auto-accept behavior can be introduced later.

---

# 39. Undo / Rollback

Every major plan change should support rollback.

Example:

```text
Plan updated

Undo
```

Rollback should restore the previous active plan version.

---

# 40. Accessibility

Required:

* Keyboard navigation
* Visible focus states
* Semantic HTML
* Accessible labels
* Sufficient contrast
* Reduced motion support
* Screen-reader-friendly controls
* No interaction dependent solely on color

---

# 41. Responsive Design

Desktop:

```text
Sidebar
Content
Optional secondary panel
```

Mobile:

```text
Top context
Primary content
Bottom navigation
```

Use the breakpoints shared with `design.md` and the TRD:

```text
Mobile: < 640px
Tablet: 640px–<1024px
Desktop: 1024px–1440px
Large: > 1440px
```

The Today screen must remain useful on small screens.

The primary action should always be easy to reach.

---

# 42. Performance

Prioritize:

* Server rendering where appropriate
* Small client bundles
* Lazy loading
* Optimized icons
* Optimized images
* Minimal third-party JavaScript
* Database indexes
* Efficient queries

Avoid:

* Huge client-side state
* Fetching all tasks unnecessarily
* Rendering every historical plan
* Loading AI SDKs into the Flutter client

---

# 43. Database Indexes

Index common query paths.

Examples:

```text
tasks(user_id, status)
tasks(user_id, deadline)
tasks(user_id, created_at)

events(user_id, start_time)
events(user_id, end_time)

plans(user_id, date)

plan_versions(plan_id, created_at)

wake_events(user_id, date)
```

Add indexes based on actual query patterns as the application grows.

---

# 44. Testing Strategy

## Unit Tests

Test:

* Task prioritization
* Availability calculation
* Schedule placement
* Conflict detection
* Task splitting
* Replanning
* Plan validation

## Integration Tests

Test:

* Authentication
* Task CRUD
* Event CRUD
* Plan generation
* Wake event flow
* Replanning
* Database ownership

## E2E Tests

Critical journey:

```text
Register
 ↓
Onboarding
 ↓
Create event
 ↓
Create tasks
 ↓
Open Today
 ↓
Wake
 ↓
Replan
 ↓
Accept plan
 ↓
Complete task
```

---

# 45. Critical Scheduler Test Cases

The following scenarios must pass.

### Case 1 — Normal Day

User wakes on time.

Expected:

* Existing plan remains valid.
* No unnecessary replanning.

### Case 2 — Late Wake

User wakes several hours late.

Expected:

* Wake event recorded.
* Missed flexible blocks removed.
* Fixed commitments protected.
* Remaining tasks rescheduled.

### Case 3 — Late Wake Before Fixed Event

Expected:

* Fixed event remains unchanged.
* Available time before event is recalculated.

### Case 4 — Completed Task

Expected:

* Completed task cannot be scheduled again.

### Case 5 — Deadline

Expected:

* Deadline-aware prioritization.

### Case 6 — Impossible Schedule

Expected:

* System does not create overlapping blocks.
* User receives a clear explanation.

### Case 7 — AI Failure

Expected:

* Existing plan remains intact.

### Case 8 — Network Failure During Replan

Expected:

* Existing plan remains intact.
* User can retry.

### Case 9 — Duplicate Request

Expected:

* Replanning request should not accidentally create duplicate active plans.

### Case 10 — Unauthorized Access

Expected:

* User cannot access another user's task, event, or plan.

---

# 46. Observability

Track:

```text
API latency
Error rate
AI request count
AI token usage
AI failures
Planner failures
Database latency
Authentication failures
Rate-limit events
```

Do not log sensitive user content unnecessarily.

---

# 47. Analytics

Track product behavior carefully.

Useful events:

```text
task_created
task_completed
task_deleted
event_created
wake_recorded
replan_started
replan_completed
replan_rejected
plan_accepted
plan_rolled_back
notification_opened
```

Do not collect data that is not needed.

---

# 48. Feature Flags

Create a lightweight feature-flag mechanism for risky features.

Examples:

```text
AI_PLANNER_ENABLED
AUTO_REPLAN_ENABLED
INSIGHTS_ENABLED
GOALS_ENABLED
CALENDAR_INTEGRATION_ENABLED
```

This allows controlled rollout.

---

# 49. Development Order

Implement in the order of the tickets in `feature_ticket_list.md`, respecting each ticket's dependencies. The MVP sequence is:

```text
FT-001–FT-011: foundation, design, database, authentication, onboarding, preferences
FT-012–FT-024: tasks, events, availability, deterministic scheduler
FT-026–FT-043: daily plans, Today, execution, wake capture, replanning, plan review
FT-048–FT-049: in-app reminder model and delivery
FT-056–FT-057: schedule locking and simple daily/weekly recurrence
FT-058: conflict detection
FT-066–FT-084: responsive/accessibility support, error states, authorization, tests, release readiness
FT-085–FT-087, FT-089: plan states, manual block editing, undo, scheduler specification tests
```

Do not implement AI before the deterministic planner works.

---

# 50. MVP Definition

The MVP is exactly the scope in `decisions.md`. Do not use this plan to add or remove MVP capabilities.

---

# 51. Phase 1 Release Scope

Release scope follows `decisions.md`; the implementation tickets in `feature_ticket_list.md` classify work needed to deliver that scope.

---

# 52. AI Development Rules

AI coding agents working on this repository must:

1. Read `prd.md`.
2. Read `design.md`.
3. Read `app_flow.md`.
4. Read `security_rules.md`.
5. Read `implementation_plan.md`.
6. Check existing code before creating new files.
7. Reuse existing components.
8. Never duplicate functionality unnecessarily.
9. Never bypass authentication.
10. Never expose secrets.
11. Never create insecure database queries.
12. Never modify the scheduling engine without tests.
13. Never introduce a new dependency without justification.
14. Keep changes scoped to the requested feature.
15. Run relevant tests after implementation.
16. Run type checking.
17. Run linting.
18. Do not silently change product behavior.
19. Do not replace deterministic scheduling with an LLM.
20. Never assume AI-generated output is trusted.

---

# 53. AI Coding Prompt Contract

Every coding task should follow:

```text
CONTEXT
What part of Flowlly is being changed?

GOAL
What should be implemented?

FILES
Which files are expected to change?

CONSTRAINTS
What must not change?

SECURITY
What security requirements apply?

ACCEPTANCE CRITERIA
How do we know the feature is complete?

TESTS
What tests must be added or updated?
```

---

# 54. Example AI Coding Task

```text
CONTEXT

Flowlly's deterministic scheduling engine already supports
fixed events and flexible tasks.

GOAL

Implement the "I'm Awake" replanning flow.

REQUIREMENTS

1. Record the user's actual wake time.
2. Identify flexible schedule blocks that are now in the past.
3. Never move fixed commitments.
4. Recalculate remaining available windows.
5. Reschedule remaining flexible tasks.
6. Validate the resulting schedule.
7. Create a new plan version.
8. Do not activate the plan until the user accepts it.

SECURITY

The wake event must belong to the authenticated user.

ACCEPTANCE CRITERIA

- Wake time is persisted.
- Existing plan remains unchanged until acceptance.
- No fixed events move.
- No overlapping blocks are generated.
- Completed tasks are not scheduled.
- Previous plan can be restored.
- Tests cover late wake scenarios.

TESTS

Add unit and integration tests for the above cases.
```

---

# 55. Deployment Pipeline

Recommended pipeline:

```text
Pull Request
     ↓
Install dependencies
     ↓
Type check
     ↓
Lint
     ↓
Unit tests
     ↓
Integration tests
     ↓
Build
     ↓
Security checks
     ↓
Deploy preview
     ↓
Manual verification
     ↓
Production
```

---

# 56. Production Environment

Required:

```text
Production database
Production authentication
Production AI credentials
Production application URL
Production logging
Production rate limiting
Production HTTPS
Production security headers
```

Never use development credentials in production.

---

# 57. Pre-Launch Checklist

## Product

* [ ] User can register
* [ ] User can log in
* [ ] User can create tasks
* [ ] User can create events
* [ ] User can see today's plan
* [ ] User can record wake time
* [ ] User can replan
* [ ] User can accept/reject plan
* [ ] User can complete tasks

## Scheduler

* [ ] Fixed commitments protected
* [ ] No overlapping blocks
* [ ] Deadlines respected
* [ ] Completed tasks excluded
* [ ] Late wake handled
* [ ] Impossible schedules handled
* [ ] Plan versions stored
* [ ] Rollback works

## AI

* [ ] Server proxy implemented
* [ ] API key server-side
* [ ] Rate limiting active
* [ ] Input validation active
* [ ] Output validation active
* [ ] Token limits configured
* [ ] Usage tracking implemented
* [ ] AI failures do not destroy schedules

## Security

* [ ] `.env` ignored
* [ ] `.env.example` exists
* [ ] Authentication enforced
* [ ] Ownership checks enforced
* [ ] Database queries parameterized
* [ ] CORS restricted
* [ ] Security headers enabled
* [ ] Production errors sanitized
* [ ] Dependencies audited
* [ ] No dangerous HTML rendering
* [ ] Rate limits active

## UI

* [ ] Responsive
* [ ] Keyboard accessible
* [ ] Good contrast
* [ ] Loading states
* [ ] Empty states
* [ ] Error states
* [ ] Reduced motion
* [ ] Mobile Today screen tested

---

# 58. Post-MVP Development

After MVP stability, implement:

## Calendar Integration

```text
Google Calendar
Apple Calendar
Outlook
```

Imported events should initially be treated as external fixed commitments.

## Smarter Personalization

Learn:

```text
Typical wake time
Typical completion time
Preferred focus windows
Rescheduling behavior
Task duration accuracy
```

Do not automatically infer sensitive characteristics.

## Energy-Aware Planning

Allow the user to define:

```text
High-energy periods
Low-energy periods
Preferred task types
```

Then optimize scheduling accordingly.

## Travel-Aware Planning

Future architecture can support:

```text
Event A
↓
Travel
↓
Event B
```

Travel time must be treated as a hard constraint when enabled.

---

# 59. Long-Term Architecture

If Flowlly grows significantly:

```text
                 ┌───────────────┐
                 │ Web / Mobile  │
                 └───────┬───────┘
                         │
                 ┌───────▼───────┐
                 │ API Gateway   │
                 └───────┬───────┘
                         │
        ┌────────────────┼────────────────┐
        │                │                │
        ▼                ▼                ▼
   User Service     Planner Service    AI Service
        │                │                │
        └────────┬───────┴────────────────┘
                 ▼
             PostgreSQL
                 │
                 ▼
              Queue
```

Do not build this architecture prematurely.

Start with a modular monolith.

Extract services only when justified by:

* Scale
* Team size
* Deployment requirements
* Independent scaling
* Reliability requirements

---

# 60. Definition of Done

A feature is not complete because:

```text
"It works on my machine."
```

A feature is complete when:

```text
✓ Requirements implemented
✓ UI implemented
✓ Loading state implemented
✓ Empty state implemented
✓ Error state implemented
✓ Authentication checked
✓ Authorization checked
✓ Input validated
✓ Database access secured
✓ Tests written
✓ Dart analysis and formatting pass
✓ Flutter unit, widget, and integration tests pass
✓ Supabase migrations and RLS policies are verified
✓ Accessibility reviewed
✓ Responsive behavior reviewed
✓ Security implications reviewed
```

---

# 61. Core Engineering Principle

The most important architectural rule in Flowlly is:

```text
              USER DATA
                  │
                  ▼
          ┌───────────────┐
          │ Deterministic │
          │   Scheduler   │
          └───────┬───────┘
                  │
                  ▼
             VALID PLAN
                  │
                  ▼
          ┌───────────────┐
          │      AI       │
          │ explanation / │
          │ suggestions   │
          └───────────────┘
```

Not:

```text
USER
 ↓
LLM
 ↓
random schedule
```

Flowlly should be intelligent without becoming unpredictable.

The scheduler owns correctness.

The AI owns interpretation and assistance.

The user owns the final decision.

---

# 62. Final Implementation Sequence

```text
FOUNDATION
    ↓
DESIGN SYSTEM
    ↓
AUTH
    ↓
DATABASE
    ↓
TASKS
    ↓
EVENTS
    ↓
SCHEDULER
    ↓
TODAY
    ↓
I'M AWAKE
    ↓
REPLANNING
    ↓
PLAN VERSIONING
    ↓
AI SERVER PROXY
    ↓
AI EXPLANATIONS
    ↓
NOTIFICATIONS
    ↓
SCHEDULE VIEW
    ↓
PROJECTS
    ↓
GOALS
    ↓
INSIGHTS
    ↓
CALENDAR INTEGRATIONS
    ↓
PERSONALIZATION
    ↓
OPTIMIZATION
```

---

# 63. Final Product Principle

Flowlly should feel like a calm intelligent layer between the user's intentions and their actual day.

The user should not have to constantly manage the planner.

The planner should continuously answer:

> **What matters now?**

And when reality changes:

> **What should happen next?**

That is the core implementation objective of Flowlly.
