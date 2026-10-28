Authority: decisions.md. If this file conflicts with decisions.md, decisions.md wins.

Date: 2026-10-28

# Open Questions

Only unresolved choices are listed here. Locked decisions are not questions. The two options under each item are alternatives for the product owner to choose or replace; neither is an approved answer. Do not implement an unresolved choice as fact.

## Product behavior

### OQ-001: What happens when a user has no saved IANA timezone?
**Answer:** Option A
**Status:** Answered
- **Files that depend on it:** `decisions.md`, `database-schema.md`, `user_flow.md`, `notifications.md`, `testing.md`
- **Options:** A. Initialize it from the device's current IANA timezone at account setup. B. Use UTC until the user chooses an IANA timezone.

- **Related decisions:** D-006

### OQ-002: What is the MVP scheduler score formula and which factors have weights?
**Answer:** Option B
**Status:** Open
- **Files that depend on it:** `decisions.md`, `prd.md`, `trd.md`, `implementation_plan.md`, `feature_ticket_list.md`, `database-schema.md`, `testing.md`
- **Options:** A. Use a weighted formula with an explicitly approved MVP factor list and weights. B. Use an explicitly approved ordered set of priority rules without weighted factors.

- **Related decisions:** D-009

### OQ-003: What does a Task deadline mean and how is its boundary evaluated?
**Answer:** Option B. Consistent with D-027
**Status:** Answered
- **Files that depend on it:** `prd.md`, `trd.md`, `database-schema.md`, `API_CONTRACTS.md`, `app_flow.md`, `flow.md`, `user_flow.md`, `implementation_plan.md`, `testing.md`
- **Options:** A. A deadline is a date in the user's timezone and remains valid through that local date. B. A deadline is an exact instant stored and evaluated in UTC, displayed in the user's timezone.

- **Related decisions:** D-006, D-009

### OQ-004: Are all-day Events supported in MVP?
**Answer:** Option B. Consistent with D-001
**Status:** Answered
- **Files that depend on it:** `prd.md`, `trd.md`, `database-schema.md`, `API_CONTRACTS.md`, `flow.md`, `user_flow.md`, `testing.md`
- **Options:** A. Support date-based all-day Events separately from timed Events. B. Support timed Events only in MVP.

- **Related decisions:** D-001, D-006

### OQ-005: What is the MVP category scope and what happens to assigned items when a category is deleted?
**Answer:** Option B
**Status:** Answered
- **Files that depend on it:** `prd.md`, `trd.md`, `database-schema.md`, `API_CONTRACTS.md`, `app_flow.md`, `flow.md`, `user_flow.md`, `feature_ticket_list.md`
- **Options:** A. Categories are post-MVP and are excluded from MVP forms and schema. B. Include user-owned categories in MVP and retain associated items with no category after category deletion.

- **Related decisions:** D-001, D-003

### OQ-006: What deletion and recovery behavior applies to Tasks and Events?
**Answer:** Option A
**Status:** Answered
- **Files that depend on it:** `prd.md`, `trd.md`, `database-schema.md`, `API_CONTRACTS.md`, `app_flow.md`, `flow.md`, `user_flow.md`, `notifications.md`, `testing.md`
- **Options:** A. Soft-delete records and provide an undo or restore path. B. Delete records immediately after confirmation, retaining only required audit data.

- **Related decisions:** D-001, D-003, D-018

### OQ-007: What behavior applies when a monthly recurrence date does not exist in a month?
**Answer:** Option A
**Status:** Answered
- **Files that depend on it:** `decisions.md`, `trd.md`, `database-schema.md`, `API_CONTRACTS.md`, `flow.md`, `user_flow.md`, `notifications.md`, `testing.md`
- **Options:** A. Use the last calendar day of that month. B. Skip that month's Occurrence.

- **Related decisions:** D-013, D-014

### OQ-008: How are recurrence local times resolved at daylight-saving transitions?
**Answer:** Option A
**Status:** Answered
- **Files that depend on it:** `decisions.md`, `database-schema.md`, `API_CONTRACTS.md`, `flow.md`, `user_flow.md`, `notifications.md`, `testing.md`
- **Options:** A. For a nonexistent local time, move to the next valid time; for a repeated time, use the earlier instant. B. For a nonexistent local time, skip the Occurrence; for a repeated time, use the later instant.

- **Related decisions:** D-006, D-014

### OQ-009: How is recurrence end-of-series configured in MVP?
**Answer:** Option B
**Status:** Answered
- **Files that depend on it:** `database-schema.md`, `API_CONTRACTS.md`, `flow.md`, `user_flow.md`, `testing.md`
- **Options:** A. Support an unbounded series and an end date. B. Support an end date and a maximum occurrence count.

- **Related decisions:** D-013, D-014

### OQ-010: Are recurring Occurrences calculated on demand or materialized in storage?
**Answer:** Option A
**Status:** Answered
- **Files that depend on it:** `database-schema.md`, `API_CONTRACTS.md`, `trd.md`, `implementation_plan.md`, `notifications.md`, `testing.md`
- **Options:** A. Calculate Occurrences for requested date ranges and persist only Exceptions and per-Occurrence state. B. Persist Occurrence records with stable identities and update them as the series changes.

