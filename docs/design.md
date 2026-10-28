# Flowlly — Product Design System

> **Relationship to other documents:** `decisions.md` is authoritative for MVP scope and architecture. This design system must defer to it wherever scope or architecture differs.

## 1. Design Philosophy

Flutter Material 3 may be used as a base for component behavior and accessibility. Components must be restyled to meet this design system; default Material appearance is not acceptable.

Flowlly is a **personal life scheduling system**, not a productivity dashboard.

The interface should feel:

* Minimal
* Calm
* Premium
* Intelligent
* Personal
* Quiet
* Fast
* Slightly futuristic
* Human
* Non-judgmental

The design should **not** look like a typical AI-generated SaaS interface.

Avoid:

* Excessive cards
* Excessive rounded rectangles
* Generic dashboard layouts
* Gradient-heavy backgrounds
* Purple AI aesthetics
* Floating blobs
* Excessive icons
* Excessive shadows
* Dense KPI dashboards
* "AI assistant" chat layouts
* Every section being inside a card
* Decorative glass everywhere
* Generic default Material appearance

The goal is:

> **A calm interface that gets out of the user's way.**

The user should feel that Flowlly is helping them organize their life rather than asking them to manage another productivity system.

---

# 2. Core Visual Direction

### Design Concept

**Quiet glass + porcelain canvas + ink typography + one energetic accent.**

The UI combines:

```text
Minimal editorial interface
        +
Soft glass surfaces
        +
Subtle translucency
        +
Strong typography
        +
Precise spacing
        +
Occasional vivid accent
```

Glassmorphism is a **supporting material**, not the identity of the entire interface.

Use glass primarily for:

* Floating navigation
* Contextual controls
* Today's active task
* Modal/dialog surfaces
* Floating action areas
* Sticky controls
* Replanning overlays

Do NOT make every card glass.

---

# 3. Visual Personality

Flowlly should feel closer to:

```text
premium personal software
        +
modern editorial design
        +
quiet mobile OS interface
```

and less like:

```text
AI SaaS dashboard
        +
startup landing page
        +
Notion clone
        +
generic productivity app
```

---

# 4. Color System

## Primary Canvas

```css
--color-canvas: #F5F6F7;
```

This is the primary application background.

It should never feel pure white.

---

## Primary Text

```css
--color-ink: #151618;
```

Use for:

* Main headings
* Important text
* Primary navigation
* Active states
* Important schedule items

---

## Secondary Text

```css
--color-stone: #74777A;
```

Use for:

* Descriptions
* Metadata
* Secondary labels
* Timestamps
* Supporting information

---

## Tertiary Text

```css
--color-mist: #AEB2B5;
```

Use sparingly.

---

## Surface

```css
--color-white: #FFFFFF;
```

Used for:

* Content surfaces
* Task panels
* Calendar surfaces
* Dialogs
* Important containers

---

## Accent

Flowlly uses a single recognizable accent.

```css
--color-accent: #C85BD3;
```

The accent should communicate:

* Active
* Selected
* Important
* Interactive
* Adaptive

Do not use accent everywhere.

---

## Success

```css
--color-success: #3E8A61;
```

---

## Warning

```css
--color-warning: #D98A27;
```

---

## Error

```css
--color-error: #D94B55;
```

---

# 5. Color Ratio

Target visual ratio:

```text
85–90%
Neutral colors

8–12%
Secondary surfaces / subtle borders

1–3%
Accent
```

The accent should feel special because it is rare.

Do not create entire sections with accent backgrounds.

---

# 6. Glassmorphism System

Glass should be subtle.

The visual goal is:

> **You notice the depth before you notice the effect.**

Never create obvious "frosted glass UI" everywhere.

---

## Glass Base

```css
background:
  rgba(255, 255, 255, 0.58);

backdrop-filter:
  blur(24px)
  saturate(120%);

-webkit-backdrop-filter:
  blur(24px)
  saturate(120%);

border:
  1px solid rgba(255, 255, 255, 0.72);
```

---

## Glass Dark

For dark floating surfaces:

```css
background:
  rgba(21, 22, 24, 0.82);

backdrop-filter:
  blur(24px);

border:
  1px solid rgba(255, 255, 255, 0.08);
```

---

## Glass Highlight

Important glass surfaces may have:

```css
box-shadow:
  inset 0 1px 0 rgba(255,255,255,.65);
```

Do not combine this with heavy external shadows.

---

# 7. Glass Rules

### Use glass for:

