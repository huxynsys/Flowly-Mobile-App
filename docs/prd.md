
# Flowlly — Product Requirements Document

**Product Name:** Flowlly  
**Product Type:** AI-powered adaptive life scheduling and planning app  
**Document Version:** 1.0  
**Status:** Product Definition  
**Date:** September 2026

> **Relationship to other documents:** `decisions.md` is authoritative for MVP scope and architecture. This PRD must defer to it wherever scope or architecture differs.

---

# 1. Product Overview

Flowlly is a personal scheduling app whose deterministic scheduler creates a realistic daily plan from a user's commitments, tasks, preferences, and availability. AI may explain or suggest changes, but does not generate the canonical schedule.

Unlike traditional calendar and to-do apps, Flowlly does not expect users to manually construct the perfect schedule.

Instead, users tell Flowlly:

- What they need to do
- What commitments they have
- When they are available
- What their priorities are
- How they slept
- When they actually woke up
- What changed during the day

Flowlly then generates and continuously adapts the user's plan.

The core idea is:

> **Don't plan your life perfectly. Let Flowlly adapt your plan to your real life.**

---

# 2. Problem Statement

Most productivity and scheduling applications assume that people can predict their day accurately.

In reality, plans frequently change.

A user may:

- Wake up later than expected
- Sleep unusually late
- Have an unexpected class or meeting
- Spend longer on a task than expected
- Skip a task
- Become tired
- Need to travel somewhere
- Have an urgent task appear
- Finish something earlier than expected
- Lose several hours of the day

Traditional calendar applications usually show the original schedule but do not intelligently rebuild the remaining day.

To recover, users must manually move tasks, recalculate available time, change priorities, and decide what should be postponed.

This creates planning friction.

### The core problem

Users do not need another static calendar.

They need a system that can answer:

> **"Given what is happening right now, what should I do next?"**

Flowlly solves this by continuously generating and adapting a realistic schedule around the user's actual circumstances.

---

# 3. Target Users

## 3.1 Primary Users

Flowlly is designed primarily for students and young professionals, but the product is not limited to university users.

Typical users:

- University students
- College students
- Freelancers
- Software developers
- Remote workers
- Entrepreneurs
- Creators
- Professionals with flexible schedules
- People who struggle with planning consistency

### Typical characteristics

Age:

**16–35**

Technology comfort:

**Medium to high**

Common behaviors:

- Uses a smartphone frequently
- Uses messaging and productivity applications
- Has irregular schedules
- Has multiple responsibilities
- Often creates plans but does not follow them exactly
- Wants structure without excessive manual planning

---

# 4. User Personas

## Persona 1 — The Irregular Student

A university student may have classes at different times throughout the week.

Their sleep schedule can also vary.

Example:

- Monday class: 9:00 AM
- Tuesday first class: 2:00 PM
- Wednesday: no class
- Thursday: 10:00 AM class

The student may sleep at 3:00 AM and wake up according to their first commitment.

They need Flowlly to understand that their available day is different every day.

---

## Persona 2 — The Overloaded Student

This user has:

- Classes
- Assignments
- Exams
- Personal tasks
- Exercise
- Social commitments
- Projects

Their problem is not knowing what tasks exist.

Their problem is deciding:

> "When should I actually do everything?"

---

## Persona 3 — The Freelancer

Their schedule is flexible but unpredictable.

They may have:

- Client work
- Meetings
- Personal projects
- Exercise
- Errands
- Deep work

Flowlly helps them turn an unstructured day into a realistic plan.

---

## Persona 4 — The Young Professional

They have fixed working hours but flexible personal time.

They want Flowlly to coordinate:

- Work
- Commute
- Exercise
- Family
- Personal projects
- Learning
- Sleep

---

# 5. Product Vision

> **Flowlly is the adaptive operating system for your day — continuously turning your commitments, priorities, and real-world changes into a realistic plan.**

Long-term, Flowlly should become a personal planning assistant that understands how users actually live rather than forcing users to live according to a rigid calendar.

---

# 6. Product Principles

## 6.1 Reality Over Perfection

Flowlly should generate plans users can realistically follow.

A schedule containing 15 perfectly organized tasks is worse than a schedule containing 7 achievable tasks.

---

## 6.2 Adapt Instead of Punish

Missing a task should not make the user feel like they failed.

Flowlly should simply adapt.

Instead of:

> "You missed your task."

Flowlly should communicate:

> "Your plan changed. I've reorganized the remaining day."

---

## 6.3 Minimum Manual Planning

Users should not have to manually drag every task around.

Flowlly should automate scheduling decisions wherever possible.