- **Related decisions:** D-003, D-014, D-015

### OQ-011: Is a user account required before using MVP scheduling features?
**Answer:** Option B
**Status:** Answered
- **Files that depend on it:** `prd.md`, `trd.md`, `API_CONTRACTS.md`, `app_flow.md`, `flow.md`, `user_flow.md`, `security_rules.md`, `testing.md`
- **Options:** A. Require Supabase email/password authentication before creating or viewing schedule data. B. Allow a local guest schedule and require authentication only for cloud persistence or synchronization.

- **Related decisions:** D-003, D-005

### OQ-012: Is a dedicated Calendar screen required in MVP, or is Schedule navigation sufficient?
**Answer:** Option A
**Status:** Answered
- **Files that depend on it:** `prd.md`, `trd.md`, `app_flow.md`, `flow.md`, `user_flow.md`, `design.md`, `feature_ticket_list.md`
- **Options:** A. Include a separate Calendar destination. B. Provide date navigation within Schedule and no separate Calendar destination.

- **Related decisions:** D-001

### OQ-013: Which schedule items can be marked completed?
**Answer:** Option A
**Status:** Answered
- **Files that depend on it:** `trd.md`, `database-schema.md`, `API_CONTRACTS.md`, `app_flow.md`, `flow.md`, `user_flow.md`, `notifications.md`, `testing.md`
- **Options:** A. Only Tasks can be completed; Events remain commitments. B. Tasks and Plan Blocks can be marked completed independently of their source item.

- **Related decisions:** D-010, D-011

### OQ-014: What priority scale and default priority apply to Tasks?
**Answer:** Option A
**Status:** Open
- **Files that depend on it:** `prd.md`, `trd.md`, `database-schema.md`, `API_CONTRACTS.md`, `app_flow.md`, `feature_ticket_list.md`, `testing.md`
- **Options:** A. Use a fixed ordered scale with a documented default. B. Use named priority levels with a documented default.

- **Related decisions:** D-009

## AI, notification, and platform values

### OQ-015: What numerical token caps and rate limits apply to AI plan generation and replanning?
**Answer:** Option B
**Status:** Open
- **Files that depend on it:** `decisions.md`, `trd.md`, `API_CONTRACTS.md`, `security_rules.md`, `ERROR_HANDLING.md`, `testing.md`
- **Options:** A. Set per-request token caps and per-user request limits over a defined time window. B. Set per-user token budgets and request limits over a defined time window.

- **Related decisions:** D-016, D-017

### OQ-016: What schema is the canonical AI response validated against?
**Answer:** Option B
**Status:** Answered
- **Files that depend on it:** `trd.md`, `API_CONTRACTS.md`, `database-schema.md`, `implementation_plan.md`, `feature_ticket_list.md`, `testing.md`
- **Options:** A. Validate a complete proposed Plan Version payload. B. Validate a structured set of proposed changes to the current Plan Version.

- **Related decisions:** D-016

### OQ-017: Which reminder offsets and reminder multiplicity are available in MVP?
**Answer:** Option B
**Status:** Answered
- **Files that depend on it:** `decisions.md`, `prd.md`, `trd.md`, `database-schema.md`, `API_CONTRACTS.md`, `notifications.md`, `flow.md`, `user_flow.md`, `testing.md`
- **Options:** A. One product-defined reminder offset per eligible item. B. User-selectable offsets with more than one reminder per eligible item.

- **Related decisions:** D-018, D-019

### OQ-018: Are quiet hours part of MVP notification preferences?
**Answer:** Option A. Consistent with D-001
**Status:** Answered
- **Files that depend on it:** `notifications.md`, `database-schema.md`, `API_CONTRACTS.md`, `app_flow.md`, `user_flow.md`, `testing.md`
- **Options:** A. Exclude quiet hours from MVP. B. Include a user-configured local-time quiet-hours window.

- **Related decisions:** D-018, D-019

### OQ-019: What happens to pending Task reminders when the Task is completed?
**Answer:** Option A
**Status:** Answered
- **Files that depend on it:** `notifications.md`, `database-schema.md`, `API_CONTRACTS.md`, `flow.md`, `user_flow.md`, `testing.md`
- **Options:** A. Cancel all pending reminders for the completed Task and its Occurrences. B. Keep reminders for future recurring Occurrences but cancel the completed Occurrence's reminders.

- **Related decisions:** D-014, D-018, D-019

### OQ-020: What Android notification channel configuration and local-notification package are approved?
**Answer:** Option A
**Status:** Open
- **Files that depend on it:** `decisions.md`, `notifications.md`, `trd.md`, `implementation_plan.md`, `CI_CD.MD`, `testing.md`
- **Options:** A. Use one user-visible reminders channel and the package selected after Android/iOS compatibility review. B. Use separate channels for distinct reminder classes and the package selected after the same review.