* Floating navigation
* Active task overlay
* Replanning modal
* Floating controls
* Sticky bottom actions
* Context menus
* Command/search palette

### Do not use glass for:

* Every task
* Every calendar event
* Every section
* Every statistic
* Basic list rows
* Large page backgrounds

Glass should create **hierarchy**, not noise.

---

# 8. Background Treatment

The application background should remain mostly flat.

Optional subtle ambient lighting:

```css
background:
  radial-gradient(
    600px 400px at 85% 0%,
    rgba(200, 91, 211, 0.06),
    transparent 70%
  ),
  #F5F6F7;
```

Only one or two extremely subtle ambient areas should exist.

Never use:

```text
purple blobs
rainbow gradients
mesh gradients
neon glows
animated background particles
```

---

# 9. Typography

Typography is one of the most important parts of the Flowlly identity.

Preferred font:

```text
Roobert
```

Fallback:

```text
Inter
Manrope
Satoshi
ui-sans-serif
system-ui
sans-serif
```

---

# 10. Typography Scale

## Display

```css
font-size: 56px;
line-height: 1;
font-weight: 700;
letter-spacing: -0.045em;
```

Used rarely.

---

## Page Heading

```css
font-size: 40px;
line-height: 1.05;
font-weight: 700;
letter-spacing: -0.04em;
```

---

## Section Heading

```css
font-size: 28px;
line-height: 1.15;
font-weight: 650;
letter-spacing: -0.035em;
```

---

## Card Heading

```css
font-size: 20px;
line-height: 1.25;
font-weight: 600;
letter-spacing: -0.025em;
```

---

## Body

```css
font-size: 15px;
line-height: 1.55;
font-weight: 400;
```

---

## Small

```css
font-size: 13px;
line-height: 1.45;
```

---

## Caption

```css
font-size: 11px;
line-height: 1.4;
font-weight: 600;
letter-spacing: .08em;
text-transform: uppercase;
```

---

# 11. Typography Philosophy

Avoid oversized typography everywhere.

Large type should communicate hierarchy.

Example:

```text
Good afternoon.

12:42
```

should feel more important than:

```text
Today's Schedule
Your Productivity
Tasks Completed
Upcoming Events
```

The application should not constantly shout at the user.

---

# 12. Spacing System

Use an 8px base system.

```text
4px
8px
12px
16px
24px
32px
40px
48px
64px
80px
96px
```

Primary spacing values:

```css
--space-1: 4px;
--space-2: 8px;
--space-3: 12px;
--space-4: 16px;
--space-6: 24px;
--space-8: 32px;
--space-10: 40px;
--space-12: 48px;
--space-16: 64px;
--space-20: 80px;
--space-24: 96px;
```

---

# 13. Layout Philosophy

Do not fill the screen.

Whitespace is a functional component of the interface.

The user should be able to visually understand:

```text
Where am I?
What should I do now?
What comes next?
What changed?
```

within seconds.

---

# 14. Application Shell

Desktop layout:

```text
┌────────────────────────────────────────────────────────────┐
│                                                            │
│   Flowlly                         Search       Profile      │
│                                                            │
│  ┌────────────┐                                          │
│  │ Today      │              MAIN CONTENT                │
│  │ Schedule   │                                          │
│  │ Tasks      │                                          │
│  │ Projects   │                                          │
│  │ Goals      │                                          │
│  │ Insights   │                                          │
│  │            │                                          │
│  │ Settings   │                                          │
│  └────────────┘                                          │
│                                                            │
└────────────────────────────────────────────────────────────┘
```

But the sidebar should not look like a conventional admin dashboard.

---

# 15. Sidebar

The sidebar should be visually quiet.

Background:

```css
transparent;
```

or:

```css
rgba(255,255,255,.38);
```

Only the active navigation item receives a filled surface.

Example:

```text
Today
Schedule
Tasks
Projects
Goals
Insights

──────────────

Settings
```

Avoid:

```text
large icons
colored navigation
huge active backgrounds
heavy sidebar borders
```

---

# 16. Floating Navigation Variant

On larger screens, an alternative navigation treatment may be used.

```text
                 ┌─────────────────────────────┐
                 │ Today  Schedule  Tasks  ... │
                 └─────────────────────────────┘
                       glass floating bar
```

Properties:

```css
border-radius: 999px;
background: rgba(255,255,255,.62);
backdrop-filter: blur(20px);
border: 1px solid rgba(255,255,255,.72);
```

This should only be used if it does not reduce usability.

---

# 17. Mobile Navigation

Use a floating bottom navigation.

