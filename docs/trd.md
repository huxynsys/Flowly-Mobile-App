**# Flowlly — Technical Requirements Document (TRD)**



\> **\*\*Relationship to other documents:\*\*** \`decisions.md\` is authoritative for MVP scope and architecture. This TRD must defer to it wherever scope or architecture differs.



**\*\*Product:\*\*** Flowlly

**\*\*Product Type:\*\*** Adaptive Life Scheduling & Planning Application

**\*\*Document Type:\*\*** Technical Requirements Document

**\*\*Version:\*\*** 1.0

**\*\*Status:\*\*** Development Specification

**\*\*Primary UI Design Tool:\*\*** Google Stitch



**---**



**# 1. Product Overview**



Flowlly is an adaptive life scheduling application designed to help users plan, execute, and continuously adjust their day.



Unlike a traditional calendar or task manager, Flowlly does not assume that the user's day always goes according to the original plan.



The system continuously considers:



\* Fixed commitments

\* Flexible tasks

\* Deadlines

\* Priorities

\* Estimated task durations

\* User availability

\* Sleep and wake patterns

\* Actual wake time

\* Completed tasks

\* Missed tasks

\* Remaining available time

\* User preferences

\* Daily routines

\* Goals and projects



The central concept is:



\> **\*\*The plan should adapt to the user's real life, not force the user to adapt to the plan.\*\***



**---**



**# 2. Core Product Concept**



Flowlly consists of five major technical systems:



1\. **\*\*Planning System\*\***

2\. **\*\*Scheduling Engine\*\***

3\. **\*\*Adaptive Replanning Engine\*\***

4\. **\*\*AI Planning Assistant\*\***

5\. **\*\*User Interface\*\***



The architecture must keep these systems logically separated.



The AI should not directly control the database or arbitrarily modify schedules.



Instead:



\`\`\`text

User

  ↓

Flowlly UI

  ↓

Application/API Layer

  ↓

Planning Domain

  ↓

Scheduling Engine

  ↓

AI Planning Layer

  ↓

Validated Plan

  ↓

Database

  ↓

UI

\`\`\`



**---**



**# 3. Product Goals**



**## 3.1 Primary Goals**



Flowlly must:



\* Help users organize their entire day.

\* Combine calendar events and tasks.

\* Generate realistic daily schedules.

\* Automatically adapt schedules when circumstances change.

\* Allow users to report their actual wake time.

\* Replan the remaining day after a late wake-up.

\* Protect fixed commitments.

\* Prioritize important work.

\* Reduce decision fatigue.

\* Make scheduling simple enough to use every day.

\* Explain why the schedule changed.

\* Preserve user control over AI-proposed schedule changes.



**---**



**# 4. Non-Goals**



Flowlly is not initially intended to be:



\* A full project-management replacement.

\* A social network.

\* A complex enterprise resource planner.

\* A financial planning application.

\* A medical sleep-management application.

\* A fully autonomous assistant that makes irreversible decisions without confirmation.



**---**



**# 5. Core User Scenarios**



**## 5.1 Normal Day**



The user has:



\* University/work commitments

\* Tasks

\* Personal activities

\* Exercise

\* Meals

\* Sleep schedule



Flowlly generates a realistic daily plan.



**---**



**## 5.2 Late Wake-Up**



Example:



The user planned to wake at 9:00 AM.



They actually wake at 12:00 PM.



They open Flowlly and press:



\> **\*\*I'm Awake\*\***



Flowlly then:



1\. Records actual wake time.

2\. Marks the morning schedule as invalid/past.

3\. Protects fixed commitments.

4\. Identifies remaining available time.

5\. Re-evaluates tasks.

6\. Moves flexible tasks.

7\. Removes or postpones low-priority work when necessary.

8\. Recalculates the schedule.

9\. Generates a new plan.

10\. Shows the user what changed.



**---**



**## 5.3 Missed Task**



If the user misses a task:



\`\`\`text

Task missed

    ↓

Determine task importance

    ↓

Check deadline

    ↓

Check remaining availability

    ↓

Reschedule / postpone / split / remove

    ↓

Update daily plan

\`\`\`



**---**



**## 5.4 Unexpected Event**



Example:



User adds:



\> Doctor appointment — 4:00 PM–5:00 PM



Flowlly recalculates the surrounding schedule without unnecessarily changing unrelated commitments.



**---**



**# 6. Recommended Technology Stack**

## 6.1 Mobile Frontend

The production client is a Flutter mobile application.

Recommended stack:

* Flutter
* Dart (sound null safety)
* Material 3 with a custom Flowlly design system
* Riverpod for application state and dependency injection
* GoRouter for navigation and route guards
* `supabase_flutter` for Supabase Auth, database access, and supported realtime features
* Freezed + `json_serializable` for immutable models and JSON serialization
* `intl` and `timezone` for locale-aware date/time handling
* `shared_preferences` for lightweight local preferences
* Flutter's built-in test framework, plus `flutter_test` and `integration_test`

Use Dio only where a direct HTTP client is needed beyond the Supabase SDK. Avoid adding packages unless a feature requires them.

Responsibilities:

* UI rendering and interaction
* Local UI state
* API communication
* Input validation and model parsing
* Accessible mobile layouts
* Loading, empty, and error states
* Presenting proposed schedule changes and collecting user confirmation

## 6.2 Backend and Data

Recommended initial backend:

* Supabase Auth for email/password authentication and session management
* Supabase PostgreSQL for persistent application data
* Row Level Security (RLS) policies for user-owned records
* Supabase Edge Functions for privileged server-side operations and AI-provider calls
* OpenAI API accessed only from a trusted server-side function
* PostgreSQL migrations for schema changes

The Flutter app must never contain service-role keys or private AI API keys. Client-side authorization checks are for UX only; database RLS and server-side authorization remain authoritative.

## 6.3 Scheduling and AI Boundaries

Keep the deterministic scheduling engine separate from the AI layer. The engine may be implemented in a server-side module invoked by Edge Functions; it must validate all candidate plans before persistence. AI returns structured proposals and explanations, not arbitrary database writes.

Implement the authoritative scheduling engine in TypeScript/Deno under the Supabase Edge Functions shared scheduler module defined in `decisions.md`. Do not move privileged scheduling logic into the Flutter client.

## 6.4 Development and Delivery

* IDE: VS Code
* AI-assisted development: Cline
* Version control: Git + GitHub
* Local tooling: Flutter SDK, Dart SDK, Android Studio tooling, and Xcode when building for iOS
* CI: format, analyze, test, and build checks
* Mobile delivery: Android App Bundle/APK and iOS archive through Flutter's build tooling; store submission process to be selected later

The frontend technology change does not change the product's MVP scope or scheduling rules.



**# 7. UI Design and Google Stitch**



**## 7.1 Google Stitch Role**



Google Stitch will be used as the primary UI design and visual prototyping environment.



Stitch is a **\*\*design/prototyping layer\*\***, not the production application runtime.



The workflow is:



\`\`\`text

Product Requirements

        ↓

UX Flows

        ↓

Google Stitch

        ↓

Visual Design

        ↓

Design Review

        ↓

Design Tokens

        ↓

Reusable Flutter Widgets

        ↓

Production UI

\`\`\`



**---**



**## 7.2 Stitch Responsibilities**



Use Stitch for:



\* Screen design

\* Layout exploration

\* Responsive concepts

\* Visual hierarchy

\* Typography exploration

\* Color system exploration

\* Component appearance

\* User-flow visualization

\* Dashboard concepts

\* Mobile layouts

\* Empty states

\* Loading states

\* Error states



**---**



**## 7.3 Production Implementation**



Stitch designs must be translated into reusable application components.



Do not blindly copy generated markup.



Production UI must use:



\`\`\`text

Flutter

+

Dart

+

Material 3 + custom Flowlly widgets

+

Flowlly Design Tokens

\`\`\`



**---**



**# 8. UI Design Principles**



Flowlly should feel:



\* Calm

\* Intelligent

\* Personal

\* Lightweight

\* Trustworthy

\* Modern

\* Focused

\* Non-judgmental



It should not feel like:



\* Enterprise project management software

\* A spreadsheet

\* A complicated calendar

\* A chatbot interface

\* A productivity guilt machine



**---**



**# 9. Primary Navigation**



**## Desktop**



Recommended navigation:



\`\`\`text

Today

Schedule

Tasks

Projects

Goals

Insights

Settings

\`\`\`



**## Mobile**



\`\`\`text

Today

Schedule

Tasks

More

\`\`\`



**---**



**# 10. Core Screens**



**## 10.1 Today**



The Today screen is the primary application screen.



It should answer:



\> **\*\*What should I do right now?\*\***



Example:



\`\`\`text

Good afternoon

12:34 PM



Your day is 42% complete



────────────────────



NOW



Database Assignment

12:30 – 2:00 PM



[ Start ]



────────────────────



NEXT



Lunch

2:00 – 2:30 PM



────────────────────



LATER



Gym

5:00 – 6:00 PM



────────────────────



[ Replan My Day ]

\`\`\`



**---**



**# 11. I'm Awake Experience**



The application must prominently support the wake-up action.



Example:



\`\`\`text

Good afternoon.



Ready to start your day?



You woke up later than planned.

No problem — I'll adjust today's schedule.



[ I'm Awake ]

\`\`\`



After tapping:



\`\`\`text

I'm Awake

      ↓

Record actual wake time

      ↓

Recalculate available time

      ↓

Run planner

      ↓

Generate updated plan

\`\`\`



**---**



**# 12. Replanning UI**



During replanning:



\`\`\`text

Replanning your day...



✓ Checking commitments

✓ Protecting fixed events

✓ Reviewing remaining tasks

✓ Optimizing available time

✓ Building new schedule

\`\`\`



Then:



\`\`\`text

Your new plan is ready.



You have 6 tasks remaining.



I moved:

• Database Assignment

• Gym



I postponed:

• Reading



I protected:

• 4:00 PM appointment



[ Accept New Plan ]



[ Keep Current Plan ]

\`\`\`



**---**



**# 13. Schedule Screen**



The Schedule screen provides a timeline.



Example:



\`\`\`text

08:00  Sleep

09:00  ─────────────



12:00  Wake Up



12:30  Database Assignment

14:00  Lunch

14:30  Study

16:00  Appointment

17:00  Gym

18:00  Dinner

19:00  Personal Project

21:00  Free Time

23:30  Wind Down

00:00  Sleep

\`\`\`



**---**



**# 14. Task System**



Tasks must support:



\* Title

\* Description

\* Priority

\* Estimated duration

\* Deadline

\* Status

\* Project

\* Goal

\* Preferred time

\* Fixed/flexible classification

\* Recurrence

\* Dependencies

\* Tags

\* Energy requirement

\* Context



Example:



\`\`\`text

Task

├── id

├── title

├── description

├── priority

├── duration

├── deadline

├── status

├── projectId

├── goalId

├── flexibility

├── preferredTime

├── energyLevel

└── dependencies

\`\`\`



**---**



**# 15. Fixed vs Flexible Items**



This distinction is fundamental.



**## Fixed**



Examples:



\* Class

\* Meeting

\* Appointment

\* Flight

\* Work shift



These cannot normally be moved by the planner.



**## Flexible**



Examples:



\* Study

\* Exercise

\* Reading

\* Personal project

\* Cleaning

\* Shopping



These may be moved by the planner.



**---**



**# 16. Scheduling Engine**



The scheduling engine is the core deterministic system.



It must be independent from the AI layer.



Its responsibilities include:



\* Finding available time.

\* Detecting conflicts.

\* Respecting fixed events.

\* Scheduling flexible tasks.

\* Handling deadlines.

\* Splitting tasks.

\* Applying priorities.

\* Applying user preferences.

\* Protecting buffers.

\* Recalculating schedules.



**---**



**# 17. Scheduling Constraints**



The scheduler must support two constraint classes.



**## Hard Constraints**



These must not normally be violated.



Examples:



\`\`\`text

Fixed event

Sleep period

Unavailable period

Calendar commitment

Task dependency

\`\`\`



**## Soft Constraints**



These may be violated when necessary.



Examples:



\`\`\`text

Preferred time

Preferred duration

Preferred energy level

Routine preference

Ideal break duration

\`\`\`



**---**



**# 18. Scheduling Priority Model**



A task can be evaluated using multiple factors:



\`\`\`text

Priority

\+

Deadline urgency

\+

Estimated duration

\+

Dependency importance

\+

User preference

\+

Available time

\+

Energy compatibility

\`\`\`



A conceptual score:



\`\`\`text

Task Score =

Priority Weight

\+

Deadline Urgency

\+

Dependency Weight

\+

Preference Weight

\+

Energy Fit

\`\`\`



The exact scoring algorithm should remain configurable.



**---**



**# 19. Adaptive Replanning**



The replanning engine is responsible for modifying an existing plan.



Inputs:



\`\`\`text

Current Plan

\+

Actual Time

\+

Actual Wake Time

\+

Completed Tasks

\+

Missed Tasks

\+

New Events

\+

Remaining Tasks

\+

User Preferences

\`\`\`



Output:



\`\`\`text

New Validated Plan

\`\`\`



**---**



**# 20. Replanning Rules**



When replanning:



**### Rule 1**



Never automatically move a fixed commitment unless explicitly permitted.



**### Rule 2**



Completed tasks must remain completed.



**### Rule 3**



Past tasks must not be scheduled in the future.



**### Rule 4**



Tasks with expired deadlines require special handling.



**### Rule 5**



High-priority tasks should receive preference when feasible.



**### Rule 6**



Low-priority tasks may be postponed.



**### Rule 7**



Tasks can be split when necessary.



Example:



\`\`\`text

Original:

Study 3 hours



New:

13:00–14:00

16:00–17:00

20:00–21:00

\`\`\`



**### Rule 8**



The system should avoid excessive schedule fragmentation.



**---**



**# 21. AI Planning Layer**



AI should enhance the scheduling system rather than replace it.



The AI can:



\* Interpret natural-language tasks.

\* Understand user intent.

\* Suggest task priorities.

\* Suggest schedule adjustments.

\* Explain schedule changes.

\* Convert vague goals into actionable tasks.

\* Identify potential conflicts.

\* Suggest realistic alternatives.

\* Summarize the user's day.



**---**



**# 22. AI Safety Architecture**



AI must not directly write arbitrary database records.



Recommended architecture:



\`\`\`text

User

 ↓

AI

 ↓

Structured Planning Proposal

 ↓

Validation Layer

 ↓

Scheduling Engine

 ↓

Validated Changes

 ↓

Database

\`\`\`



Example AI output:



\`\`\`json

{

  "action": "reschedule",

  "taskId": "task_123",

  "suggestedStart": "15:00",

  "durationMinutes": 60,

  "reason": "Moved because the morning block was missed."

}

\`\`\`



The scheduling engine validates this proposal before applying it.



**---**



**# 23. AI Explanation System**



Flowlly should explain important schedule changes.



Example:



\> “I moved your assignment to 3:00 PM because you woke up later than planned and your 4:00 PM appointment is fixed.”



The explanation should be:



\* Short

\* Clear

\* Human

\* Non-judgmental

\* Based on actual scheduling changes



**---**



**# 24. Database Architecture**



A relational database is recommended.



Recommended:



**\*\*PostgreSQL\*\***



Core entities:



\`\`\`text

users

user_preferences

tasks

projects

goals

calendar_events

daily_plans

plan_items

plan_versions

wake_events

replanning_runs

task_dependencies

notifications

task_completion_logs

\`\`\`



**---**



**# 25. User Model**



\`\`\`text

User

├── id

├── email

├── name

├── timezone

├── locale

├── createdAt

└── updatedAt

\`\`\`



**---**



**# 26. User Preferences**



\`\`\`text

UserPreferences

├── userId

├── defaultWakeTime

├── defaultSleepTime

├── minimumSleepDuration

├── preferredWorkDuration

├── defaultBreakDuration

├── planningStyle

├── notificationPreferences

└── schedulingPreferences

\`\`\`



**---**



**# 27. Task Model**



\`\`\`text

Task

├── id

├── userId

├── title

├── description

├── status

├── priority

├── estimatedMinutes

├── deadline

├── preferredStart

├── preferredEnd

├── flexibility

├── energyLevel

├── projectId

├── goalId

├── createdAt

└── updatedAt

\`\`\`



**---**



**# 28. Calendar Event Model**



\`\`\`text

CalendarEvent

├── id

├── userId

├── title

├── startTime

├── endTime

├── location

└── isFixed

\`\`\`

Recurring fixed events and external calendar source/ID fields are post-MVP; task recurrence is limited to simple daily and weekly rules as defined in `decisions.md`.



**---**



**# 29. Daily Plan Model**



\`\`\`text

DailyPlan

├── id

├── userId

├── date

├── status

├── generatedAt

├── version

└── createdAt

\`\`\`



**---**



**# 30. Plan Item Model**



\`\`\`text

PlanItem

├── id

├── dailyPlanId

├── taskId

├── calendarEventId

├── startTime

├── endTime

├── type

├── status

├── source

└── metadata

\`\`\`



**---**



**# 31. Plan Versioning**



Every significant replanning operation should create a new plan version.



Example:



\`\`\`text

Plan v1

   ↓

User wakes late

   ↓

Plan v2

   ↓

User adds appointment

   ↓

Plan v3

\`\`\`



This enables:



\* History

\* Rollback

\* Debugging

\* Analytics

\* User transparency



**---**



**# 32. Wake Event Model**



\`\`\`text

WakeEvent

├── id

├── userId

├── date

├── plannedWakeTime

├── actualWakeTime

├── source

├── createdAt

└── replanningRunId

\`\`\`



Possible sources:



\`\`\`text

manual

notification

wearable

automatic

\`\`\`



MVP should support:



\`\`\`text

manual

\`\`\`



**---**



**# 33. Replanning Run**



\`\`\`text

ReplanningRun

├── id

├── userId

├── dailyPlanId

├── trigger

├── startedAt

├── completedAt

├── status

├── inputSnapshot

├── outputSnapshot

└── explanation

\`\`\`



Possible triggers:



\`\`\`text

wake

missed_task

new_event

task_completed

manual_replan

schedule_conflict

\`\`\`



**---**



**# 34. API Architecture**



The Flutter client communicates with the backend through the Supabase SDK and authenticated Edge Function endpoints.



Example API structure:



\`\`\`text

Flutter uses Supabase Auth and the authenticated Supabase client for ordinary CRUD under RLS. Invoke server-authoritative operations through Supabase Edge Functions:

functions/v1/generate-plan

functions/v1/replan-day

functions/v1/wake

functions/v1/ai-plan (post-MVP AI capability)

functions/v1/ai-explain (post-MVP beyond-template explanations)

\`\`\`



**---**



**# 35. Example Wake API**



\`\`\`http

POST /functions/v1/wake

\`\`\`



Request:



\`\`\`json

{

  "actualWakeTime": "2026-09-19T12:05:00"

}

\`\`\`



Response:



\`\`\`json

{

  "success": true,

  "replanningRequired": true,

  "planId": "plan_123",

  "replanId": "replan_456"

}

\`\`\`



**---**



**# 36. Replanning API**



\`\`\`http

POST /functions/v1/replan-day

\`\`\`



Request:



\`\`\`json

{

  "date": "2026-09-19",

  "trigger": "wake",

  "context": {

    "actualWakeTime": "12:05"

  }

}

\`\`\`



Response:



\`\`\`json

{

  "planVersion": 2,

  "changes": [],

  "warnings": [],

  "explanation": "Your schedule was adjusted around your later wake time."

}

\`\`\`



**---**



**# 37. Authentication**



The application must support:



\* Email authentication

\* Password authentication

\* Session management

\* Secure logout

\* Password reset

The MVP uses Supabase Auth with email/password authentication. OAuth providers are post-MVP.



**---**



**# 38. Authorization**



Every user-owned resource must be protected.



A user must never be able to access another user's:



\* Tasks

\* Plans

\* Calendar events

\* Goals

\* Projects

\* Preferences

\* AI history

\* Notifications



Authorization must be enforced server-side.



Client-side checks are not sufficient.



**---**



**# 39. Timezone Handling**



Time is critical to Flowlly.



All persisted timestamps should use a consistent timezone-aware representation.



Each user has:



\`\`\`text

timezone

\`\`\`



Example:



\`\`\`text

Asia/Karachi

\`\`\`



The application must correctly handle:



\* Local dates

\* Day boundaries

\* Daylight saving time where applicable

\* Calendar event conversions

\* Recurring schedules



**---**



**# 40. Notification System**



Notifications may include:



\* Upcoming task

\* Task starting

\* Task overdue

\* Wake reminder

\* Replanning completion

\* Daily planning reminder

\* Evening review



Notification preferences must be configurable.



**---**



**# 41. Daily Planning Lifecycle**



The daily lifecycle:



\`\`\`text

Day Created

    ↓

Initial Plan Generated

    ↓

User Wakes

    ↓

Actual Wake Recorded

    ↓

Replanning

    ↓

User Executes Tasks

    ↓

Task Completion

    ↓

Potential Replanning

    ↓

New Event / Change

    ↓

Replanning

    ↓

Evening Review

    ↓

Day Completed

\`\`\`



**---**



**# 42. Morning Planning**



When the user starts their day, Flowlly should show:



\* Current time

\* Actual wake time

\* Today's commitments

\* Current task

\* Next task

\* Remaining tasks

\* Available time

\* Important deadlines



**---**



**# 43. Evening Review**



Optional evening review:



\`\`\`text

Your day



Completed

7 tasks



Moved

2 tasks



Postponed

1 task



Tomorrow

3 important tasks

\`\`\`



This data can later be used to improve scheduling recommendations.



**---**



**# 44. Projects**



Projects group related tasks.



Example:



\`\`\`text

Project:

Final Year Project



Tasks:

├── Research

├── Requirements

├── UI Design

├── Backend

├── Testing

└── Documentation

\`\`\`



Projects should not control scheduling directly.



The scheduler schedules their tasks.



**---**



**# 45. Goals**



Goals represent higher-level outcomes.



Example:



\`\`\`text

Goal:

Improve Fitness



Projects:

├── Gym Routine

└── Nutrition



Tasks:

├── Workout

├── Meal Planning

└── Grocery Shopping

\`\`\`



Goal → Project → Task



This hierarchy should remain optional.



**---**



**# 46. Recurring Tasks**



Support recurring tasks such as:



\* Daily exercise

\* Weekly cleaning

\* Weekly planning

\* Monthly bills

\* Regular study sessions



Recurring tasks should use a recurrence rule rather than generating unlimited database records.



**---**



**# 47. Task Dependencies**



Example:



\`\`\`text

Task A

  ↓

Task B

  ↓

Task C

\`\`\`



If Task A is not completed, Task B may need to move.



The scheduler must respect dependency constraints.



**---**



**# 48. Conflict Detection**



The system must detect:



\`\`\`text

Task overlap

Event overlap

Insufficient available time

Deadline conflicts

Dependency conflicts

Sleep conflicts

\`\`\`



Example:



\`\`\`text

Task:

Study — 3 hours



Available:

1 hour



System:



⚠ Not enough time



Options:

[Split Task]

[Move Task]

[Reduce Duration]

\`\`\`



**---**



**# 49. Task Splitting**



Flowlly should be able to split flexible tasks.



Example:



\`\`\`text

Original:

Programming — 180 minutes



Available:

90 minutes



Plan:



10:00–11:00 Programming

11:00–11:30 Break

15:00–16:00 Programming

16:00–16:30 Programming

\`\`\`



The exact splitting strategy should be configurable.



**---**



**# 50. Schedule Quality**



The scheduler should optimize for:



1\. Hard constraint satisfaction

2\. Deadline safety

3\. Priority alignment

4\. Realistic task duration

5\. User preferences

6\. Reduced fragmentation

7\. Reasonable breaks

8\. Consistent routines



The system should prioritize feasibility over theoretical optimization.



**---**



**# 51. Manual Override**



Users must always be able to:



\* Move a task

\* Delete a task

\* Add an event

\* Change duration

\* Mark complete

\* Skip a task

\* Postpone a task

\* Lock a schedule item

\* Trigger replanning



Manual user decisions should generally take precedence over AI suggestions.



**---**



**# 52. Schedule Locking**



Users may lock an item.



Example:



\`\`\`text

Gym

5:00–6:00 PM

🔒 Locked

\`\`\`



The scheduler should treat locked items as hard constraints.



**---**



**# 53. Offline Considerations (Post-MVP)**



Offline behavior is post-MVP and is not required for the MVP defined in \`decisions.md\`.



Potentially cache:



\* Today’s plan

\* Tasks

\* Recent schedule

\* Current task

\* Basic completion state



Operations requiring AI or server-side replanning require connectivity.



**---**



**# 54. Frontend Architecture**



The authoritative deterministic scheduler is in `supabase/functions/_shared/scheduler/`, as defined by `decisions.md`:



\`\`\`text

supabase/functions/_shared/scheduler/

├── availability/

├── constraints/

├── conflict/

├── planning/

├── replanning/

├── scoring/

├── types/

└── validation/

\`\`\`



**---**



**# 55. Component Architecture**



Components should be reusable.



Example:



\`\`\`text

TodayPage

├── Greeting

├── CurrentTime

├── DayProgress

├── CurrentTaskCard

├── NextTaskCard

├── Timeline

├── UpcomingTasks

├── AwakeButton

└── ReplanButton

\`\`\`



**---**



**# 56. Design System**



Flowlly should maintain centralized design tokens in Dart theme/configuration files.



Tokens should cover:



\* Colors

\* Typography

\* Spacing

\* Border radius

\* Shadows

\* Component sizes

\* Motion

\* Breakpoints



Example conceptual structure:



\`\`\`text

Design Tokens

├── color

├── typography

├── spacing

├── radius

├── elevation

├── motion

└── breakpoint

\`\`\`



The actual values should be established through the Google Stitch design process and then implemented centrally.



**---**



**# 57. Responsive Design**



The application must support:



\`\`\`text

Mobile

Tablet

Desktop

Large Desktop

\`\`\`



Use the same numeric responsive layout thresholds as \`design.md\` and \`implementation_plan.md\`: mobile \`< 640px\`, tablet \`640px–<1024px\`, desktop \`1024px–1440px\`, and large desktop \`> 1440px\`.



Mobile should prioritize:



\* Current task

\* Timeline

\* Wake action

\* Task completion

\* Replanning



Desktop can expose more scheduling context.



**---**



**# 58. Accessibility**



The application must target WCAG 2.2 AA where practical.



Requirements:



\* Keyboard navigation

\* Screen-reader labels

\* Sufficient contrast

\* Focus states

\* Semantic widgets and accessible labels

\* Accessible forms

\* Accessible dialogs

\* Accessible drag-and-drop alternatives

\* Reduced-motion support



**---**



**# 59. Performance Requirements**



Target:



\* Fast initial page load

\* Minimal unnecessary JavaScript

\* Server-side rendering where useful

\* Optimistic UI for simple actions

\* Efficient database queries

\* Pagination for large task collections

\* Debounced search

\* Cached frequently accessed data



Today should load significantly faster than complex historical analytics pages.



**---**



**# 60. State Management**



Use the simplest state-management solution appropriate to the feature.



Separate:



**### Server State**



Examples:



\* Tasks

\* Plans

\* Events

\* Goals



**### UI State**



Examples:



\* Open dialog

\* Selected task

\* Timeline mode

\* Current filter



Do not duplicate server state unnecessarily in client state.



**---**



**# 61. Validation**



Use schema validation at API boundaries.



Validation must occur for:



\* User input

\* Tasks

\* Events

\* Plan modifications

\* AI-generated proposals, subject to deterministic validation and server-controlled persistence

\* Replanning requests



Recommended approach:



\`\`\`text

Request

 ↓

Schema Validation

 ↓

Authorization

 ↓

Business Rules

 ↓

Database

\`\`\`



**---**



**# 62. Error Handling**



Errors should be categorized.



\`\`\`text

Validation Error

Authentication Error

Authorization Error

Conflict Error

Scheduling Error

AI Error

Database Error

External Integration Error

\`\`\`



User-facing messages should be understandable.



Do not expose internal stack traces.



**---**



**# 63. Logging**



Log important backend operations:



\`\`\`text

Authentication

Plan generation

Replanning

AI calls

Scheduling conflicts

Database failures

External integrations

Notification failures

\`\`\`



Logs must not contain sensitive information unnecessarily.



**---**



**# 64. AI Observability**



Track:



\* AI request ID

\* Model/provider

\* Input category

\* Output validation result

\* Latency

\* Token usage where available

\* Failure reason

\* User acceptance/rejection



Avoid storing unnecessary personal information in AI logs.



**---**



**# 65. Security Requirements**



The system must:



\* Use HTTPS in production.

\* Hash passwords through the authentication provider.

\* Protect sessions.

\* Validate all server inputs.

\* Authorize every resource.

\* Protect API endpoints.

\* Rate-limit sensitive endpoints.

\* Secure secrets.

\* Avoid exposing API keys to the Flutter client.

\* Sanitize user-generated content where necessary.

\* Protect against common web vulnerabilities.



**---**



**# 66. Environment Variables**



Example:



\`\`\`env

SUPABASE_URL=

SUPABASE_ANON_KEY=

\`\`\`

Configure `OPENAI_API_KEY` separately as a Supabase Edge Function secret; never add it to Flutter configuration or assets.


Secrets must never be committed to source control.



Use:



\`\`\`text

.env.local

\`\`\`



for local development.



**---**



**# 67. External Integrations (Post-MVP)**



All external integrations are post-MVP.



**### Calendar**



\* Google Calendar

\* Apple Calendar

\* Microsoft Outlook



**### Notifications**



Email, push, and platform notifications are post-MVP. The MVP supports in-app reminders only.



**### AI**



\* Additional AI capabilities beyond the MVP's template explanations; OpenAI is the selected provider when AI features are introduced



**### Future**



\* Wearables

\* Health platforms

\* Automation platforms



**---**



**# 68. Integration Architecture**



External services should be isolated behind adapters.



Example:



\`\`\`text

CalendarService

├── GoogleCalendarAdapter

├── OutlookCalendarAdapter

└── AppleCalendarAdapter

\`\`\`



This prevents external provider logic from spreading throughout the application.



**---**



**# 69. Scheduling Engine Module**



Recommended structure:



\`\`\`text

supabase/functions/_shared/scheduler/

├── availability/

├── constraints/

├── conflict/

├── planning/

├── replanning/

├── scoring/

├── types/

└── validation/

\`\`\`



**---**



**# 70. Scheduling Pipeline**



\`\`\`text

1\. Load user context

2\. Load fixed commitments

3\. Load flexible tasks

4\. Load preferences

5\. Load current plan

6\. Use `supabase/functions/_shared/scheduler/availability` to determine available windows

7\. Use `supabase/functions/_shared/scheduler/constraints` to apply hard constraints

8\. Use `supabase/functions/_shared/scheduler/planning` to calculate candidate placements

9\. Use `supabase/functions/_shared/scheduler/scoring` to score candidates with deterministic tie-breaking

10\. Use `supabase/functions/_shared/scheduler/planning` to build the schedule

11\. Use `supabase/functions/_shared/scheduler/validation` to validate the result

12\. Return the deterministic schedule for server review and user confirmation where applicable

13\. Persist the accepted plan version through the authenticated Edge Function and enforce ownership with RLS

\`\`\`



**---**



**# 71. Replanning Pipeline**



\`\`\`text

Trigger

 ↓

Capture current state

 ↓

Load current plan

 ↓

Keep completed and protected items; keep in-progress blocks until their end unless explicitly skipped

 ↓

Mark ended, incomplete blocks missed and replaced prior-version items superseded

 ↓

Identify remaining flexible tasks

 ↓

Use `supabase/functions/_shared/scheduler/availability` and `constraints`

 ↓

Use `supabase/functions/_shared/scheduler/planning` and `scoring`

 ↓

Use `supabase/functions/_shared/scheduler/validation`

 ↓

Prepare plan and change summary for review

 ↓

Create a new version through an authenticated Edge Function after user acceptance

 ↓

Notify UI

\`\`\`



**---**



**# 72. Event-Driven Architecture**



Initially, a modular monolith is recommended rather than microservices.



Possible internal events:



\`\`\`text

TASK_CREATED

TASK_COMPLETED

TASK_MISSED

WAKE_RECORDED

EVENT_CREATED

EVENT_UPDATED

PLAN_GENERATED

PLAN_REPLANNED

PLAN_ACCEPTED

\`\`\`



These can later support:



\* Notifications

\* Analytics

\* Automation

\* AI learning

\* Integrations



**---**



**# 73. Recommended Backend Architecture**

For the first production version, use a managed backend and keep the system a modular monolith.

```text
Flutter Mobile App
        |
        | Supabase client / authenticated HTTPS calls
        v
