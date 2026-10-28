# Flowlly — Product and Architecture Decisions

This document is the single source of truth for product scope and architecture. The PRD, TRD, app flow, design, implementation plan, security rules, error-handling standards, feature tickets, and development status must defer to it. Where another document conflicts with a decision here, this document takes precedence.

## 1. Product role of AI

**Decision:** AI may propose, explain, or interpret natural language; the deterministic scheduler validates proposals, the server decides what is valid and persists changes, and the user confirms significant changes. AI never writes to the database directly.

**Rationale:** Deterministic validation and explicit server authorization protect schedule integrity and user control.

## 2. MVP scope

**Decision:** The following is the complete MVP definition; anything not listed is post-MVP.

MVP:
- Auth (email/password), onboarding, scheduling preferences
- Tasks (create, edit, complete, skip, delete/archive; simple daily/weekly recurrence only; editing includes moving, resizing, and deleting a flexible plan block)
- Fixed events (create, edit; recurring fixed events are post-MVP)
- Deterministic daily plan generation
- Today screen (current, next, later)
- “I’m Awake” with actual wake time and replanning
- Plan versions with accept / keep current, including undo of the latest accepted replan by restoring the previous active version
- Manual “Replan My Day”
- Schedule locking (MVP; protects fixed commitments the user chooses)
- Basic in-app reminders
- Accessibility baseline, error and empty states

Post-MVP:
- AI explanations beyond templates
- Natural-language task input
- Projects, goals, insights
- Calendar integrations
- Recurring fixed events
- Recurring tasks beyond simple daily/weekly
- Task splitting
- Energy-aware planning
- Travel time
- Personalization

**Rationale:** A deliberately bounded MVP centers on reliable daily planning and user control; supporting engineering work does not add product features to this scope.

## 3. Authentication and data access

**Decision:** Use Supabase Auth with email/password. Enforce ownership with PostgreSQL Row Level Security (RLS) policies on every user-owned table; Edge Functions must authenticate requests and preserve the user's authorization context. Never trust client-supplied user IDs.

**Rationale:** Supabase Auth and PostgreSQL RLS provide a consistent authorization boundary for the Flutter client and Supabase backend.

## 4. Database and migrations

**Decision:** Use Supabase-hosted PostgreSQL and manage schema changes with versioned Supabase CLI migrations.

**Rationale:** Supabase keeps authentication, PostgreSQL, RLS, migrations, and server functions within the selected backend platform.

## 5. Folder layout

**Decision:** Use Flutter/Dart for the client and TypeScript/Deno for Supabase Edge Functions. Keep privileged scheduling, AI calls, and authoritative plan validation server-side. Follow this repository layout:

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
test/                             # Dart unit and widget tests
integration_test/                 # Flutter integration tests
```

**Rationale:** This separates Flutter presentation from server-authoritative scheduling and AI, while keeping database migrations and TypeScript/Deno Edge Functions in the Supabase project.

## 6. Day boundary

**Decision:** Each user's planning day starts at `day_start_time`, defaulting to 06:00 in that user's timezone. Activities crossing this boundary belong to the planning day on which they start. Sleep is not a scheduling block; it is a constraint derived from wake and sleep preferences.

**Rationale:** A user-specific boundary models planning days consistently without treating sleep itself as an activity to schedule.

## 7. Time model

**Decision:** Store timestamps in UTC; interpret and schedule them in the user's IANA timezone. Scheduling granularity is 15 minutes.

**Rationale:** UTC storage avoids ambiguous instants while IANA timezone calculations preserve local planning behavior and consistent placement precision.

## 8. Scheduler determinism

**Decision:** The same scheduler input must produce the same output. Break ties by higher score, earlier deadline, earlier creation time, then task ID in lexicographic order.

**Rationale:** Stable results make plans explainable, testable, and reproducible.

## 9. Plan item states

**Decision:** Plan items use these states: `planned`, `in_progress`, `completed`, `skipped`, `missed`, `protected` (fixed or locked), and `superseded` (part of a prior plan version).

**Rationale:** Explicit lifecycle states preserve execution history and distinguish protected commitments from flexible work.

## 10. Past and in-progress blocks during replanning

**Decision:** At replanning time, a block whose end time is earlier than now becomes `missed` if it is not completed. A block currently in progress stays in place until its end unless the user explicitly skips it.

**Rationale:** Replanning should preserve work already underway and make elapsed, unfinished work visible rather than silently rescheduling it.

## 11. Transitions

**Decision:** Use a default 10-minute buffer between blocks. Travel time is post-MVP.

**Rationale:** A small transition buffer makes plans practical while avoiding travel modeling in the MVP.

## 12. Language and tone

**Decision:** Do not use AI branding in the UI. User-facing copy must avoid guilt language.

**Rationale:** Flowlly should feel calm and supportive, with the schedule—not an AI persona—as the product's focus.

## 13. AI provider and secret handling

**Decision:** Use the OpenAI API for AI features. Store `OPENAI_API_KEY` only as a Supabase Edge Function secret; never include it in the Flutter app, client configuration, logs, or source control. AI features remain subject to the MVP boundary above.

**Rationale:** A server-held provider key allows the app to call AI through a trusted boundary without exposing credentials or bypassing deterministic validation.

## 14. Deployment

**Decision:** Build the client for Android and iOS with Flutter; use Supabase for hosted backend services. Store-publishing accounts and release procedures must be confirmed before launch.

**Rationale:** This matches the selected cross-platform mobile client and managed backend.

## Open Questions

- Confirm whether the Google Stitch screens referenced by FT-002 and FT-076 exist and are available to the implementation team; their existence has not been verified in this documentation pass.