---

## 6.4 User Control

AI should recommend and organize.

The user remains in control.

Users can:

- Lock events
- Move tasks
- Change priorities
- Skip tasks
- Reschedule
- Override AI decisions

---

## 6.5 Context-Aware Scheduling

The same task may be scheduled differently depending on:

- Time
- Location
- Energy
- Available duration
- Deadline
- Priority
- Existing commitments
- User habits

---

# 7. Core Value Proposition

### Traditional Planner

User → Creates tasks → Creates schedule → Manually adjusts schedule

### Flowlly

User → Provides commitments/tasks → Deterministic scheduler creates plan → Reality changes → Deterministic scheduler replans

The key product loop is:

```text
PLAN
  ↓
LIVE DAY
  ↓
CHANGE
  ↓
REASSESS
  ↓
REPLAN
  ↓
CONTINUE
````

---

# 8. Core Features

Feature priority:

* **P0:** Required for MVP
* **P1:** Important after MVP
* **P2:** Future / Nice-to-have

---

# 9. Feature: Onboarding

**Priority:** P0

The onboarding process collects enough information to generate useful schedules.

### Information collected

#### Basic information

* Name
* Typical wake time
* Typical sleep time
* Timezone

#### Schedule preferences

* Work/study hours
* Preferred focus periods
* Preferred break duration
* Maximum deep-work duration

#### Life categories

Users can configure areas such as:

* University
* Work
* Study
* Health
* Fitness
* Personal
* Family
* Social
* Projects
* Hobbies

### Goal

The onboarding process should take less than 5 minutes.

---

# 10. Feature: Tasks

**Priority:** P0

Users can create tasks.

Each task can contain:

* Title
* Description
* Category
* Priority
* Deadline
* Estimated duration
* Energy requirement
* Optional location
* Optional simple daily or weekly recurrence (task recurrence only; recurring fixed events are post-MVP)

Example:

```text
Complete Database Assignment

Priority: High
Duration: 90 minutes
Deadline: Tomorrow
Energy: High
Category: University
```

---

# 11. Feature: Fixed Events

**Priority:** P0

Users can create events that Flowlly should treat as fixed commitments.

Examples:

* University class
* Work shift
* Meeting
* Doctor appointment
* Flight
* Dinner
* Event

Fixed events cannot be automatically moved unless the user explicitly allows it.

---

# 12. Feature: Flexible Tasks

**Priority:** P0

Tasks should be distinguishable from fixed events.

Example:

```text
Fixed:
10:00 AM — Database Lecture

Flexible:
Complete database assignment
Exercise
Read chapter 4
Buy groceries
```

Flowlly decides where flexible tasks fit around fixed commitments.

---

# 13. Feature: Deterministic Daily Schedule Generation

**Priority:** P0

This is the central feature of Flowlly.

The deterministic scheduler generates the daily schedule based on:

* Fixed events
* Tasks
* Deadlines
* Priorities
* Estimated duration
* Sleep schedule
* User availability
* Preferences
* Energy requirements
* Break requirements

Example:

```text
12:00 PM — Wake up
12:00–12:30 — Breakfast
12:30–2:00 — Database Assignment
2:00–2:30 — Break
2:30–4:00 — Study
4:00–5:00 — Gym
5:00–6:00 — Dinner
6:00–8:00 — Personal Project
8:00–8:30 — Break
8:30–10:00 — Assignment Revision
```

---

# 14. Feature: "I Woke Up" Button

**Priority:** P0

This is one of Flowlly's defining features.

The user should be able to open the app and tap:

> **I'M AWAKE**

When pressed, Flowlly recalculates the user's remaining day.

### Example

The user planned to wake at:

```text
9:00 AM
```

But actually wakes at:

```text
12:00 PM
```

Instead of showing the outdated schedule, Flowlly asks:

> "You're up. Want me to rebuild today's plan?"

The deterministic scheduler then generates a new schedule from the current time. AI may explain or suggest changes, but its output is a proposal and must pass deterministic validation.

### Important rule

The app should not treat waking late as a failure.

It should treat it as a new planning state.

---

# 15. Feature: Automatic Late Wake Detection

**Priority:** P1

Flowlly may eventually detect that a user has not started their day.

For example:

```text
Expected wake time: 9:00 AM
Current time: 11:45 AM
```

Flowlly can optionally ask:

> "Looks like your day hasn't started yet. Still sleeping?"

The user can respond:

* I'm awake
* Still sleeping
* Start my day later
* Don't ask again

---

# 16. Feature: Dynamic Replanning

**Priority:** P0

Users can trigger replanning at any time.

Examples:

> "I woke up late."

> "I have a meeting at 4."

> "I don't want to study right now."

> "Move my workout."

> "I have only two hours left."

Flowlly recalculates the remaining schedule.

---

# 17. Feature: "Replan My Day"

**Priority:** P0

A prominent action should exist on the daily schedule.

Example:

```text
[ Replan My Day ]
```

When pressed, Flowlly considers the current state and generates a new plan.

---

# 18. Feature: Task Completion

**Priority:** P0

Users can mark tasks as:

* Completed
* Skipped
* Partially completed
* Postponed

Completion should affect the remaining schedule.

Example:

```text
Task:
Study Operating Systems

