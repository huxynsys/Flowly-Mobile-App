# Flowlly — App Flow

> **Relationship to other documents:** `decisions.md` is authoritative for MVP scope and architecture. This flow must defer to it wherever scope or architecture differs.

## 1. Overview

Flowlly is an adaptive life scheduling planner that helps users organize their commitments, tasks, routines, goals, and available time into a practical daily plan.

Unlike a traditional calendar, Flowlly continuously adapts the user's plan when real-life conditions change.

The most important adaptive interaction is:

> **I'm Awake**

When the user wakes up later or earlier than expected, they can tell Flowlly when their day actually started. The deterministic scheduler then regenerates the remaining schedule while protecting fixed commitments.

---

# 2. Core Navigation

## Desktop Navigation

The primary navigation contains:

```text
Flowlly
│
├── Today
├── Schedule
├── Tasks
├── Projects (Coming later)
├── Goals (Coming later)
├── Insights (Coming later)
│
└── Settings
```

### Today

The user's primary home screen.

Shows:

* Current date
* Current time
* Current day progress
* Current activity
* Next activity
* Remaining schedule
* Important tasks
* Quick actions
* "I'm Awake"
* "Replan My Day"

### Schedule

Calendar and timeline view.

Users can:

* View scheduled events
* Create events
* Edit events
* Delete events
* View day/week
* View fixed commitments
* View flexible planned blocks

### Tasks

Task management.

Users can:

* Create tasks
* Complete tasks
* Edit tasks
* Delete tasks
* Set priority
* Set deadline
* Set duration
* Assign tasks to projects
* Mark tasks as flexible or fixed
* View overdue tasks

### Projects

Groups related tasks into larger outcomes.

Example:

```text
Project: Semester Final Project

Tasks:
├── Research
├── Database design
├── API implementation
├── Frontend
├── Testing
└── Documentation
```

### Goals

Long-term objectives.

Example:

```text
Goal: Improve fitness

Projects:
├── Gym routine
├── Nutrition
└── Running
```

### Insights

Shows historical scheduling information.

Examples:

* Planned vs completed tasks
* Most productive periods
* Schedule consistency
* Frequently postponed tasks
* Average wake time
* Completion patterns
* Time allocation

### Settings

Contains:

* Profile
* Preferences
* Scheduling preferences
* Wake-up preferences
* Notifications
* Calendar integrations
* AI preferences
* Appearance
* Account
* Data/privacy
* Sign out

---

# 3. Mobile Navigation

Mobile navigation should prioritize the user's immediate needs.

```text
Bottom Navigation

Today
Schedule
Tasks
More
```

The **More** screen contains:

```text
More
│
├── Projects (Coming later)
├── Goals (Coming later)
├── Insights (Coming later)
├── Notifications
└── Settings
```

The Today screen remains the primary entry point.

---

# 4. Application Entry Flow

```text
Open Flowlly
      │
      ▼
Is user authenticated?
      │
 ┌────┴────┐
 │         │
 NO        YES
 │         │
 ▼         ▼
Login   Is onboarding complete?
           │
       ┌───┴───┐
       │       │
      NO      YES
       │       │
       ▼       ▼
   Onboarding Today
```

---

# 5. Landing Page Flow

Unauthenticated users initially see the Flowlly landing page.

```text
Landing Page
│
├── See How It Works
├── Features
├── Sign In
└── Get Started
```

### Get Started

```text
Get Started
     │
     ▼
Create Account
     │
     ▼
Onboarding
     │
     ▼
Today
```

### Sign In

```text
Sign In
│
├── Email + Password
├── Continue with Google
└── Forgot Password
```

Successful authentication redirects the user to:

```text
Today
```

---

# 6. Authentication Flow

## 6.1 Sign Up

```text
Sign Up
   │
   ▼
Enter Name
   │
   ▼
Enter Email
   │
   ▼
Create Password
   │
   ▼
Validate Input
   │
 ┌─┴──────────────┐
 │                │
Invalid          Valid
 │                │
 ▼                ▼
Show Error     Create Account
                  │
                  ▼
              Onboarding
```