- **Related decisions:** D-002, D-018

### OQ-021: Which mechanism implements required post-reboot notification recovery on each platform?
**Answer:** Option B
**Status:** Answered
- **Files that depend on it:** `decisions.md`, `notifications.md`, `trd.md`, `implementation_plan.md`, `testing.md`
- **Options:** A. Use the selected notification package's built-in reboot rescheduling support where available. B. Add app-owned platform integration for reboot recovery where the package does not provide it.

- **Related decisions:** D-018

### OQ-022: Which minimum Android and iOS versions must Flowly support?
**Answer:** Option A
**Status:** Open
- **Files that depend on it:** `decisions.md`, `trd.md`, `notifications.md`, `CI_CD.MD`, `testing.md`, `feature_ticket_list.md`
- **Options:** A. Set separate minimum OS versions for Android and iOS. B. Set one support policy based on a minimum OS release date, then derive each platform's minimum version.

- **Related decisions:** D-002, D-018, D-022

## Data, operations, and experience

### OQ-023: What is the account deletion and associated-data retention policy?
**Answer:** Option B
**Status:** Open
- **Files that depend on it:** `database-schema.md`, `API_CONTRACTS.md`, `security_rules.md`, `ERROR_HANDLING.md`, `flow.md`, `user_flow.md`, `testing.md`
- **Options:** A. Permanently delete user-owned data as part of account deletion, subject to legally required retention. B. Disable the account and retain user-owned data for a defined retention period before deletion.

- **Related decisions:** D-003, D-005

### OQ-024: Which languages are supported at MVP launch?
**Answer:** Option A
**Status:** Answered
- **Files that depend on it:** `design.md`, `notifications.md`, `ERROR_HANDLING.md`, `user_flow.md`, `testing.md`, `feature_ticket_list.md`
- **Options:** A. Ship English only with localization-ready strings. B. Ship English plus a product-owner-selected set of additional languages.

- **Related decisions:** D-002, D-020

### OQ-025: Which Flutter and Dart SDK versions are the project's supported baseline?
**Answer:** Option A
**Status:** Open
- **Files that depend on it:** `agents.md`, `CI_CD.MD`, `testing.md`, `trd.md`, `feature_ticket_list.md`
- **Options:** A. Pin exact Flutter and Dart versions in repository tooling. B. Declare minimum versions and validate against the current stable release in CI.

- **Related decisions:** D-002, D-003, D-022

### OQ-026: Which state-management and navigation packages are approved?
**Answer:** Option A
**Status:** Answered
- **Files that depend on it:** `trd.md`, `implementation_plan.md`, `testing.md`, `feature_ticket_list.md`, `agents.md`
- **Options:** A. Use Riverpod and GoRouter as named in the current initialization ticket. B. Select alternatives after documenting a package evaluation and updating the ticket.

- **Related decisions:** D-002, D-004

### OQ-027: Which CI provider and required merge gates are approved?
**Answer:** Option A
**Status:** Open
- **Files that depend on it:** `CI_CD.MD`, `testing.md`, `agents.md`, `feature_ticket_list.md`
- **Options:** A. Select a hosted CI provider and define required build, analysis, test, and migration checks. B. Use repository-hosted automation and define the same required checks there.

- **Related decisions:** D-003, D-022

### OQ-028: What numeric test coverage and performance budgets are release or merge gates?
**Answer:** Option A
**Status:** Open
- **Files that depend on it:** `testing.md`, `CI_CD.MD`, `agents.md`, `trd.md`
- **Options:** A. Set explicit numeric thresholds for coverage and performance. B. Require trend reporting and regression review without a numeric blocking threshold initially.

- **Related decisions:** D-002, D-003, D-022

### OQ-029: What ordering is used for Today and schedule list items when timestamps are equal or absent?
**Answer:** Option A
**Status:** Answered
- **Files that depend on it:** `flow.md`, `app_flow.md`, `user_flow.md`, `API_CONTRACTS.md`, `testing.md`
- **Options:** A. Order scheduled items by start time, then stable ID; put unscheduled Tasks in a separate section ordered by deadline and priority. B. Interleave scheduled and unscheduled items using an explicitly approved deadline and priority ordering.

- **Related decisions:** D-009, D-010

### OQ-030: Confirm the canonical user flow document
**Status:** Open
- **Files that depend on it:** `decisions.md`, `app_flow.md`, `flow.md`, `user_flow.md`
- **Options:** A. Confirm `app_flow.md` as canonical, with `flow.md` and `user_flow.md` superseded. B. Select `flow.md` or `user_flow.md` as canonical instead.
- **Related decisions:** D-056

### OQ-031: How should the accent color pairing meet WCAG 2.2 AA contrast?
**Status:** Open
- **Files that depend on it:** `decisions.md`, `design.md`
- **Options:** A. Darken the accent until white text reaches 4.5:1. B. Keep the accent and use dark text on it.
- **Related decisions:** D-057