Status:
Completed

Result:
Flowlly recalculates remaining tasks.
```

---

# 19. Feature: Missed Task Handling

**Priority:** P0

If a task's block has ended without completion, mark it `missed` as defined in `decisions.md`. Do not silently move or split it.

When replanning, the deterministic scheduler evaluates:

1. Is the task still important?
2. Is the deadline approaching?
3. How much time remains?
4. What other tasks are more important?
5. Can the task be moved without violating constraints?
6. Whether it can fit at its original duration; task splitting is post-MVP.

If it fits, offer the new plan as a version for the user to accept or keep the current plan:

```text
Your 2:00 PM study session was missed.

A proposed plan places it at 7:30 PM for its original duration.
Accept this plan or keep your current plan?
```

---

# 20. Feature: Smart Task Prioritization

**Priority:** P0

Flowlly should consider more than a manually assigned priority.

Priority can be influenced by:

* Deadline proximity
* Importance
* Estimated effort
* Dependencies
* User-defined priority
* Consequences of missing the task

Example:

A task due tomorrow should generally receive more scheduling attention than a task due next month.

---

# 21. Feature: Energy-Aware Scheduling

**Priority:** P1

Users can define their preferred energy patterns.

Example:

```text
Morning: High
Afternoon: Medium
Night: High
```

Flowlly can schedule demanding work during high-energy periods.

Low-energy tasks can be scheduled during lower-energy periods.

Example:

### High energy

* Coding
* Studying
* Writing
* Problem solving

### Low energy

* Email
* Cleaning
* Planning
* Reading
* Administrative tasks

---

# 22. Feature: Task Duration Intelligence

**Priority:** P1

Flowlly should learn whether the user consistently underestimates or overestimates task durations.

Example:

User estimates:

```text
Assignment = 60 minutes
```

Historical behavior:

```text
Average actual time = 95 minutes
```

Flowlly can eventually adjust scheduling estimates.

---

# 23. Feature: Task Splitting

**Status:** Post-MVP, per `decisions.md`.

**Priority:** P1

Large tasks can be automatically divided.

Example:

```text
Build mobile app
```

could become:

```text
Research
30 min

Architecture
45 min

Implement authentication
90 min

Build dashboard
90 min

Testing
45 min
```

The user should be able to approve or edit the breakdown.

---

# 24. Feature: Recurring Tasks

**Priority:** P0 for simple daily/weekly recurrence only; all other recurrence rules are post-MVP.

Users can create recurring activities.

Examples:

* Gym
* Reading
* Weekly planning
* Laundry
* Medication reminder
* Classes
* Work shifts

Recurrence options:

* Daily
* Weekly

Weekday-specific patterns, custom days, and custom intervals are post-MVP.

---

# 25. Feature: Habits

**Priority:** P1

Flowlly can support habits separately from one-time tasks.

Examples:

* Drink water
* Exercise
* Read
* Journal
* Sleep consistently

The AI can recommend when habits fit into the user's schedule.

---

# 26. Feature: Time Blocking

**Priority:** P0

Flowlly should use time-blocking internally.

Users should see clear blocks such as:

```text
2:00–3:30
Deep Work

3:30–4:00
Break

4:00–5:00
Study