### Validation Errors

Examples:

```text
Email is required.

Please enter a valid email address.

Password must contain at least 8 characters.

An account with this email already exists.
```

---

# 7. Login Flow

```text
Login
 │
 ├── Email
 └── Password
       │
       ▼
   Submit
       │
       ▼
Authenticate
       │
 ┌─────┴─────┐
 │           │
Failed     Success
 │           │
 ▼           ▼
Error      Check User
             │
             ▼
          Dashboard
```

## Login Error States

Possible errors:

```text
Invalid email or password.

Too many login attempts.
Please try again later.

Unable to connect.
Please check your internet connection.
```

The password field should never be cleared unnecessarily after a recoverable validation error.

---

# 8. Forgot Password Flow

```text
Login
 │
 ▼
Forgot Password
 │
 ▼
Enter Email
 │
 ▼
Send Reset Link
 │
 ▼
Check Email
 │
 ▼
Reset Password
 │
 ▼
Login
```

Success message:

```text
Password reset link sent.
Check your email to continue.
```

---

# 9. First-Time Onboarding

After registration, the user enters onboarding.

The onboarding should be short and focused on information required for useful scheduling.

```text
Welcome
   │
   ▼
About Your Day
   │
   ▼
Scheduling Preferences
   │
   ▼
Typical Wake/Sleep Pattern
   │
   ▼
Important Commitments
   │
   ▼
Notification Preferences
   │
   ▼
Create First Plan
   │
   ▼
Today
```

---

# 10. Onboarding — Basic Profile

Collect:

* Name
* Optional timezone
* Preferred language
* Optional profile information

The user's timezone should preferably be detected automatically and allow manual correction.

---

# 11. Onboarding — Daily Preferences

The user can define:

```text
Typical wake time
Typical sleep time
Preferred planning style
Preferred focus duration
Break preference
```

Example:

```text
Wake time:
12:00 PM

Sleep time:
2:00 AM

Focus sessions:
60–90 minutes

Breaks:
Short breaks
```

These values are **preferences**, not rigid commitments.

---

# 12. Onboarding — Fixed Commitments

Users can add individual fixed commitments in the MVP. Recurring fixed commitments are post-MVP.

Examples:

```text
University class
Work shift
Gym
Family responsibility
Prayer
Commute
Regular appointment
```

Each commitment can contain:

```text
Title
Days
Start time
End time
Location
Recurring / one-time
Fixed / flexible
```

Fixed commitments become protected scheduling blocks.

---

# 13. Onboarding Completion

After onboarding:

```text
Preparing your Flowlly experience...
      │
      ▼
Create initial schedule
      │
      ▼
Today
```

The system creates the initial daily planning context.

---

# 14. Today Page (MVP)

Today is the central screen of Flowlly.

Example:

```text
Good afternoon.

12:34 PM

Your day is 42% complete.


NOW

Database Assignment
12:30 – 2:00 PM

[Start]


NEXT

Lunch
2:00 – 2:30 PM


LATER

Gym
5:00 – 6:00 PM


[Replan My Day]
```

The Today page should answer:

> **What should I do right now?**

without requiring the user to inspect the entire calendar.

---

# 15. Today Page — First Open

When the user opens Flowlly:

```text
Open App
   │
   ▼
Load User
   │
   ▼
Load Today's Plan
   │
   ▼
Check Current Time
   │
   ▼
Check Plan Status
   │
   ▼
Today
```

The system determines:

* Current time
* Current schedule block
* Next block
* Missed blocks
* Completed tasks
* Remaining tasks
* Fixed commitments
* Available time

---

# 16. "I'm Awake" Flow

This is one of Flowlly's core features.

When the user opens Flowlly and has not started their day:

```text
Good afternoon.

Ready to start your day?

You woke up later than planned.

No problem — I'll adjust today's schedule.

[I'm Awake]
```