```text
        ┌───────────────────────────────┐
        │  Today   Schedule   Tasks More │
        └───────────────────────────────┘
```

Properties:

```css
position: fixed;
bottom: 16px;
left: 16px;
right: 16px;

border-radius: 24px;

background:
  rgba(255,255,255,.72);

backdrop-filter:
  blur(24px);
```

The navigation should visually float above the application.

---

# 18. Today Screen

Today is the most important screen.

It should not resemble a calendar dashboard.

It should feel like a **personal daily briefing**.

---

## Today Layout

```text
Good afternoon.

Thursday, September 19
12:42 PM


NOW

Database Assignment
12:30 — 2:00

[Start]


NEXT

Lunch
2:00 — 2:30


LATER

Gym
5:00 — 6:00


────────────────────

5 tasks remaining

                    [Replan]
```

---

# 19. Today Hero

The top section should contain:

```text
Greeting
Current time
Day progress
```

Example:

```text
Good afternoon.

12:42 PM

4h 18m remaining
```

Do not turn this into a collection of statistic cards.

---

# 20. Current Task

The current task receives the strongest visual treatment.

Example:

```text
┌──────────────────────────────────────┐
│                                      │
│ NOW                                  │
│                                      │
│ Database Assignment                  │
│                                      │
│ 12:30 — 2:00                         │
│ Deep work · 90 min                   │
│                                      │
│                         [Start]      │
│                                      │
└──────────────────────────────────────┘
```

This may use subtle glass.

```css
background: rgba(255,255,255,.62);
backdrop-filter: blur(24px);
border: 1px solid rgba(255,255,255,.75);
```

---

# 21. Task Hierarchy

The task list should use visual hierarchy instead of card stacking.

```text
NOW
Database Assignment
12:30 — 2:00


NEXT
Lunch
2:00 — 2:30


LATER
Gym
5:00 — 6:00


LATER
Project Work
7:00 — 8:30
```

Only the active item needs a container.

---

# 22. Timeline Design

Avoid excessive horizontal calendar grids.

Use a clean vertical timeline.

```text
12:30 ───── ● Database Assignment
            │
2:00  ───── ● Lunch
            │
5:00  ───── ● Gym
            │
7:00  ───── ● Project Work
```

Timeline lines should be:

```css
#DADCDD;
```

not dark.

---

# 23. Fixed Events

Fixed commitments receive a small visual indicator.

Example:

```text
● Class
  10:00 — 11:30
  FIXED
```

Use a tiny label rather than a large badge.

---

# 24. Flexible Tasks

Flexible tasks should look lighter.

```text
Database Assignment
90 min · Flexible
```

The system can move these during replanning.

---

# 25. Completed Tasks

Completed tasks should visually disappear into the background.

Example:

```text
✓ Read Chapter 4
  45 min
```

Use:

```css
color: #9B9EA1;
text-decoration: line-through;
```

Do not use celebratory green cards.

Completion should feel calm.

---

# 26. "I'm Awake" Experience

This is a signature Flowlly interaction.

The screen should feel personal.

```text
Good afternoon.

Ready to start your day?

You woke up later than planned.

No problem.
I'll adjust today's schedule.


          [ I'm Awake ]
```

No excessive illustration.

No chatbot.

No modal overload.

---

# 27. Awake Button

Primary action:

```css
background: #C85BD3;
color: white;
border-radius: 19px;
```

Button:

```text
I'm Awake
```

Optional secondary action:

```text
Not yet
```

---

# 28. Replanning Experience

Replanning should feel like the application is quietly working.

Use a centered glass surface.

```text
┌───────────────────────────────────────┐
│                                       │
│       Rebuilding your day             │
│                                       │
│       ✓ Checking commitments          │
│       ✓ Protecting fixed events       │
│       ✓ Moving flexible tasks         │
│       • Finding the best sequence     │
│                                       │
└───────────────────────────────────────┘
```

Avoid:

```text
AI thinking animation
typing dots
robot icons
sparkles
large AI gradients
```

---

# 29. New Plan Review

The user must clearly understand what changed.

Example:

```text
Your new plan

Moved
Gym
5:00 → 6:30 PM

Protected
Database Assignment
12:30 → 2:00 PM

Rescheduled
Reading
8:30 → 9:15 PM
```

Primary:

```text
Accept New Plan
```

Secondary:

```text
Keep Current
```

---

# 30. Change Indicators

Use concise change language.

```text
Moved
Rescheduled
Added
Removed
Completed
Protected
```