5:00–6:00
Gym
```

---

# 27. Feature: Smart Breaks

**Priority:** P1

Flowlly should prevent unrealistic back-to-back scheduling.

Breaks may be inserted based on:

* Task duration
* User preferences
* Work intensity
* Previous activity
* Time of day

---

# 28. Feature: Buffer Time

**Priority:** P0

Use the default 10-minute transition buffer defined in `decisions.md`. This does not calculate route-based travel time.

Example:

```text
University → Gym
```

requires:

```text
10 minutes between blocks
```

The buffer provides transition time but does not calculate route-based travel.

---

# 29. Feature: Travel Time (Post-MVP)

**Priority:** P2

If location information is available, Flowlly can calculate travel time between events.

Example:

```text
Class
↓
15 min travel
↓
Gym
```

This should eventually integrate with mapping services.

---

# 30. Feature: Natural Language Input

**Priority:** P1

Users should be able to enter tasks naturally.

Example:

> "I need to finish my AI assignment by Sunday and it will probably take 3 hours."

Flowlly extracts:

```text
Task: AI assignment
Deadline: Sunday
Duration: 3 hours
```

---

# 31. Feature: Conversational Planning Assistant

**Priority:** P1

Users can interact with Flowlly through natural language.

Examples:

> "What should I do now?"

> "Can I fit a gym session today?"

> "Move my assignment to tomorrow."

> "I don't have enough energy for coding."

> "What can I skip today?"

The AI should respond based on the user's current schedule.

---

# 32. Feature: "What Should I Do Now?"

**Priority:** P0

The app should prominently show the current recommendation.

Example:

```text
WHAT SHOULD I DO NOW?

2:00–3:30 PM

Finish Database Assignment

High Priority
90 minutes

[ Start ]
```

This reduces decision fatigue.

---

# 33. Feature: Start Task

**Priority:** P0

Users can tap:

> Start

The task enters an active state.

Possible future functionality:

* Timer
* Focus mode
* Progress tracking
* Task notes

---

# 34. Feature: Focus Mode

**Priority:** P1

When starting a task, Flowlly can provide a distraction-free interface.

Example:

```text
DATABASE ASSIGNMENT

52:14 remaining

Goal:
Complete query optimization section

[Pause]
[Finish]
[Need More Time]
```

---

# 35. Feature: "Need More Time"

**Priority:** P1

Users can tell Flowlly:

> "I need another 30 minutes."

Flowlly evaluates the remaining schedule and adjusts future tasks.

---

# 36. Feature: "I'm Done Early"

**Priority:** P1

Users can indicate that they finished earlier than expected.

Flowlly can reclaim the available time.

Example:

```text
Planned:
2 hours

Actual:
1 hour

You gained 1 hour.

Would you like me to:
[Pull forward a task]
[Add a break]
[Keep the free time]
```

---

# 37. Feature: Daily Overview

**Priority:** P0

The home screen should show:

* Current time
* Current activity
* Next activity
* Remaining tasks
* Important deadlines
* Progress
* Replanning action

Example:

```text
Good afternoon, Hussain.

You're 34% through today's plan.

NOW
Database Assignment
12:30–2:00

NEXT
Lunch
2:00–2:30

LATER
Gym
5:00–6:00

[ Replan My Day ]
```

---

# 38. Feature: Calendar View

**Priority:** P1

Users can view schedules in:

* Day
* Week
* Month

The day view is the primary planning interface.

---

# 39. Feature: Weekly Planning

**Priority:** P1

At the beginning of a week, Flowlly can analyze:

* Upcoming deadlines
* Fixed events
* Tasks
* Goals
* Habits

Then suggest a weekly distribution of work.

---

# 40. Feature: Goals

**Priority:** P1

Users can define larger goals.

Example:

```text
Goal:
Learn React

Target:
Complete course in 30 days

Available:
1 hour/day
```

Flowlly can break the goal into smaller tasks.

---

# 41. Feature: Projects

**Priority:** P1

Users can organize related tasks into projects.

Example:

```text
Project:
Final Year Project

Tasks:
- Research
- Requirements
- UI Design
- Backend
- Testing
- Documentation
```

---

# 42. Feature: Deadline Intelligence

**Priority:** P1

Flowlly should warn users when a deadline is at risk.

Example:

> "You have 5 hours of remaining work but only 3 hours available before the deadline."

The app should suggest possible adjustments.

---

# 43. Feature: Daily Reflection

**Priority:** P2

At the end of the day, Flowlly can ask:

* What went well?
* What was difficult?
* What should change tomorrow?

This data can improve future scheduling.

---

# 44. Feature: Planning Intelligence

**Priority:** P2

Over time, Flowlly can learn:

* Typical task duration
* Typical wake time
* Typical sleep time
* Completion patterns
* Preferred work periods
* Frequently skipped tasks
* Productivity patterns

The goal is personalization, not surveillance.

---

# 45. Feature: Adaptive Sleep Schedule

**Priority:** P1

Flowlly should understand that users may not have a fixed wake time.

Example:

```text
Monday:
Wake 8:00 AM

Tuesday:
Wake 12:00 PM

Wednesday:
Wake 10:00 AM
```

The schedule should adapt to commitments.

---

# 46. Feature: Sleep-Aware Planning

**Priority:** P2

If the user reports sleeping very late or poorly, Flowlly may adjust the intensity of the day's plan.

Example:

```text
You slept 4 hours last night.