---

# 17. User Taps "I'm Awake"

```text
I'm Awake
    │
    ▼
Record actual wake time
    │
    ▼
Load today's plan
    │
    ▼
Identify missed blocks
    │
    ▼
Protect fixed commitments
    │
    ▼
Calculate remaining available time
    │
    ▼
Rebuild flexible schedule
    │
    ▼
Generate new plan
    │
    ▼
Show plan preview
```

---

# 18. Adaptive Replanning

The scheduler must distinguish between:

### Fixed

Cannot normally be moved.

Examples:

```text
Class
Meeting
Doctor appointment
Work shift
Flight
```

### Flexible

Can potentially move.

Examples:

```text
Study
Exercise
Personal project
Reading
Cleaning
Shopping
```

When replanning:

```text
Fixed events
      │
      ▼
Protected

Flexible tasks
      │
      ▼
Rescheduled
```

---

# 19. Replanning UI

While the plan is being regenerated:

```text
Replanning your day...

✓ Preserving completed, protected, and in-progress items
✓ Marking ended, incomplete items as missed
✓ Reviewing remaining flexible tasks
✓ Finding available time
✓ Validating the deterministic plan
```

Then:

```text
Your new plan is ready.

[Accept New Plan]
[Keep Current Plan]
```

---

# 20. Replanning Rules

The deterministic replanning flow follows the state transitions in `decisions.md` and this order:

```text
1. Preserve completed plan items as completed
2. Keep protected fixed or locked items
3. Keep an in-progress block until its end unless explicitly skipped
4. Mark ended, incomplete blocks as missed
5. Calculate remaining available time
6. Prioritize remaining flexible tasks
7. Respect deadlines and task duration
8. Respect preferred time windows
9. Apply the default 10-minute transition buffer
10. Place feasible flexible tasks and validate the plan
11. Mark replaced items from the prior version as superseded
12. Present the new plan for accept / keep current
```

The deterministic scheduler generates the candidate plan. AI suggestions, if used, are proposals and must pass validation. The server decides which validated changes can be persisted.

---

# 21. Accept New Plan

```text
Accept New Plan
      │
      ▼
Save New Plan Version
      │
      ▼
Update Today's Schedule
      │
      ▼
Update Notifications
      │
      ▼
Return to Today
```

The user may keep the current plan or accept the proposal. Undo restores the previous active version after an accepted replan.

---

# 22. Keep Current Plan

```text
Keep Current Plan
      │
      ▼
Dismiss Preview
      │
      ▼
Return to Today
```

No schedule changes are committed.

---

# 23. Manual Replanning

Users can manually trigger replanning.

```text
Today
 │
 ▼
Replan My Day
 │
 ▼
Select Reason
```

Possible reasons:

```text
I woke up late
I have less time today
Something came up
I want a lighter day
I want to focus on an important task
Other
```

Then:

```text
Generate New Plan
      │
      ▼
Review
      │
      ▼
Accept / Keep Current
```

---

# 24. Task Creation Flow

```text
Tasks
 │
 ▼
Add Task
 │
 ▼
Enter Task Information
 │
 ├── Title
 ├── Description
 ├── Duration
 ├── Priority
 ├── Deadline
 ├── Recurrence (none, daily, weekly)
 └── Scheduling flexibility
 │
 ▼
Save Task
 │
 ▼
Task Created
```

Optional action:

```text
Schedule this task
```

---

# 25. Quick Task Creation

Users should also be able to create a task quickly.

Example:

```text
+ Add task

"Finish database assignment"
```

After creation:

```text
Task created.

[Schedule it]
```

If scheduled automatically:

```text
Added to today's plan.
```

---

# 26. Task Completion Flow

```text
Today
 │
 ▼
Current Task
 │
 ▼
Complete
 │
 ▼
Mark Task Completed
 │
 ▼
Update Progress
```

Depending on the user's settings, completion may trigger plan adjustment.

Example:

```text
Task completed 20 minutes early.

You now have 20 extra minutes.

[Optimize remaining day]
```

---

# 27. Missed Task Flow

If an uncompleted plan item ends before the replanning time, its state becomes `missed` (per `decisions.md`):

```text
Scheduled time ends
       │
       ▼
Task incomplete?
       │
   ┌───┴───┐
   │       │
  NO      YES
   │       │
   ▼       ▼
Continue   Set state to missed
           │
           ▼
       Decide next action
```

Possible actions:

```text
Replan remaining work
Skip
```

Do not offer “Complete manually” for an elapsed block. A block currently in progress remains `in_progress` until its end unless the user explicitly skips it.

---

# 28. Schedule Flow (MVP)

```text
Schedule
 │
 └── Day / Timeline
```

The MVP schedule shows today's plan and fixed events; week views are post-MVP.

Selecting an event:

```text
Event
 │
 ├── View
 ├── Edit
```

---

# 29. Create Event Flow

```text
Schedule
 │
 ▼
+ Add Event
 │
 ▼
Event Details
 │
 ├── Title
 ├── Date
 ├── Start time
 ├── End time
 ├── Location
 └── Fixed commitment
 │
 ▼
Save
 │
 ▼
Schedule Updated
```

If the new fixed event conflicts with an existing flexible task:

```text
Conflict detected.

This event overlaps with:

Database Assignment
1:00 – 2:00 PM
```

Options:

```text
Move the flexible block
Skip the flexible task and replan
Cancel event creation
```

The MVP must not save an overlapping event or silently overwrite fixed commitments.

---

# 30. Calendar Conflict Flow

When a conflict occurs:

```text
New Event
   │
   ▼
Conflict Detection
   │
 ┌─┴──────────────┐
 │                │
No conflict      Conflict
 │                │
 ▼                ▼
Save          Show Conflict
                  │
                  ▼
              Resolve
```

Resolution options depend on the affected items.

---

# 31. Projects Flow

```text
Projects
 │
 ▼
Project List
 │
 ▼
Select Project
 │
 ├── Overview
 ├── Tasks
 ├── Progress
 ├── Deadline
 └── Schedule
```

Create:

```text
Projects
 │
 ▼
New Project
 │
 ▼
Project Details
 │
 ▼
Add Tasks
 │
 ▼
Project Created
```

---

# 32. Goals Flow

```text
Goals
 │
 ▼
Goal List
 │
 ▼
Select Goal
 │
 ├── Progress
 ├── Projects
 ├── Milestones
 └── Related Tasks
```

Create goal:

```text
New Goal
 │
 ▼
Goal Details
 │
 ▼
Optional Milestones
 │
 ▼
Optional Project
 │
 ▼
Goal Created
```

---

# 33. Insights Flow

```text
Insights
 │
 ▼
Select Time Period
 │
 ├── Today
 ├── This Week
 ├── This Month
 └── Custom
 │
 ▼
Generate Insights
```

Possible information:

```text
Tasks completed
Tasks postponed
Planned hours
Completed hours
Most productive periods
Schedule changes
Wake-time patterns
Goal progress
```

Insights should describe observed behavior rather than judge the user.

---

# 34. Notifications Flow

Notifications can originate from:

```text
Upcoming event
Upcoming task
Task deadline
Schedule change
Replanning result
Reminder
```

The MVP reminder is in-app. Additional notification channels and goal-related notifications are post-MVP.

Example:

```text
Database Assignment starts in 15 minutes.
```

For adaptive changes:

```text
Your schedule changed.

Gym moved from 5:00 PM to 6:30 PM.
```

The MVP includes basic in-app reminders only. Push, email, and browser notifications are post-MVP. Users can control in-app reminder preferences in Settings.

---

# 35. Settings Flow

```text
Settings
│
├── Account
├── Profile
├── Scheduling Preferences
├── Wake/Sleep Preferences
├── Notifications
├── Calendar Integrations (Coming later)
├── AI Preferences (Coming later)
├── Appearance
├── Privacy & Data
└── Sign Out
```