Avoid technical terms such as:

```text
optimization pass
constraint resolution
AI inference
schedule mutation
```

---

# 31. Schedule Screen

The schedule screen should feel like a **timeline**, not a spreadsheet.

Desktop:

```text
Today                 Week

12 PM
│
├── Database Assignment
│
2 PM
│
├── Lunch
│
3 PM
│
│
5 PM
│
├── Gym
│
7 PM
│
└── Project Work
```

---

# 32. Calendar Grid

Use a grid only when users genuinely need precise time positioning.

Do not make the grid the default visual language of Flowlly.

---

# 33. Tasks Screen

Tasks should feel lightweight.

```text
Tasks

Today
────────────────────────

○ Database Assignment
  90 min · High

○ Read Chapter 4
  45 min

○ Gym
  60 min


Upcoming
────────────────────────

○ Submit report
  Tomorrow
```

---

# 34. Task Detail

Task detail should be simple.

```text
Database Assignment

90 min
High priority
Due today

Description

Complete database normalization
and query implementation.


Schedule
Today · 12:30 PM


[Complete]
```

---

# 35. Task Creation

Use a focused sheet/modal.

```text
New task

What needs to get done?

[________________________]

Duration
[ 60 min ]

Priority
[ Medium ]

Deadline
[ Today ]

[Create Task]
```

Do not show every possible field initially.

Progressively reveal advanced options.

---

# 36. Quick Add

The application should support quick capture.

```text
+ Add task
```

The input should support natural language eventually.

Example:

```text
Finish database assignment for 90 minutes tomorrow morning
```

Flowlly can parse:

```text
Task:
Finish database assignment

Duration:
90 minutes

Date:
Tomorrow

Preferred period:
Morning
```

---

# 37. Projects

Projects should not look like Kanban boards by default.

Use editorial lists.

```text
Projects

Semester Final Project
72% complete
12 tasks

Portfolio
38% complete
8 tasks

Fitness
Active
5 routines
```

---

# 38. Goals

Goals should focus on progress, not gamification.

Example:

```text
Improve fitness

██████████░░░ 72%

3 active routines
12 sessions this month
```

Avoid:

```text
streak flames
XP
badges
leaderboards
confetti
```

unless later research proves they are useful.

---

# 39. Insights

Insights should feel analytical but calm.

Avoid a wall of KPI cards.

Instead:

```text
Your week

You planned 31h 20m
You completed 26h 45m


Most consistent period

6 PM — 9 PM


Common pattern

Tasks scheduled after 11 PM
were moved more often.
```

Then show one or two clean charts.

---

# 40. Charts

Chart style:

* Minimal axes
* Thin lines
* No heavy grids
* No unnecessary legends
* No gradients
* One accent color
* Neutral secondary data

Example:

```text
Completion
│
│       ╭──╮
│   ╭───╯  ╰──╮
│───╯         ╰────
└──────────────────
 Mon Tue Wed Thu Fri
```

---

# 41. Cards

Cards should be used sparingly.

Default card:

```css
background: #FFFFFF;
border-radius: 32px;
padding: 24px;
```

No heavy shadow.

Optional:

```css
box-shadow:
  0 8px 24px rgba(20,22,24,.035);
```

But flat surfaces should be preferred.

---

# 42. Card Radius

Primary:

```text
32px
```

Large feature surfaces:

```text
40px
```

Small controls:

```text
16px
```

Buttons:

```text
19px
```

Pills:

```text
999px
```

Avoid random radius values.

---

# 43. Borders

Borders should be extremely subtle.

Default:

```css
border: 1px solid rgba(21,22,24,.07);
```

Glass:

```css
border: 1px solid rgba(255,255,255,.72);
```

Never use thick dark borders for ordinary surfaces.

---

# 44. Shadows

Shadows should be nearly invisible.

Default:

```css
box-shadow:
  0 8px 30px rgba(21,22,24,.035);
```

Glass:

```css
box-shadow:
  0 12px 40px rgba(21,22,24,.045);
```

Do not use:

```text
large black shadows
colored shadows
neon shadows
multiple stacked shadows
```

---

# 45. Buttons

## Primary

```css
background: #C85BD3;
color: white;
border-radius: 19px;
padding: 11px 16px;
font-weight: 600;
```

---

## Secondary

```css
background: rgba(255,255,255,.65);
color: #151618;
border: 1px solid rgba(21,22,24,.09);
border-radius: 19px;
```

---

## Dark

```css
background: #151618;
color: white;
border-radius: 19px;
```

---