Supabase Auth + Row Level Security
        |
        +--------------------> PostgreSQL
        |
        v
Supabase Edge Functions
        |
        +--> Authorization and request validation
        +--> Domain services
        +--> Deterministic scheduling and replanning
        +--> AI provider adapter
        +--> Validated plan persistence
```

The mobile client handles presentation and user interaction. Privileged operations, AI calls, and authoritative plan validation run server-side. A separate microservice architecture is unnecessary for the initial product.

The scheduling engine remains a logically separate TypeScript/Deno module inside Supabase Edge Functions, per `decisions.md`. Verify Edge Function runtime limits and test scheduling performance before production.



**# 74. Background Jobs**



Background jobs may be used for:



\* Notifications

\* Recurring task generation

\* Calendar synchronization

\* AI processing

\* Daily plan preparation

\* Analytics aggregation



Do not use background jobs when a normal request/response is sufficient.



**---**



**# 75. Testing Strategy**



Testing should exist at four levels.



**## Unit Tests**



Test:



\* Scheduling algorithms

\* Priority calculations

\* Time calculations

\* Constraint validation

\* Task splitting



**## Integration Tests**



Test:



\* Edge Functions + database

\* Authentication

\* Replanning

\* Calendar synchronization



**## E2E Tests**



Test:



\`\`\`text

Login

→ Create task

→ Create event

→ Generate plan

→ Press I'm Awake

→ Replan

→ Accept plan

→ Complete task

\`\`\`



**## Visual Tests**



Use the Stitch designs as visual references.



**---**



**# 76. Critical Test Cases**



**### Case 1**



User wakes at planned time.



Expected:



\`\`\`text

Normal schedule remains.

\`\`\`



**### Case 2**



User wakes late.



Expected:



\`\`\`text

Morning flexible tasks are replanned.

Fixed events remain.

\`\`\`



**### Case 3**



User wakes after a fixed appointment.



Expected:



\`\`\`text

Appointment remains historical/missed.

Remaining schedule is rebuilt.

\`\`\`



**### Case 4**



Task deadline is today.



Expected:



\`\`\`text

Scheduler prioritizes the task if feasible.

\`\`\`



**### Case 5**



No available time exists.



Expected:



\`\`\`text

System reports conflict.

It does not fabricate available time.

\`\`\`



**### Case 6**



User manually locks a task.



Expected:



\`\`\`text

Scheduler treats it as a hard constraint.

\`\`\`



**---**



**# 77. Analytics**



Initial analytics should track product behavior rather than excessive personal profiling.



Useful metrics:



\`\`\`text

Daily active users

Weekly active users

Plans generated

Plans replanned

Wake actions

Task completion rate

Schedule acceptance rate

Manual schedule edits

Replanning frequency

\`\`\`



**---**



**# 78. Personalization**



Over time, Flowlly can learn:



\* Typical wake time

\* Typical task completion duration

\* Preferred work periods

\* Frequently postponed tasks

\* Common schedule conflicts

\* Typical planning patterns



Personalization should be transparent and user-controllable.



**---**



**# 79. Adaptive Intelligence Roadmap**



**## Level 1 — Rule Based**



\`\`\`text

Wake time changed

→ Recalculate schedule

\`\`\`



**## Level 2 — Context Aware**



\`\`\`text

Wake time

\+

Deadlines

\+

Calendar

\+

Task priority

→ Better schedule

\`\`\`



**## Level 3 — Personalized**



\`\`\`text

User behavior

\+

Historical completion

\+

Preferences

→ Personalized scheduling

\`\`\`



**## Level 4 — Predictive**



Potential future capabilities:



\`\`\`text

Predict task duration

Predict missed tasks

Predict overloaded days

Suggest schedule changes proactively

\`\`\`



**---**



**# 80. MVP Scope**



The MVP scope is exactly the list in \`decisions.md\`; this document does not define a separate or expanded MVP.



**---**



**# 81. Post-MVP Features**



Potential later features:



\* Google Calendar integration

\* Outlook integration

\* Apple Calendar integration

\* Natural-language task creation

\* Advanced recurring routines

\* Projects

\* Goals

\* Energy-aware scheduling

\* Travel time

\* Smart reminders

\* Wearable integrations

\* Productivity analytics

\* Automatic evening review

\* Predictive planning

\* Multi-device synchronization



**---**



**# 82. Future AI Features**



Example:



User:



\> “I have an exam next Friday and haven't started studying.”



Flowlly could transform this into:



\`\`\`text

Goal

 ↓

Study Plan

 ↓

Daily Tasks

 ↓

Scheduled Blocks

\`\`\`



Another example:



\> “I want to go to the gym three times this week.”



Flowlly could determine appropriate available windows and propose them without violating existing commitments.



**---**



**# 83. UX for AI Actions**



AI should not overwhelm the user.



Prefer:



\`\`\`text

Suggestion

Why

Action

\`\`\`



Example:



\`\`\`text

I found 90 minutes tomorrow for your project.



Why:

You have a free block after lunch.



[ Add to Plan ]

[ Not Now ]

\`\`\`



Avoid:



\`\`\`text

Large chatbot conversation

\`\`\`



unless conversational planning is explicitly requested.



**---**



**# 84. Data Ownership**



Users should be able to:



\* View their data

\* Modify their data

\* Delete their data

\* Export their data where supported



The architecture should avoid unnecessary vendor lock-in.



**---**



**# 85. Privacy**



Flowlly may contain highly personal scheduling information.



Therefore:



\* Collect only necessary data.

\* Minimize sensitive data retention.

\* Restrict internal access.

\* Secure database access.

\* Encrypt data in transit.

\* Follow applicable privacy requirements.

\* Provide account deletion functionality.



**---**



**# 86. Disaster Recovery**



Production systems should support:



\* Automated database backups

\* Backup retention

\* Recovery procedures

\* Error monitoring

\* Incident logging



Recovery objectives should be defined before production launch.



**---**



**# 87. Deployment Architecture**

Recommended initial architecture:

```text
User
  |
  v