---

# 36. Calendar Integration Flow (Post-MVP)

When integrations are implemented:

```text
Settings
 │
 ▼
Calendar Integrations
 │
 ▼
Connect Calendar
 │
 ▼
Authentication
 │
 ▼
Grant Permission
 │
 ▼
Calendar Connected
 │
 ▼
Import Events
```

Imported calendar events should be clearly distinguishable from Flowlly-created items.

---

# 37. AI Planning Flow (Post-MVP)

AI is optional and must not be the source of truth for the calendar. The deterministic scheduler generates the canonical plan.

```text
User Context
     │
     ├── Tasks
     ├── Events
     ├── Goals
     ├── Preferences
     ├── Current Time
     └── Wake Time
            │
            ▼
   AI Proposal (optional)
            │
            ▼
  Deterministic Scheduler
            │
            ▼
      Validated Plan
            │
            ▼
     Server Decision
            │
            ▼
       User Review
            │
       ┌────┴────┐
       │         │
     Accept     Keep Current
       │         │
       ▼         ▼
 Save Plan    Keep Plan
```

---

# 38. AI Error Flow

If AI planning fails:

```text
Planning request
      │
      ▼
AI service unavailable
      │
      ▼
Use deterministic fallback
      │
      ▼
Generate best available plan
```

User message:

```text
We couldn't use the AI planner right now.

Your existing schedule is safe.
We'll use your saved scheduling preferences instead.
```

The app should remain functional when AI is unavailable.

---

# 39. Network Error Flow

For temporary network problems:

```text
Request
 │
 ▼
Network failure
 │
 ▼
Retry automatically
 │
 ├── Success → Continue
 │
 └── Failure → Show error
```

Example:

```text
Something went wrong.

Your changes haven't been lost.

[Try Again]
```

---

# 40. Save Failure Flow

If a schedule change cannot be saved:

```text
User accepts plan
       │
       ▼
Save
       │
       ▼
Failure
       │
       ▼
Do not pretend it was saved
       │
       ▼
Show error
```

Example:

```text
We couldn't save your new plan.

Your previous schedule is still safe.

[Try Again]
```

---

# 41. Offline Flow

If the application supports offline operation:

```text
Internet unavailable
       │
       ▼
Show offline indicator
       │
       ▼
Allow local actions where safe
       │
       ▼
Queue changes
       │
       ▼
Connection restored
       │
       ▼
Synchronize
```

Conflicts discovered during synchronization must be surfaced to the user instead of silently overwriting data.

---

# 42. Session Expiration

```text
Session expired
      │
      ▼
Save recoverable local state
      │
      ▼
Redirect to Login
      │
      ▼
User authenticates
      │
      ▼
Restore application state
```

Message:

```text
Your session has expired.

Please sign in again to continue.
```

---

# 43. Delete Confirmation Flow

For destructive actions:

```text
Delete
 │
 ▼
Confirmation
 │
 ├── Cancel
 └── Delete
```

Example:

```text
Delete this task?

This action cannot be undone.

[Cancel] [Delete]
```

For recoverable entities, prefer archive/undo where appropriate.

---

# 44. Empty States

Every major page should have a useful empty state.

## No Tasks

```text
Nothing on your task list yet.

Add something you want to get done.

[Add Task]
```

## No Events

```text
Your schedule is clear.

Add a commitment or event to get started.

[Add Event]
```

## No Projects

```text
Projects help you organize related tasks.

[Create Project]
```

## No Goals

```text
Turn something important into a goal.

[Create Goal]
```

## No Insights

```text
Complete a few days in Flowlly
to start seeing useful patterns.
```

---

# 45. Global Error Principles

Errors should:

1. Explain what happened.
2. Protect user data.
3. Provide a recovery action.
4. Avoid technical jargon.
5. Never claim an action succeeded when it did not.
6. Preserve unsaved work where possible.
7. Avoid interrupting the user unnecessarily.