## Ghost

```css
background: transparent;
color: #74777A;
```

No border unless needed.

---

# 46. Button Rules

Never put multiple competing primary buttons together.

Correct:

```text
[Accept New Plan]    Keep Current
```

Incorrect:

```text
[Accept] [Replan] [Optimize] [AI Suggest] [Continue]
```

The interface should make the next action obvious.

---

# 47. Inputs

Inputs should feel integrated rather than like form components.

```css
background: rgba(255,255,255,.65);
border: 1px solid rgba(21,22,24,.08);
border-radius: 16px;
padding: 12px 14px;
```

Focus:

```css
border-color: rgba(200,91,211,.55);
box-shadow: 0 0 0 3px rgba(200,91,211,.10);
```

---

# 48. Selects

Prefer compact controls.

Example:

```text
Duration     60 min   ↓
Priority     High     ↓
```

Do not create giant form controls.

---

# 49. Modal Design

Modals should use glass.

Background:

```css
rgba(255,255,255,.72)
```

Blur:

```css
backdrop-filter: blur(28px);
```

Radius:

```text
32px
```

Overlay:

```css
background: rgba(15,16,18,.20);
```

The underlying interface should remain faintly visible.

---

# 50. Bottom Sheets

On mobile, prefer bottom sheets over centered modals.

```text
────────────────────────

New Task

What needs to get done?

[______________________]

Duration
[60 min]

[Create Task]
```

Radius:

```text
32px 32px 0 0
```

---

# 51. Icons

Use a single icon family.

Recommended:

```text
Lucide
```

Icons should be:

```text
16px
18px
20px
```

Avoid:

* 3D icons
* colorful icons
* emoji as primary UI
* mixed icon libraries

---

# 52. Icon Philosophy

Icons should support text.

Do not make users interpret ambiguous icons.

Prefer:

```text
+ Add task
```

over:

```text
[ mysterious plus icon ]
```

---

# 53. Motion

Motion should be subtle and purposeful.

Use:

```css
transition:
  transform 180ms ease,
  opacity 180ms ease,
  background 180ms ease;
```

---

# 54. Motion Principles

### Enter

Fade + slight vertical movement.

```text
opacity: 0 → 1
translateY(4px → 0)
```

### Exit

Fade quickly.

### Modal

Slight scale:

```text
0.98 → 1
```

### Replanning

Progressive checklist updates.

Do not use infinite loaders when the operation has understandable steps.

---

# 55. Microinteractions

Good:

```text
Task completed
→ row softly fades

Schedule changed
→ moved task gently transitions

Plan accepted
→ new schedule replaces old schedule

I'm Awake
→ current day recalculates
```

Bad:

```text
confetti
explosions
sparkles
bouncing cards
large celebratory animations
```

Flowlly is calm.

---

# 56. Loading States

Prefer skeletons that resemble the actual content.

Avoid:

```text
Loading...
```

everywhere.

Example:

```text
████████████
██████

████████
██████████████
```

Use subtle opacity.

---

# 57. Empty States

Empty states should feel intentional.

Example:

```text
Your day is clear.

Nothing needs your attention right now.

[Add a task]
```

Do not use:

* Cartoon illustrations
* Generic AI illustrations
* Giant empty icons
* Stock photography

---

# 58. Error States

Errors should feel calm.

Example:

```text
We couldn't update your schedule.

Your previous plan is safe.

[Try again]
```

No red full-screen alerts.

---

# 59. Toasts

Use small floating glass toasts.

Example:

```text
┌────────────────────────────┐
│ ✓ Task completed           │
└────────────────────────────┘
```

Position:

```text
bottom: 24px
right: 24px
```

Mobile:

```text
bottom: 88px
left: 16px
right: 16px
```

---

# 60. AI Design Language

Flowlly should **not** advertise AI visually.

Do not use:

```text
✨ AI
🤖 AI Assistant
Magic AI
AI Brain
Sparkles
```

Instead, AI should be invisible infrastructure.

Use natural language:

```text
Your plan was adjusted.

You now have enough time for your remaining tasks.
```

Instead of:

```text
Your day has been adjusted around the changes.
```

---

# 61. AI Explanations

When explaining an adaptive decision:

```text
Why did this change?

You woke up 1h 40m later than planned.

Your class at 2:00 PM was fixed,
so the remaining flexible tasks were moved
into the available time.
```

This is much more trustworthy than presenting an "AI score".

---

# 62. Glass Usage Map

Recommended glass usage:

```text
Floating navigation       ████████
Replanning modal          ████████
Active task               ██████
Quick actions             █████
Context menus             █████
Standard cards            ██
Task rows                 █
Background                0
```

Glass should be an accent material.

---

# 63. Desktop Composition

The desktop interface should have large areas of breathing room.

Example:

```text
┌──────────────────────────────────────────────────────────┐
│ Flowlly                                     12:42   ◉    │
│                                                          │
│                                                          │
│ Good afternoon.                                          │
│                                                          │
│ 12:42 PM                                                 │
│                                                          │
│ ┌───────────────────────────────────────────────┐        │
│ │ NOW                                           │        │
│ │ Database Assignment                           │        │
│ │ 12:30 — 2:00                     [Start]      │        │
│ └───────────────────────────────────────────────┘        │
│                                                          │
│ NEXT                                                     │
│ Lunch                                      2:00 — 2:30   │
│                                                          │
│ LATER                                                    │
│ Gym                                        5:00 — 6:00   │
│                                                          │
└──────────────────────────────────────────────────────────┘
```

---

# 64. Mobile Composition

Mobile should be even simpler.

```text
Good afternoon.

12:42 PM


NOW

Database Assignment
12:30 — 2:00

[Start]


NEXT
Lunch
2:00 — 2:30


LATER
Gym
5:00 — 6:00


5 tasks remaining
```

Navigation floats at the bottom.

---

# 65. Responsive Breakpoints

Use the same breakpoints as the TRD and implementation plan:

```text
Mobile:
< 640px

Tablet:
640px – < 1024px

Desktop:
1024px – 1440px

Large:
> 1440px
```

Do not design separate products for every breakpoint.

Components should naturally adapt.

---

# 66. Maximum Width

Main content:

```css
max-width: 1200px;
```

For Today content:

```css
max-width: 900px;
```

The Today page should not stretch excessively on large monitors.

---

# 67. Content Density

Default density:

```text
Low → Medium
```

Users should not see dozens of objects simultaneously.

Prioritize:

```text
Current
Next
Later
```

rather than showing the entire day immediately.

---

# 68. Information Hierarchy

Every screen should follow:

```text
1. What is happening?
2. What should I do?
3. What happens next?
4. What changed?
5. Details
```

Never reverse this.

---

# 69. Design Principle — One Screen, One Job

Today:

> What should I do now?

Schedule:

> When is everything happening?

Tasks:

> What needs to get done?

Projects:

> What am I working toward?

Goals:

> What matters long-term?

Insights:

> What patterns are emerging?

Settings:

> How should Flowlly behave?

---

# 70. Design Principle — Don't Turn Everything Into a Card

Use cards only when grouping information improves comprehension.

Instead of:

```text
[ Task Card ]
[ Task Card ]
[ Task Card ]
[ Task Card ]
[ Task Card ]
```

Prefer:

```text
NOW

Database Assignment
12:30 — 2:00

──────────────────

NEXT

Lunch
2:00 — 2:30

──────────────────

LATER

Gym
5:00 — 6:00
```

---

# 71. Design Principle — Visual Silence

Leave intentional empty space.

Empty space communicates:

```text
calm
priority
confidence
focus
```

Do not fill empty areas just because there is available space.

---

# 72. Design Principle — No Dashboard Syndrome

Flowlly must not become:

```text
12 cards
7 charts
5 statistics
3 progress bars
2 gradients
```

on one screen.

The Today screen should primarily contain:

```text
Current moment
Current task
Next task
Remaining day
Primary action
```

---

# 73. Design Principle — Real Life Is Messy

The interface should never shame the user.

Avoid:

```text
You failed to complete 4 tasks.
```

Prefer:

```text
4 tasks remain.

We can fit 3 comfortably into the time left.
```

The system should help users adapt rather than punish deviation.

---

# 74. Design Principle — User Control

Automatic scheduling should always remain understandable.

Whenever Flowlly changes something important:

```text
What changed?

Why?

What can I do?
```

The user should never feel that the AI secretly changed their life.

---

# 75. Accessibility

Minimum requirements:

* WCAG AA contrast
* Keyboard navigation
* Visible focus states
* Screen-reader labels
* Touch targets ≥ 44px
* Reduced-motion support
* No information communicated by color alone
* Flutter `Semantics` labels and roles for accessible controls
* Accessible modal focus management

---

# 76. Reduced Motion

Respect:

```css
@media (prefers-reduced-motion: reduce) {
  * {
    animation-duration: 0.01ms !important;
    transition-duration: 0.01ms !important;
  }
}
```