I've reduced today's high-intensity workload
and moved some flexible tasks.
```

The user remains in control of these adjustments.

---

# 47. Feature: Notifications

**Priority:** P0

Notifications should be useful rather than excessive.

The MVP supports basic in-app reminders only. Push, email, and browser notifications are post-MVP.

Potential notifications:

* Upcoming event
* Task starting
* Deadline approaching
* Schedule changed
* Replanning suggestion
* Day-start reminder

Users can configure notification behavior.

---

# 48. Feature: Schedule Explanation

**Priority:** P1

Beyond-template AI explanations are post-MVP. The MVP may use concise template-based explanations for significant scheduling decisions.

When available, users can ask:

> "Why did you schedule this?"

Example:

> "I scheduled your assignment first because it is due tomorrow and requires high focus. Your gym session was moved to 6 PM because it is flexible."

This improves trust.

---

# 49. Feature: Schedule Locking

**Priority:** P0

Users can lock important schedule blocks.

Example:

```text
10:00 AM — Class
LOCKED
```

Flowlly should not move locked blocks during replanning.

---

# 50. Feature: Manual Override

**Priority:** P0

Users can always manually change:

* Time
* Duration
* Priority
* Deadline
* Status

The deterministic scheduler should adapt around manual changes. Any AI suggestion is a proposal and must pass validation.

---

# 51. Feature: Undo Replan

**Priority:** P0

Users should be able to undo the latest accepted replan by restoring the previous active plan version.

Example:

```text
Schedule updated.

[Undo]
```

This is important for user trust.

---

# 52. Feature: Offline Support

**Priority:** P2

Offline schedule viewing and task completion are post-MVP.

AI replanning may require connectivity.

---

# 53. Feature: Integrations (Post-MVP)

**Priority:** P2

Future integrations:

* Google Calendar
* Apple Calendar
* Outlook Calendar
* Google Tasks
* Notion
* Todoist
* Maps
* Wearables

Calendar integrations should primarily provide fixed commitments and availability.

---

# 54. AI Planning Support and Scheduler Output

The deterministic scheduler is the core schedule-generation component. AI may provide proposals or explanations, but cannot decide or persist the canonical plan.

It should receive structured context.

Example:

```json
{
  "current_time": "12:30",
  "wake_time": "12:00",
  "sleep_time": "03:00",
  "fixed_events": [],
  "tasks": [],
  "preferences": {},
  "energy": "medium",
  "remaining_available_time": "8h"
}
```

Example deterministic scheduler output (not AI output):

The `reasoning` field is scheduler-generated explanation metadata, not private AI reasoning.

```json
{
  "schedule": [],
  "unscheduled_tasks": [],
  "reasoning": [],
  "warnings": []
}
```

---

# 55. Scheduling Constraints

The scheduling engine should consider:

## Hard Constraints

These should generally not be violated.

Examples:

* Locked events
* Fixed meetings
* Class times
* Deadlines
* User availability

## Soft Constraints

These can be adjusted.

Examples:

* Preferred workout time
* Preferred study time
* Break preferences
* Ideal task ordering
* Preferred wake time

---

# 56. Scheduler Scoring Inputs

A possible set of scheduler scoring inputs:

```text
Urgency
+
Importance
+
Deadline
+
User Priority
+
Task Dependencies
+
Energy Match
+
Available Time
+
Historical Behavior
```

The exact algorithm may evolve during development.

The system should optimize for realistic completion rather than maximum task density.

---

# 57. Handling Conflicts

If the user has more work than available time, Flowlly should not pretend everything fits.

Example:

```text
You have 8 hours of work remaining
but only 5 hours available today.

Possible options:

1. Move lower-priority tasks
2. Reduce task durations
3. Split tasks
4. Continue tomorrow
5. Remove optional tasks
```

The user should be informed when the schedule is infeasible.

---

# 58. Home Screen

The home screen should answer three questions immediately:

### 1. Where am I?

Current time and schedule progress.

### 2. What should I do now?

Current recommended task.

### 3. What happens next?

Next scheduled block.

Primary actions:

```text
[ Start Task ]

[ Complete ]

[ Replan My Day ]