Flutter App (Android / iOS)
  |
  | Authenticated requests
  v
Supabase
  ├── Auth
  ├── PostgreSQL + Row Level Security
  └── Edge Functions
          ├── Scheduling / replanning
          ├── AI provider adapter
          └── Validated persistence
```

Flutter builds are produced through the Flutter toolchain. Backend functions and database migrations are deployed to Supabase. Store publishing and production environment configuration must be documented before release.



**# 88. Development Environments**



Maintain:



\`\`\`text

Development

Staging

Production

\`\`\`



Never use production data for local development.



**---**



**# 89. CI/CD**



Every pull request should ideally run:



\`\`\`text

Type checking

Linting

Unit tests

Integration tests

Build

\`\`\`



Production deployment should occur only after successful validation.



**---**



**# 90. Git Structure**



Recommended branches:



\`\`\`text

main

develop

feature/\*

fix/\*

\`\`\`



Feature branches should be small and focused.



**---**



**# 91. Code Quality**



Requirements:



\* Dart analyzer with strict analysis options

\* Clear naming

\* Small reusable components

\* Domain logic separated from UI

\* No business logic inside visual components

\* Shared validation

\* Consistent error handling

\* Automated tests for scheduling logic



**---**



**# 92. Important Architectural Rule**



The most important technical separation is:



\`\`\`text

UI

≠

AI

≠

Scheduling Engine

≠

Database

\`\`\`



Each system must have a defined responsibility.



**---**



**# 93. Source of Truth**



**### UI**



Source of truth:



\`\`\`text

Server data + local UI state

\`\`\`



**### AI**



Source of truth:



\`\`\`text

Planning suggestions

\`\`\`



**### Scheduling Engine**



Source of truth:



\`\`\`text

Whether a schedule is valid

\`\`\`



**### Database**



Source of truth:



\`\`\`text

Persisted application state

\`\`\`



**---**



**# 94. Example Complete Flow**



User wakes at 12:15 PM.



**### Step 1**



User opens Flowlly.



**### Step 2**



Flowlly displays:



\`\`\`text

Good afternoon.



Ready to start your day?



[ I'm Awake ]

\`\`\`



**### Step 3**



User taps the button.



**### Step 4**



Server records:



\`\`\`text

plannedWakeTime = 09:00

actualWakeTime = 12:15

\`\`\`



**### Step 5**



Planner loads:



\`\`\`text

Today's tasks

Today's events

Deadlines

User preferences

Existing plan

\`\`\`



**### Step 6**



Scheduler removes invalid past flexible blocks.



**### Step 7**



Fixed events are preserved.



**### Step 8**



Remaining tasks are prioritized.



**### Step 9**



Available time windows are calculated.



**### Step 10**



Tasks are placed.



**### Step 11**



The new schedule is validated.



**### Step 12**



New plan version is created.



**### Step 13**



UI displays:



\`\`\`text

Your day has been updated.



Moved:

Database Assignment → 1:00 PM



Moved:

Gym → 6:00 PM



Postponed:

Reading → Tomorrow



Protected:

4:00 PM Appointment



[ Accept New Plan ]

\`\`\`



**---**



**# 95. Definition of Done — MVP**



The MVP is technically complete when the complete scope in \`decisions.md\` is implemented and verified. This includes:



1\. Create an account.

2\. Complete onboarding.

3\. Set sleep/wake preferences.

4\. Create tasks.

5\. Create fixed events.

6\. Generate a daily plan.

7\. View the plan.

8\. See the current task.

9\. Mark tasks complete.

10\. Press **\*\*I'm Awake\*\***.

11\. Enter/confirm actual wake time.

12\. Trigger automatic replanning.

13\. Preserve fixed events.

14\. Reschedule flexible tasks.

15\. View what changed.

16\. Accept the new plan.

17\. Keep the current plan if the user rejects the proposal.

18\. Undo the latest accepted replan by restoring the previous active version.

19\. Manually move, resize, or delete a flexible plan block.

20\. Continue executing the day.

21\. Receive basic in-app reminders.



**---**



**# 96. Product Architecture Summary**

```text
                         FLOWLLY
                            |
               +------------+-------------+
               |                          |
          Flutter App                Supabase
               |                          |
       Dart / Riverpod          +---------+----------+
               |                |                    |
       Flowlly Design System   PostgreSQL          Edge Functions
               |                |                    |
       Google Stitch design     |            +-------+-------+
       → visual specification   |            |               |
                                |        Scheduling       AI Layer
                                |            |               |
                                +------------+-------+-------+
                                                     |
                                                 Validation
                                                     |
                                            Accepted plan versions