---

# 46. Global Navigation Rules

### Rule 1 — Today is the home

After authentication, the default destination is:

```text
Today
```

### Rule 2 — Preserve context

Returning from a detail screen should normally return the user to the previous screen and relevant scroll position.

### Rule 3 — Never silently destroy schedule data

Changes to events, tasks, and plans should be explicit.

### Rule 4 — Fixed commitments are protected

The planner should not silently move fixed commitments.

### Rule 5 — AI proposes, the system validates

Any AI schedule proposal must pass deterministic validation; the server decides what is persisted after user confirmation.

### Rule 6 — User remains in control

The user can review and reject significant automatic schedule changes.

---

# 47. Primary User Journey

The main Flowlly journey is:

```text
Landing
   │
   ▼
Sign Up
   │
   ▼
Onboarding
   │
   ▼
Today
   │
   ▼
Add Commitments / Tasks
   │
   ▼
Initial Plan
   │
   ▼
Start Day
   │
   ▼
I'm Awake
   │
   ▼
Adaptive Replanning
   │
   ▼
Review New Plan
   │
   ▼
Accept
   │
   ▼
Follow Day
   │
   ├──────────────┐
   ▼              ▼
Complete       Something Changes
Task               │
   │               ▼
   │          Replan My Day
   │               │
   └───────┬───────┘
           ▼
       Updated Day
```

---

# 48. Core Screen Map

```text
                         ┌──────────────┐
                         │   Landing    │
                         └──────┬───────┘
                                │
                     ┌──────────┴──────────┐
                     ▼                     ▼
                  Sign Up                Login
                     │                     │
                     └──────────┬──────────┘
                                ▼
                         ┌──────────────┐
                         │  Onboarding  │
                         └──────┬───────┘
                                ▼
                         ┌──────────────┐
                         │     Today    │
                         └──────┬───────┘
                ┌───────────────┼────────────────┐
                ▼               ▼                ▼
           Schedule           Tasks       Projects (Coming later)
                │               │                │
                └───────────────┼────────────────┘
                                ▼
                         Goals (Coming later)
                                │
                                ▼
                       Insights (Coming later)


Today
 │
 ├── I'm Awake
 │      │
 │      ▼
 │   Replanning
 │      │
 │      ▼
 │   Review Plan
 │      │
 │      ├── Accept
 │      └── Keep Current
 │
 ├── Current Task
 │
 ├── Next Task
 │
 └── Replan My Day
```

---

# 49. Priority of User Flows

Priorities defer to the MVP definition in `decisions.md`.

## Must-have

* Landing
* Sign up
* Login
* Onboarding
* Today
* Tasks
* Events
* Daily schedule
* "I'm Awake"
* Adaptive replanning
* Plan review
* Schedule locking
* Task completion
* Basic in-app reminders
* Undo last accepted replan
* Manual flexible plan-block editing
* Error handling
* Settings
* Sign out

## Should-have

* None beyond the MVP; do not use this list to expand MVP scope.

## Nice-to-have

* Natural-language task creation
* AI explanations beyond templates
* Energy-aware planning
* Travel-time optimization
* Automatic behavioral learning
* Advanced productivity analytics
* Multi-calendar intelligence
* Projects, goals, insights, calendar integrations, and recurring tasks beyond simple daily/weekly

---

# 50. Definition of a Successful App Flow

A user should be able to:

```text
Create account
      ↓
Set basic preferences
      ↓
Add commitments
      ↓
Add tasks
      ↓
Receive a practical daily plan
      ↓
Wake up at an unexpected time
      ↓
Tap "I'm Awake"
      ↓
Receive an updated plan
      ↓
Review changes
      ↓
Accept the plan
      ↓
Complete tasks
      ↓
Handle unexpected changes
      ↓
Replan again
      ↓
Finish the day with an accurate record
```

The entire experience should feel like **one continuous adaptive planning system**, rather than a collection of disconnected calendar, task, and productivity screens.