[ I'm Awake ]
```

---

# 59. Main Navigation

Suggested navigation:

```text
Home
Schedule
Tasks
Projects
Insights
Settings
```

---

# 60. Home Screen Example

```text
--------------------------------

Good afternoon

12:34 PM

TODAY
████████░░░░░░ 42%

NOW

Database Assignment
12:30–2:00

High Priority
1h 26m remaining

[ START ]

NEXT

Lunch
2:00–2:30

LATER

Gym
5:00–6:00

--------------------------------

[ REPLAN MY DAY ]

--------------------------------
```

---

# 61. Task Creation Flow

```text
Tap +
   ↓
Create Task
   ↓
Enter title
   ↓
Optional details
   ↓
Set deadline
   ↓
Set duration
   ↓
Set priority
   ↓
Save
   ↓
Flowlly schedules task
```

---

# 62. Daily Flow

```text
User wakes up
      ↓
Opens Flowlly
      ↓
Taps "I'm Awake"
      ↓
Scheduler evaluates today's commitments
      ↓
Deterministic scheduler generates schedule
      ↓
User follows schedule
      ↓
Task completed / skipped
      ↓
User chooses "Replan My Day" if the remaining schedule should change
      ↓
User continues
      ↓
Day ends
      ↓
Optional reflection
```

---

# 63. Replanning Flow

```text
Something changes
      ↓
User taps "Replan"
      ↓
Flowlly gathers current state
      ↓
Evaluate fixed commitments
      ↓
Evaluate remaining tasks
      ↓
Evaluate deadlines
      ↓
Evaluate available time
      ↓
Generate new schedule
      ↓
Show changes
      ↓
User accepts
      ↓
New schedule becomes active
```

---

# 64. "I'm Awake" Flow

```text
User opens app
      ↓
Current time detected
      ↓
User taps "I'm Awake"
      ↓
Flowlly identifies missed morning schedule
      ↓
Remove / reschedule outdated blocks
      ↓
Protect fixed commitments
      ↓
Prioritize important tasks
      ↓
Create realistic remaining schedule
      ↓
Display new plan
```

---

# 65. First-Time User Experience

The first-time experience should avoid overwhelming users.

### Step 1

Welcome:

> "Let's build a day that actually works for you."

### Step 2

Ask about normal schedule.

### Step 3

Ask about commitments.

### Step 4

Ask for initial tasks.

### Step 5

Generate first schedule.

### Step 6

Explain:

> "This plan isn't permanent. If your day changes, Flowlly changes with it."

---

# 66. MVP Definition

The MVP is exactly the scope in `decisions.md`; this section does not define a separate or expanded MVP.

## MVP Features

See `decisions.md` for the complete MVP list.

---

# 67. MVP User Journey

```text
Sign up
  ↓
Configure schedule
  ↓
Add commitments
  ↓
Add tasks
  ↓
Generate today's plan
  ↓
Follow plan
  ↓
Complete tasks
  ↓
Something changes
  ↓
Tap Replan
  ↓
Deterministic scheduler rebuilds remaining day
  ↓
Continue
```

---

# 68. Post-MVP Features

The post-MVP list is governed by `decisions.md`.

After the MVP proves the core concept, build:

* Natural language input
* Conversational AI
* Energy-aware planning
* Habit tracking
* Projects
* Goals
* Weekly planning
* Recurring tasks beyond simple daily/weekly
* Task splitting
* Focus mode
* Travel time
* Calendar integrations
* Productivity insights
* Adaptive learning
* Sleep-aware planning
* Wearable integrations

---

# 69. Future Features

Potential long-term features:

## Predictive Scheduling

Flowlly predicts when users are likely to be available.

## Automatic Replanning

The app can automatically adjust schedules after certain events.

## Smart Context

The app understands:

```text
Location
Time
Calendar
Weather
Energy
Sleep
Previous behavior
```

## Personal Planning Model

Flowlly develops a personalized model of how the user works.

---

# 70. What We Are NOT Building in V1

Flowlly should remain focused.

V1 will NOT attempt to build:

* A full project-management platform
* A social network
* A habit-only application
* A note-taking application
* A complete AI chatbot
* A meditation application
* A fitness tracker
* A financial planner
* A team collaboration suite
* A replacement for every calendar application
* A complex enterprise scheduling system

The core product remains:

> **Adaptive personal scheduling.**

---

# 71. Success Metrics

## North Star Metric

### Adaptive Schedule Completion Rate

Percentage of scheduled tasks that are completed, rescheduled successfully, or intentionally skipped without causing planning breakdown.

---

## Activation Metrics

Track:

* Signup completion
* Onboarding completion
* First task created
* First schedule generated
* First "I'm Awake" interaction
* First successful replan

---

## Engagement Metrics

Track:

* Daily active users
* Weekly active users
* Plans generated per user
* Replans per user
* Tasks completed
* Tasks rescheduled
* Days planned per week

---

## Retention Metrics

Track:

* Day 1 retention
* Day 7 retention
* Day 30 retention
* Weekly active users
* Monthly active users

---

## Product Quality Metrics

Track:

### Schedule Acceptance Rate

Percentage of AI schedule proposals accepted without manual modification.

### Replan Success Rate

Percentage of replans that users continue using.

### Task Completion Rate

Percentage of scheduled tasks completed.

### Schedule Override Rate

Percentage of AI proposals changed by the user.

### Deadline Success Rate

Percentage of deadline-based tasks completed before their deadlines.

---

# 72. User Satisfaction

Potential survey questions:

> "Did Flowlly help you know what to do next?"

> "Did Flowlly reduce the effort required to plan your day?"

> "Did Flowlly adapt well when your day changed?"

> "Would you use Flowlly again tomorrow?"

---

# 73. Technical Architecture

A possible architecture:

```text
                 Mobile / Web App
                        |
                        ↓
                API / Backend
                        |
          ┌─────────────┴─────────────┐
          ↓                           ↓
      Task Service              Calendar Service
          |                           |
          └─────────────┬─────────────┘
                        ↓
                Scheduling Engine
                        |
                        ↓
                    AI Layer
                        |
                        ↓
                Schedule Generator
                        |
                        ↓
                  User Schedule
```

---

# 74. Core Data Models

## User

```text
id
name
timezone
wake_preferences
sleep_preferences
energy_preferences
planning_preferences
created_at
```

## Task

```text
id
user_id
title
description
priority
duration
deadline
category
energy_requirement
status
created_at
```

## Event

```text
id
user_id
title
start_time
end_time
location
locked
recurrence
```

## Schedule Block

```text
id
user_id
task_id
start_time
end_time
type
status
```

## Daily Plan

```text
id
user_id
date
generated_at
version
status
```

---

# 75. AI Safety and Reliability

Flowlly should not blindly trust AI output.

The scheduling engine should enforce deterministic constraints around:

* Time conflicts
* Locked events
* Invalid dates
* Impossible schedules
* Task duration
* Deadlines
* User availability

AI may propose recommendations, while deterministic scheduling logic validates any proposed schedule changes. OpenAI is accessed only through a trusted server-side function; it never writes to the database.

---

# 76. AI Hallucination Prevention

AI proposals may use only known user data and must pass deterministic validation.

It should not invent:

* Events
* Deadlines
* Tasks
* Availability
* Locations
* User preferences

If required information is missing, Flowlly should ask the user.

---

# 77. Privacy

Flowlly may eventually contain highly personal information.

Therefore:

* User data should be encrypted
* Sensitive data should be minimized
* Users should control integrations
* Users should be able to delete their data
* AI processing should follow clear privacy policies
* Calendar permissions should be granular
* Location access should be optional

---

# 78. Monetization

Potential business model:

## Free

* Basic task management
* Daily scheduling
* Limited AI replanning
* Basic schedule history

## Pro

* Unlimited AI replanning
* Advanced personalization
* Natural language planning
* Calendar integrations
* Goals
* Projects
* Advanced insights
* Adaptive scheduling intelligence

Pricing should be validated through user research rather than assumed initially.

---

# 79. Competitive Positioning

Flowlly should not position itself simply as:

> "An AI calendar."

The stronger positioning is:

> **"A planner that adapts when real life changes."**

Traditional tools are generally optimized around creating schedules.

Flowlly is optimized around maintaining a useful schedule despite change.

---

# 80. Core Differentiator

The defining product loop is:

```text
PLAN → LIVE → CHANGE → REPLAN → CONTINUE
```

The "I'm Awake" feature is a concrete example of this philosophy.

Instead of assuming:

```text
Wake at 8 AM
```

Flowlly works with reality:

```text
Actually woke at 12 PM
        ↓
Recalculate
        ↓
Create realistic remaining day
```

---

# 81. Product Tone

Flowlly should feel:

* Calm
* Intelligent
* Helpful
* Practical
* Personal
* Non-judgmental
* Modern
* Lightweight

Avoid language that makes users feel guilty.

Avoid:

> "You failed to complete your schedule."

Prefer:

> "Your day changed. Let's adjust the plan."

---

# 82. UX Principles

## One Primary Action

At any moment, the user should know what action matters most.

## Low Cognitive Load

Do not expose unnecessary scheduling complexity.

## Progressive Disclosure

Advanced controls should appear only when needed.

## Fast Interaction

Adding a task should take seconds.

## Explainable AI

Important AI decisions should be understandable.

## Reversible Changes

AI changes should be easy to undo.

---

# 83. MVP Screens

The MVP should contain approximately:

1. Splash / Welcome
2. Sign Up / Login
3. Onboarding
4. Home / Today
5. Task Creation
6. Task Details
7. Schedule
8. Replan Confirmation
9. Deterministic Scheduler Plan
10. Calendar / Events
11. Settings
12. Notifications

---

# 84. MVP Home Screen Priorities

The home screen should prioritize:

```text
Current state
      ↓
Current task
      ↓
Next task
      ↓
Remaining day
      ↓
Important actions
```

Not:

```text
Statistics
Charts
Long task lists
Complex settings
```

---

# 85. Example Daily Scenario

User has:

```text
Class:
3:00–4:00 PM

Assignment:
2 hours
Deadline tomorrow

Gym:
1 hour

Personal project:
2 hours
```

User wakes at:

```text
12:00 PM
```

Flowlly generates:

```text
12:00–12:30
Breakfast

12:30–2:30
Assignment

2:30–3:00
Travel / preparation

3:00–4:00
Class

4:00–4:30
Break

4:30–5:30
Gym

5:30–6:00
Dinner

6:00–8:00
Personal Project
```

If the assignment takes 3 hours instead of 2:

```text
User:
"I need another hour."
```

Flowlly recalculates.

Potential result:

```text
Assignment
12:30–3:00

Class
3:00–4:00

Gym
4:30–5:30

Personal Project
6:30–7:30
```

The schedule changes without requiring the user to manually rebuild everything.

---

# 86. Edge Cases

Flowlly must handle:

### No tasks

Show a lightweight day rather than generating unnecessary work.

### Too many tasks

Clearly communicate that everything cannot fit.

### No available time

Identify the conflict.

### Missed deadline

Help reorganize remaining work without hiding the missed deadline.

### User wakes extremely late

Generate a realistic shortened day.

### User wakes before normal time

Use the additional available time appropriately.

### User repeatedly ignores plans

Reduce planning density and investigate preferences rather than continually generating unrealistic schedules.

### User changes their mind

Allow easy task movement or cancellation.

### The scheduler cannot find a valid schedule

Explain why and ask the user for a decision.

---

# 87. MVP Acceptance Criteria

Acceptance criteria are the complete MVP scope in `decisions.md`; specifically, verify each listed capability, including task and fixed-event management, deterministic daily planning, Today current/next/later, actual wake-time capture and replanning, plan accept/keep/undo, manual replan and flexible-block editing, schedule locking, in-app reminders, accessibility, and error/empty states.

---

# 88. Product Success Criteria

The product should demonstrate that users can:

1. Spend less time manually planning.
2. Recover more easily when plans change.
3. Understand what they should do next.
4. Complete meaningful tasks more consistently.
5. Continue using the product because it adapts to their real lives.

---

# 89. Development Roadmap

## Phase 1 — Foundation

Build:

* Authentication
* User profile
* Task management
* Event management
* Database
* Basic UI

---

## Phase 2 — Scheduling Engine

Build:

* Availability calculation
* Conflict detection
* Time blocking
* Priority logic
* Deadline logic
* Schedule generation

---

## Phase 3 — AI

Build:

* Deterministic daily plan generation
* Natural language task parsing
* Schedule explanations
* Replanning
* "I'm Awake" logic

---

## Phase 4 — Adaptive Planning

Build:

* Missed-task handling
* Dynamic rescheduling
* Duration learning
* Energy-aware planning
* Task splitting

---

## Phase 5 — Personalization

Build:

* Behavioral learning
* Habit intelligence
* Weekly planning
* Productivity insights
* Personalized scheduling

---

## Phase 6 — Integrations

Build:

* Google Calendar
* Apple Calendar
* Outlook
* Maps
* Wearables
* External task managers

---

# 90. Future Product Direction

Eventually, Flowlly should move from:

> **"A tool that helps you plan your day."**

toward:

> **"A personal system that continuously manages the relationship between your intentions and your actual life."**

The long-term product should understand:

```text
What you need to do
+
What you want to achieve
+
When you are available
+
How you actually behave
+
What is happening right now
=
What you should do next
```

---

# 91. North Star

Flowlly should ultimately answer one question extremely well:

> **"Given everything happening in my life right now, what should I do next?"**

Everything else in the product should support that goal.

---

# 92. Final Product Definition

**Flowlly is an adaptive AI life planner that continuously turns tasks, commitments, goals, and real-world changes into a realistic schedule.**

It is not simply:

* A calendar
* A to-do list
* A habit tracker
* A university planner
* A chatbot

It is a combination of these capabilities centered around one core concept:

> **Your plan should adapt to your life — not the other way around.**

```
```