```

Google Stitch remains a design/prototyping tool. Its generated output is a visual reference, not production Flutter code.



**# 97. Core Product Principle**



Flowlly should not ask:



\> “What was the plan?”



It should ask:



\> **\*\*“Given what is true right now, what is the best feasible plan for the rest of the day?”\*\***



That principle should guide the scheduling engine, AI behavior, database model, and UI design.



**---**



**# 98. Final Technology Direction**

The recommended implementation direction is:

```text
UI Design
    Google Stitch (visual reference only)

Production Mobile Frontend
    Flutter
    Dart
    Material 3 + custom Flowlly design system
    Riverpod
    GoRouter
    Freezed + json_serializable
    supabase_flutter

Backend
    Supabase Edge Functions
    Server-side domain services
    Deterministic scheduling and validation
    OpenAI API behind a server-side adapter

Database
    Supabase PostgreSQL
    Row Level Security
    Versioned schema migrations

Authentication
    Supabase Auth with email/password

Scheduling
    Custom deterministic scheduling engine
    Hard-constraint protection
    Validated plan versions

Notifications
    MVP: in-app reminders only, as defined by the existing TRD
    Platform push/local notifications are post-MVP

Deployment
    Flutter Android/iOS builds
    Managed Supabase backend
```

The architecture should begin as a modular monolith and only introduce separate services when actual scale or operational requirements justify them.

This final technology direction is consistent with `decisions.md`, which is authoritative for scope and architecture. OpenAI API credentials are stored as Supabase Edge Function secrets and are never included in the Flutter app.



**# 99. Implementation Order**



Recommended development sequence:



\`\`\`text

Phase 1

Project setup

        ↓

Phase 2

Design system + Google Stitch UI

        ↓

Phase 3

Authentication + onboarding

        ↓

Phase 4

Database + core models

        ↓

Phase 5

Tasks + fixed events

        ↓

Phase 6

Basic daily scheduler

        ↓

Phase 7

Today dashboard

        ↓

Phase 8

"I'm Awake" workflow

        ↓

Phase 9

Adaptive replanning

        ↓

Phase 10

AI planning layer

        ↓

Phase 11

Notifications

        ↓

Phase 12

Testing + optimization

        ↓

Phase 13

Production deployment

\`\`\`



**---**



**# 100. Final Architectural Principle**



Flowlly is fundamentally an **\*\*adaptive scheduling system\*\***, not simply a task manager with AI.



The technical architecture should therefore optimize for:



\`\`\`text

Real-world changes

        ↓

Accurate current state

        ↓

Constraint-aware scheduling

        ↓

AI-assisted reasoning

        ↓

Validated plan

        ↓

Simple user action

\`\`\`



The user should never feel that they have “failed” because the original schedule became impossible.



The system should simply adapt and produce the next feasible plan.


---

## Flutter implementation layout

Use the canonical Flutter and Supabase layout defined in `decisions.md`:

```text
lib/
├── app/
├── core/
│   └── widgets/
├── features/
├── domain/
└── data/
supabase/
├── migrations/
└── functions/
    ├── _shared/
    │   └── scheduler/
    └── <function-name>/
test/
integration_test/
```

Keep authoritative scheduling and validation server-side in Supabase Edge Functions; do not place those rules inside Flutter widgets. Never ship privileged secrets in the app bundle.