Glass effects should remain usable without motion.

---

# 77. Dark Mode

Dark mode should be supported architecturally but should not simply invert colors.

Dark mode palette:

```css
--dark-background: #101113;
--dark-surface: #181A1D;
--dark-surface-soft: #202226;
--dark-text: #F3F4F5;
--dark-secondary: #969A9E;
--dark-border: rgba(255,255,255,.08);
--dark-accent: #D46DDE;
```

Glass:

```css
background: rgba(28,30,33,.72);
backdrop-filter: blur(24px);
```

---

# 78. Design Tokens

The token values and CSS snippets below specify the visual system. Implement them with Flutter `ThemeData`, theme extensions, and reusable styles rather than CSS.

```css
:root {
  /* Colors */
  --color-canvas: #F5F6F7;
  --color-surface: #FFFFFF;
  --color-ink: #151618;
  --color-graphite: #343638;
  --color-stone: #74777A;
  --color-mist: #AEB2B5;

  --color-accent: #C85BD3;
  --color-success: #3E8A61;
  --color-warning: #D98A27;
  --color-error: #D94B55;

  /* Glass */
  --glass-light: rgba(255,255,255,.58);
  --glass-border: rgba(255,255,255,.72);
  --glass-dark: rgba(21,22,24,.82);

  /* Typography */
  --font-sans:
    Roobert,
    Inter,
    Manrope,
    Satoshi,
    ui-sans-serif,
    system-ui,
    sans-serif;

  --text-xs: 11px;
  --text-sm: 13px;
  --text-base: 15px;
  --text-md: 18px;
  --text-lg: 20px;
  --text-xl: 28px;
  --text-2xl: 40px;
  --text-display: 56px;

  /* Radius */
  --radius-sm: 8px;
  --radius-md: 16px;
  --radius-lg: 24px;
  --radius-xl: 32px;
  --radius-2xl: 40px;
  --radius-pill: 999px;
  --radius-button: 19px;

  /* Spacing */
  --space-1: 4px;
  --space-2: 8px;
  --space-3: 12px;
  --space-4: 16px;
  --space-6: 24px;
  --space-8: 32px;
  --space-10: 40px;
  --space-12: 48px;
  --space-16: 64px;
  --space-20: 80px;
  --space-24: 96px;

  /* Shadows */
  --shadow-soft:
    0 8px 30px rgba(21,22,24,.035);

  --shadow-glass:
    0 12px 40px rgba(21,22,24,.045);
}
```

---

# 79. Component Architecture

Recommended Flutter UI organization:

```text
lib/core/widgets/     # Shared restyled primitives: Button, Input, Dialog, Toast
lib/features/
├── today/            # Today-specific widgets
├── schedule/         # Schedule-specific widgets
└── tasks/            # Task-specific widgets
```

Feature-specific widgets belong in their corresponding `lib/features/` module; shared primitives belong in `lib/core/widgets/`, per `decisions.md`.

---

# 80. GlassSurface Component

A reusable glass component should support:

```tsx
<GlassSurface>
  ...
</GlassSurface>
```

Variants:

```text
light
dark
interactive
floating
```

The component should not automatically add excessive shadows or animations.

---

# 81. Design Anti-Patterns

Flutter Material 3 is a base for behavior and accessibility only. Restyle every component to meet this document; the default Material appearance is not acceptable.

The following are explicitly prohibited unless there is a strong product reason.

### Generic AI Gradient

```text
purple → blue → pink
```

Do not use.

### Excessive Glass

Every component should not look transparent.

### Excessive Cards

Do not put every piece of information into a rounded rectangle.

### Dashboard KPI Walls

Avoid:

```text
Tasks
+12%

Focus
+23%

Productivity
87%

Efficiency
92%
```

### AI Chat UI

Flowlly is not primarily a chatbot.

### Decorative AI Symbols

Avoid:

```text
✨
🤖
magic wand
sparkles
brain icon
```

### Stock Illustrations

Avoid generic SaaS illustrations.

### Huge Gradients

Avoid gradient backgrounds.

### Neon

Avoid neon borders and glow effects.

---

# 82. Product Photography / Imagery

The application itself should be the primary visual.

Do not rely on:

* Lifestyle photography
* Generic people photos
* Stock images
* Abstract AI illustrations

If marketing imagery is needed, use:

```text
clean device mockups
real Flowlly screens
porcelain background
subtle shadows
```

---

# 83. Marketing Page Visual Direction

The landing page should follow the same design language.

Hero:

```text
Flowlly

Plan your life.
Then let your day adapt.

[Get Started] [See how it works]

                 iPhone
```

The phone UI should be the main visual artifact.

---

# 84. Marketing Glass Usage

Marketing pages can use slightly more glass than the product.

Use:

* Floating navigation
* Phone mockup
* Small glass feature panels
* Replanning demonstration
* Subtle ambient lighting

Still avoid excessive gradients.

---

# 85. Logo

Wordmark:

```text
flowlly
```

Lowercase.

Weight:

```text
700
```

Tracking:

```text
-0.05em
```

The logo should remain monochrome in the application.

A subtle accent mark may be used in marketing.

---

# 86. Accent Gradient

If a brand gradient is used, it should be extremely restricted.

Allowed:

```text
Logo mark
Marketing decorative accent
```

Not allowed:

```text
Buttons
Cards
Body text
Navigation
Backgrounds
Charts
```

The product interface should remain mostly monochrome.

---

# 87. Glass + Accent Interaction

Correct:

```text
Neutral glass surface
+
single magenta active control
```

Incorrect:

```text
Magenta glass
+
purple border
+
pink shadow
+
gradient background
```

---

# 88. Page Transition Philosophy

Transitions should preserve spatial continuity.

Example:

```text
Today
  ↓
Task detail
  ↓
Back
  ↓
Same task position
```

The interface should feel like one continuous environment.

---

# 89. Responsive Behavior

On smaller screens:

* Reduce side padding
* Collapse navigation
* Convert modals to sheets
* Stack content
* Reduce typography
* Preserve whitespace
* Keep the current task prominent

Do not simply shrink the desktop UI.

---

# 90. Mobile Priority

Mobile order:

```text
1. Current time
2. Current task
3. Next task
4. Remaining day
5. Quick actions
6. Navigation
```

Everything else is secondary.

---

# 91. Interaction Priority

Every screen should have:

```text
Primary action
Secondary action
Passive information
```

Never present ten equally strong actions.

---

# 92. Focus States

Focused controls:

```css
outline: none;

box-shadow:
  0 0 0 3px rgba(200,91,211,.12);
```

Use visible focus indicators for keyboard navigation.

---

# 93. Hover States

Hover should be subtle.

Example:

```css
background:
  rgba(21,22,24,.035);
```

Do not make elements jump or scale dramatically.

---

# 94. Active States

Active navigation:

```css
background: rgba(255,255,255,.72);
border: 1px solid rgba(255,255,255,.78);
```

Optional tiny accent indicator:

```text
● Today
```

or a 3px vertical accent.

Do not create large colored navigation blocks.

---

# 95. Destructive Actions

Delete buttons should not be permanently bright red.

Use neutral styling initially.

Red appears when:

```text
hover
confirmation
destructive state
```

---

# 96. Accessibility and Glass

Glass must never reduce readability.

If background content interferes with text:

```text
increase opacity
increase blur
remove glass
```

Do not sacrifice accessibility for aesthetics.

---

# 97. Design Quality Checklist

Before shipping a screen, verify:

### Visual

* [ ] Mostly neutral palette
* [ ] Accent used sparingly
* [ ] No unnecessary gradients
* [ ] No excessive cards
* [ ] No excessive glass
* [ ] No heavy shadows
* [ ] Strong whitespace
* [ ] Clear hierarchy

### Interaction

* [ ] One obvious primary action
* [ ] Current state is obvious
* [ ] Next action is obvious
* [ ] Changes are understandable
* [ ] Automatic actions can be reviewed

### Typography

* [ ] Correct font
* [ ] Consistent hierarchy
* [ ] No unnecessary all-caps
* [ ] Comfortable line height
* [ ] Proper contrast

### Responsive

* [ ] Works on mobile
* [ ] Works on tablet
* [ ] Works on desktop
* [ ] Touch targets are large enough
* [ ] Navigation adapts naturally

### Accessibility

* [ ] Keyboard accessible
* [ ] Screen-reader labels
* [ ] WCAG AA contrast
* [ ] Reduced motion supported
* [ ] Color is not the only status indicator

---

# 98. Final Design Rule

The most important Flowlly design rule is:

> **Make the interface feel obvious, not impressive.**

If a user notices the glass effect before they understand their next task, the design has failed.

If a user opens Flowlly and immediately knows:

```text
Where I am
What time it is
What I should do
What happens next
What changed
```

then the design is working.

Flowlly should feel like:

> **A quiet layer of intelligence sitting between the user's intentions and their actual day.**
