# Flowlly — Development Status

> **Relationship to other documents:** `decisions.md` is authoritative for MVP scope and architecture; this status report records progress and risks only.

**Date:** October 25, 2026
**Project Lead:** Cline (AI Assistant)

## 1. Current Project State

The repository contains an earlier Next.js starter shell, but the approved product architecture is now Flutter/Dart with Supabase. That shell does not satisfy the approved stack and must be replaced or archived before feature implementation proceeds.

The Flutter application, core product domain, Supabase schema/RLS policies, Edge Functions, and production infrastructure remain to be implemented.

## 2. Existing Files

The following key documentation files are present in `docs/`:

*   `decisions.md`: Single source of truth for MVP scope and architecture.
*   `app_flow.md`: Defines application flows and navigation for desktop and mobile.
*   `design.md`: Outlines the product design philosophy, visual direction, color system, typography, and UI component guidelines.
*   `feature_ticket_list.md`: Converts PRD/TRD into actionable engineering tickets with priorities and coding prompts.
*   `implementation_plan.md`: Details architecture, technology stack, implementation order, and technical pipelines.
*   `prd.md`: Product requirements, target users, features, and success metrics.
*   `security_rules.md`: Security principles, secrets, authentication, authorization, and API security.
*   `trd.md`: Technical requirements, architecture, and testing strategy.
*   `ERROR_HANDLING.md`: Flutter and Supabase error-handling standards.

## 3. Confirmed Architecture

### High-Level System Architecture
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

### Approved Technology Stack
*   **Client:** Flutter/Dart, Material 3 restyled per `design.md`, Riverpod, and GoRouter.
*   **Backend:** Supabase Edge Functions for privileged operations, AI calls, and authoritative scheduling/validation.
*   **Database:** Supabase-hosted PostgreSQL with RLS.
*   **Authentication:** Supabase Auth with email/password for the MVP.
*   **AI Integration:** OpenAI API through authenticated Edge Functions. `OPENAI_API_KEY` is stored only in Supabase secrets; AI output is a proposal and must pass deterministic validation.
*   **Migrations:** Versioned Supabase CLI migrations.
*   **Deployment:** Flutter Android/iOS builds with Supabase-hosted backend; store-release procedures remain to be documented.
*   **Testing:** Dart analysis/formatting, Flutter unit/widget/integration tests, and Supabase RLS/Edge Function tests.

### Core Principles
*   **AI as Assistant, not Controller:** AI proposes changes; the deterministic engine validates and the server decides. AI never directly modifies the database.
*   **Security First:** Never trust the client, server-side validation, strict authorization (user data ownership), environment variable management, rate limiting.
*   **User Experience:** Minimal, calm, adaptive interface focusing on "what to do now" and seamless adaptation. Avoid generic AI SaaS aesthetics.

### Data Models (Core, from PRD §74)
*   **User:** `id`, `name`, `timezone`, `wake_preferences`, `sleep_preferences`, `planning_preferences`, `created_at`.
*   **Task:** `id`, `user_id`, `title`, `description`, `priority`, `duration`, `deadline`, `category`, `status`, `created_at`; simple daily/weekly recurrence only.
*   **Event:** `id`, `user_id`, `title`, `start_time`, `end_time`, `location`, `locked`; recurring fixed events and external-source metadata are post-MVP.
*   **Schedule Block:** `id`, `user_id`, `task_id`, `start_time`, `end_time`, `type`, `status`.
*   **Daily Plan:** `id`, `user_id`, `date`, `generated_at`, `version`, `status`.

## 4. Remaining Implementation Details

This pass records the approved Flutter/Supabase/OpenAI architecture in `decisions.md` and aligns the docs on client/server boundaries, RLS, Edge Function secrets, fixed-event recurrence scope, and error handling. The existing Next.js starter shell remains an implementation migration task, not the target architecture.

Open implementation choices that do not change the decisions:
*   **Error/monitoring tooling:** Production logging required, specific tools not chosen.
*   **Internationalization:** Not addressed.

## 5. Implementation Progress

### ⚠ FT-001 — Previous Web Starter (Not complete for the approved stack)

The existing `flowlly-app/` directory was initialized as a Next.js prototype on September 19, 2026. This is historical project state only and is not the approved architecture. Re-scaffold or replace it with Flutter/Dart and Supabase before implementing product features.

**Previous prototype layout (not canonical):**
```text
flowlly-app/
└── Next.js prototype files
```

The canonical Flutter/Supabase layout is in `decisions.md`.

**FT-002 design reference:** The existence and availability of the referenced Google Stitch screens are unverified. Until confirmed, use `design.md` as the source of design tokens.

### Core Design Token Cross-Check

The canonical values in `design.md` and `implementation_plan.md` are:

* Canvas: `#F5F6F7`
* Ink: `#151618`
* Accent: `#C85BD3`

### Documentation Consistency Pass — October 25, 2026

Aligned the documentation with the user-approved Flutter/Dart, Supabase, and OpenAI direction; clarified RLS ownership, server-only AI secrets, recurring fixed-event scope, and error-handling precedence. Updated files: `decisions.md`, `prd.md`, `trd.md`, `app_flow.md`, `design.md`, `implementation_plan.md`, `security_rules.md`, `feature_ticket_list.md`, `development-status.md`, `ERROR_HANDLING.md`, and `flowlly-app/.env.example`.

The previous Next.js prototype had passed its historical npm checks, but those results do not validate the approved Flutter/Supabase stack. Flutter and Supabase validation remains pending because the approved implementation has not yet been scaffolded.

## 6. Recommended Next Milestone

**FT-001 — Re-scaffold for Flutter and Supabase:** Replace or archive the Next.js prototype, initialize the Flutter project, set up Supabase locally, and implement Auth, database migrations, RLS policies, and Edge Function scaffolding.

## 7. Risks / Blockers

*   **Migration work:** The existing Next.js prototype is not compatible with the approved Flutter/Supabase architecture; do not build new MVP features on it.
*   **Supabase authorization:** RLS policies must be in place and cross-user tested before exposing user-owned tables.
*   **OpenAI key handling:** `OPENAI_API_KEY` must remain a Supabase Edge Function secret and must never enter Flutter configuration.
*   **AI validation complexity (upcoming):** The deterministic validator must strictly enforce all constraints on AI proposals (FT-044/FT-045); schedule early testing.
*   **Scheduler performance (upcoming):** Replanning algorithms must be unit-tested from the start (TRD §75).
