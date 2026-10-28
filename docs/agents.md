# AGENTS.md

# Schedular Project Instructions for AI Coding Agents

**Project:** Schedular  
**Primary platform:** Flutter mobile application  
**Target platforms:** Android and iOS  
**Document status:** Baseline project instructions  
**Applies to:** AI coding agents, automated development tools, and contributors working in this repository

---

## 1. Purpose

This document establishes the mandatory operating rules for anyone or any AI agent modifying the Schedular codebase.

Its purpose is to ensure that all changes are:

- Consistent with approved product requirements.
- Architecturally maintainable.
- Secure and privacy-conscious.
- Visually consistent with the design system.
- Correct in scheduling, recurrence, and reminder behavior.
- Covered by appropriate tests.
- Documented where necessary.
- Limited to the authorized scope of work.

Agents must prioritize correctness, clarity, reliability, and maintainability over implementation speed or unnecessary complexity.

A working screen is not sufficient evidence that a feature is complete. Its underlying behavior, persistence, error handling, and acceptance criteria must also be satisfied.

---

## 2. Core Rules

Every agent working on Schedular must follow these rules.

1. Read the relevant project documentation before modifying code.
2. Inspect the existing repository before proposing or implementing structural changes.
3. Follow the approved Flutter architecture and coding standards.
4. Never invent product requirements or silently expand scope.
5. Preserve existing working functionality unless the requested change requires modification.
6. Do not rewrite unrelated files or modules.
7. Never fabricate test results, coverage values, benchmark results, or build status.
8. Add or update tests for behavior changes.
9. Treat authentication, authorization, persistence, scheduling, and notifications as correctness-critical areas.
10. Follow `design.md` for all user-facing interfaces.
11. Do not introduce unapproved dependencies or architectural patterns.
12. Do not bypass validation, security controls, or database constraints to make a feature appear functional.
13. Document assumptions and unresolved decisions.
14. Report what was changed, what was tested, and what remains incomplete.
15. Prefer the smallest complete solution that meets the approved requirements.

These rules apply to new features, bug fixes, refactoring, UI redesigns, dependency upgrades, and generated code.

---

## 3. Project Documentation and Authority

The repository's project documents define product requirements and implementation constraints.

Before starting work, inspect the relevant documents, including:

- `AGENTS.md`
- `master_rules.md`
- `TRD.md`
- `USER_FLOWS.md`
- `design.md`
- `coding-standards.md`
- `database-schema.md`
- `TESTING.md`
- `CI_CD.md`
- `ARCHITECTURE.md`, if maintained
- `API_CONTRACTS.md`, if maintained
- `NOTIFICATIONS.md`, if maintained

Do not assume that every referenced document already exists. Verify the repository before relying on a file.

### 3.1 Resolving conflicts

When documents conflict:

1. Identify the conflicting requirements.
2. Determine whether one document explicitly establishes authority for the relevant decision.
3. Follow the approved project governance process.
4. Ask for clarification when the conflict cannot be resolved safely.
5. Do not silently rewrite a requirement to suit the current implementation.

The current approved product requirements determine feature scope. The current approved architecture determines implementation boundaries. `design.md` governs visual and interaction rules. `TESTING.md` governs verification expectations.

This file governs how agents work. It does not authorize agents to override product requirements or make unapproved architectural decisions.

### 3.2 Proposed versus approved decisions

A proposal in a project document is not automatically an approved implementation decision.

If a document marks a technology, feature, or workflow as provisional:

- Verify whether it has subsequently been approved.
- Do not treat illustrative examples as mandatory dependencies.
- Do not present proposed infrastructure as already configured.
- Ask for clarification when the decision materially affects the implementation.

---

## 4. Required Workflow Before Coding

Every nontrivial task must follow this workflow.

### Step 1: Understand the request

Identify:

- The requested outcome.
- The affected feature.
- The applicable acceptance criteria.
- The relevant user flows.
- The files and modules likely to change.
- Any security, data, or platform implications.

Do not begin by generating large amounts of code before understanding the task.

### Step 2: Inspect the repository

Review the relevant:

- Directory structure.
- Existing implementation.
- Related widgets and screens.
- State-management approach.
- Routing setup.
- Models and repositories.
- API clients.
- Database migrations.
- Existing tests.
- Dependency configuration.

Use the existing implementation as evidence of the current architecture, but do not assume that an existing pattern is correct if it conflicts with approved documentation.

### Step 3: Identify dependencies and risks

Determine whether the task affects:

- Public interfaces.
- Database schemas.
- API contracts.
- Authentication.
- Data migrations.
- Recurrence semantics.
- Reminder scheduling.
- Offline behavior.
- Navigation.
- Platform-specific behavior.
- Existing user data.

For risky changes, identify a safe migration, testing, and recovery strategy before implementation.

### Step 4: Define the smallest complete change

Decide which files and responsibilities need to change.

Avoid:

- Rewriting entire modules for a small fix.
- Introducing a new architecture unnecessarily.
- Adding abstractions without a demonstrated need.
- Combining unrelated refactors with feature work.
- Implementing unrequested features as a bonus.

### Step 5: Implement

Follow the project's coding, architecture, design, security, and testing rules.

### Step 6: Verify

Run the relevant available checks. Add tests where needed and inspect the final changes.

### Step 7: Report

Summarize the implementation, actual test results, limitations, and outstanding decisions.

---

## 5. Flutter Technology Rules

Schedular is a Flutter mobile application.

### 5.1 Required technology direction

- Use Flutter and Dart for the mobile application.
- Maintain Android and iOS compatibility within the approved platform targets.
- Use the Flutter SDK version established by the repository.
- Follow the project's selected state-management and navigation solutions.
- Use the approved backend and database integrations.
- Keep native platform code limited to cases where platform-specific behavior is necessary.

Do not replace Flutter with React Native, native Android, native iOS, or another application framework without explicit approval.

### 5.2 Version management

- Inspect the existing Flutter and Dart SDK constraints before changing them.
- Respect the project's version-management files and CI configuration.
- Do not upgrade major dependencies or SDK versions as an incidental part of unrelated work.
- Check dependency compatibility before upgrading.
- Update lockfiles through the approved package-management process.
- Test significant dependency upgrades on supported platforms.

### 5.3 Dependencies

Before adding a package:

1. Determine whether existing project functionality can satisfy the requirement.
2. Verify package maintenance and compatibility.
3. Evaluate security and licensing considerations.
4. Assess platform support.
5. Consider package size and long-term maintenance.
6. Use the project's established dependency-management conventions.

Do not add a package solely to avoid writing a small, straightforward implementation.

Do not silently replace an established dependency with a different library.

---

## 6. Architecture Rules

Follow the approved architecture document and existing module boundaries.

The exact architecture and libraries must be verified in the repository rather than assumed.

### 6.1 Separation of responsibilities

Maintain clear boundaries between:

- Presentation and widgets.
- Application state and orchestration.
- Domain models and business rules.
- Repositories and data access.
- Remote API integrations.
- Local persistence.
- Platform services.

Do not place database queries, authentication logic, recurrence calculations, or complex business rules directly inside widget build methods.

### 6.2 UI responsibilities

Widgets should primarily:

- Render application state.
- Collect user input.
- Dispatch user actions.
- Display loading, empty, success, and error states.
- Expose accessible semantics.

Widgets should not become the authoritative location for business rules or persistence behavior.

### 6.3 Domain responsibilities

Domain logic must remain testable without requiring a rendered Flutter screen.

Scheduling calculations, recurrence behavior, validation rules, and completion-state transitions should be isolated where practical.

### 6.4 Repository responsibilities

Repositories or the approved data-access layer should coordinate the relevant persistence operations.

They must handle:

- Data retrieval.
- Data updates.
- Serialization.
- Error mapping.
- Local and remote persistence where applicable.
- Synchronization where supported.
- Consistency with the approved domain model.

### 6.5 Avoid unnecessary abstraction

Do not create an abstraction merely because it is theoretically possible.

Introduce abstractions when they provide meaningful benefits, such as:

- Clear ownership of responsibilities.
- Replaceable infrastructure dependencies.
- Reusable domain logic.
- Easier testing.
- Reduced coupling.
- Consistent error handling.

---

## 7. State Management and Asynchronous Operations

Use the state-management solution already approved for the project.

Do not introduce another state-management library without authorization.

### 7.1 State requirements

Represent meaningful application states explicitly, including where relevant:

- Initial.
- Loading.
- Success.
- Empty.
- Validation error.
- Recoverable error.
- Authentication required.
- Pending write.
- Uncertain write outcome.
- Offline or stale data.

Avoid ambiguous combinations of booleans that allow impossible states.

### 7.2 Asynchronous operations

- Handle exceptions at appropriate boundaries.
- Avoid unhandled asynchronous errors.
- Prevent stale responses from overwriting newer state.
- Cancel or ignore obsolete operations where appropriate.
- Prevent repeated user actions from producing unintended duplicate operations.
- Clean up subscriptions and resources when their lifecycle ends.
- Do not leave indefinite loading indicators after an operation terminates.

### 7.3 Optimistic updates

Optimistic UI updates may be used only when their rollback and reconciliation behavior is defined.

If a write fails:

- Restore the previous state or show a clear pending/error state.
- Preserve user input where possible.
- Do not present an unconfirmed operation as permanently saved.
- Reconcile uncertain write outcomes with the authoritative data source where possible.

---

## 8. Navigation Rules

Follow the approved navigation library and routing architecture.

### Requirements

- Keep route definitions consistent.
- Validate incoming route parameters.
- Handle missing or deleted resources safely.
- Protect authenticated routes.
- Preserve predictable Android and iOS back behavior.
- Prevent duplicate navigation from repeated events.
- Handle cold-start and warm-start deep links where supported.
- Avoid bypassing authorization through navigation shortcuts.

A route must not be treated as an authorization mechanism.

If a user signs out, protected navigation state must be cleared or invalidated according to the approved session model.

---

## 9. Design System and UI Restrictions

`design.md` is mandatory for all user-facing UI.

The following restrictions are explicit project requirements.

### 9.1 Prohibited patterns

Never introduce:

- Purple or blue gradients.
- Pill-shaped buttons.
- Fake reviews or testimonials.
- Fake metrics or fabricated social proof.
- Vague hero headings.
- Emoji as interface icons.
- Excessive scroll-triggered animations.
- A "Made with AI" label.
- Decorative animations that interfere with task completion.
- Placeholder controls that appear functional but do nothing.

Avoid em dashes in user-facing interface copy.

### 9.2 Visual quality

Interfaces must use:

- A consistent typography system.
- Consistent spacing.
- Approved colors and surfaces.
- Predictable component states.
- Clear information hierarchy.
- Meaningful icons from the approved icon system.
- Appropriate contrast.
- Responsive layouts.
- Intentional empty, loading, and error states.

Do not invent new design tokens when an approved token already exists.

### 9.3 Buttons and controls

- Follow the approved button shape, size, and hierarchy.
- Distinguish primary, secondary, and destructive actions.
- Use clear labels.
- Provide appropriate loading and disabled states.
- Prevent accidental repeated submissions.
- Ensure controls are accessible.

### 9.4 Animation

Use animation only when it improves comprehension or feedback.

Avoid:

- Excessive scroll-based motion.
- Long transitions that delay actions.
- Motion that causes layout instability.
- Animation without a functional purpose.

Respect reduced-motion preferences where supported.

### 9.5 Honest interface behavior

Do not fabricate:

- Schedule items.
- Saved-state indicators.
- Synchronization status.
- Notification delivery status.
- User activity statistics.
- Testimonials.
- Review scores.

Mock data may be used in development and tests, but it must be clearly isolated from real user-facing production data.

---

## 10. Product Scope Control

The approved TRD defines the product scope.

Agents must not add unapproved features merely because they are common in other scheduling applications.

Potentially out-of-scope features include:

- AI-generated schedules.
- Team collaboration.
- Shared calendars.
- Public event pages.
- Social feeds.
- Reviews and ratings.
- Payments or subscriptions.
- Location tracking.
- Contact importing.
- Unapproved analytics.
- Email or SMS notifications.
- Complex project management.

These features may be implemented only after explicit approval.

### 10.1 Feature completeness

Do not create controls for features that do not work.

If a feature is not implemented:

- Omit the control, or
- Clearly identify it as unavailable only when the approved UX calls for that behavior.

Do not make a feature appear complete by hardcoding a successful result.

### 10.2 Scope changes

When a task appears to require changing product scope:

1. Explain the dependency.
2. Identify the relevant user impact.
3. State the alternatives.
4. Request approval when necessary.
5. Update the relevant requirements and documentation after approval.

---

## 11. Scheduling and Date-Time Correctness

Scheduling is a core domain of Schedular and must be treated as correctness-critical.

### 11.1 Date and time rules

- Distinguish date-only values from instants in time.
- Follow the approved time-zone storage strategy.
- Use consistent time representations across Flutter, backend, and database.
- Validate start and end times.
- Respect all-day event semantics.
- Do not silently change event times.
- Handle midnight and date-boundary conditions.
- Follow the documented policy for device time-zone changes.

### 11.2 Recurrence rules

- Implement only approved recurrence patterns.
- Keep recurrence expansion consistent between client and backend.
- Avoid duplicate occurrence generation.
- Preserve exceptions according to the approved model.
- Distinguish editing one occurrence from editing a series.
- Distinguish deleting one occurrence from deleting a series.
- Test month-end, leap-year, and daylight-saving behavior.

Do not approximate unsupported recurrence patterns.

### 11.3 Daylight-saving behavior

- Use a documented policy for nonexistent local times.
- Use a documented policy for ambiguous local times.
- Test transitions using explicit time zones.
- Do not assume a day always contains exactly 24 elapsed hours.
- Do not use fixed UTC offsets as substitutes for named time-zone rules when recurring local times require those rules.

### 11.4 Completion state

- Do not infer completion solely from the end time.
- Persist completion using the approved data model.
- Preserve the correct state during synchronization.
- Follow the approved behavior for recurring occurrences.

---

## 12. Reminder and Notification Rules

Reminder behavior must match the approved notification architecture.

### Requirements

- Request permissions when relevant to the user's action.
- Do not repeatedly prompt after denial without a meaningful user action.
- Handle permission denial without breaking unrelated scheduling features.
- Update reminders when relevant event fields change.
- Cancel obsolete reminders when appropriate.
- Avoid duplicate notification scheduling.
- Validate notification payloads.
- Handle deleted or inaccessible items safely.
- Test Android and iOS differences.
- Do not promise guaranteed delivery where the platform cannot guarantee it.

### Delivery status

Do not treat these states as equivalent:

- Reminder preference saved.
- Notification scheduling requested.
- Operating system accepted the scheduling request.
- Notification displayed.
- Notification delivery confirmed.

Only report the state supported by the actual integration.

### Notification routing

Notification taps must not bypass authentication or authorization.

If the target item no longer exists, the application must navigate to a safe fallback.

---

## 13. Authentication and Security

Security requirements apply to all features, not only authentication screens.

### 13.1 Authentication

- Use the approved authentication provider.
- Store session material using the approved secure-storage strategy.
- Handle expired sessions.
- Avoid exposing authentication secrets.
- Follow the provider's sign-out and recovery behavior.
- Do not invent unsupported authentication methods.

### 13.2 Authorization

- Enforce authorization at the backend and database where applicable.
- Verify ownership for user-specific resources.
- Do not rely on hidden buttons or client-side checks.
- Validate identifiers and request parameters.
- Prevent cross-account data access.
- Treat deep links and notification payloads as untrusted input.

### 13.3 Secrets

Never:

- Commit production credentials.
- Hardcode private API keys or tokens in source code.
- Print passwords or session tokens in logs.
- Put secrets in screenshots or test reports.
- Expose credentials to untrusted build workflows.

Use the approved environment configuration and secret-management process.

### 13.4 Logging

Logs must be useful without exposing sensitive information.

Avoid logging:

- Passwords.
- Access tokens.
- Recovery secrets.
- Private event descriptions unless necessary and explicitly approved.
- Unnecessary personal information.

### 13.5 Dependencies

Investigate relevant security advisories.

Do not silently suppress security findings or downgrade dependency versions to bypass a failing security check.

---

## 14. Database and Persistence Rules

Follow `database-schema.md` and the approved database architecture.

### Requirements

- Keep data models consistent with the approved schema.
- Respect foreign keys and constraints.
- Use transactions where atomicity is required.
- Handle persistence failures explicitly.
- Validate data on the backend.
- Avoid destructive migrations without approval.
- Preserve existing data during schema evolution.
- Test migration paths.
- Follow the approved authorization policy.

### 14.1 Migrations

Before changing a database schema:

1. Inspect the current schema and migration history.
2. Determine the impact on existing data.
3. Design a migration compatible with the deployment strategy.
4. Test the migration against a clean database.
5. Test against representative existing data where appropriate.
6. Validate constraints and authorization policies.
7. Document recovery requirements.
8. Update schema documentation and tests.

Do not assume that dropping and recreating a table is acceptable.

### 14.2 Persistence authority

When remote synchronization is used, follow the approved source-of-truth model.

Do not treat local cache contents as authoritative server data unless the architecture explicitly defines them as such.

### 14.3 Data deletion

Deleting an application record must follow the approved lifecycle rules for related data and reminders.

Do not silently remove related data beyond the authorized deletion scope.

---

## 15. API and Backend Integration

If a backend exists, follow its approved API contracts.

### Requirements

- Use the established API client and error-handling conventions.
- Keep request and response models consistent with the API contract.
- Handle timeouts and server errors.
- Validate backend responses.
- Avoid duplicating server-side business rules inconsistently.
- Respect authorization boundaries.
- Use pagination where required.
- Avoid unnecessary network requests.
- Prevent duplicate writes where supported.
- Do not expose internal server errors to users.

### Error mapping

Map technical failures into useful application-level outcomes.

For example:

- Validation failure.
- Authentication required.
- Permission denied.
- Resource not found.
- Conflict.
- Rate limit.
- Temporary service failure.
- Unknown error.

Do not discard the distinction between errors when it changes how the user should recover.

### API changes

When changing an API contract:

- Update client models.
- Update backend validation.
- Update contract tests.
- Update relevant documentation.
- Consider compatibility with existing application versions.

---

## 16. Offline Behavior and Synchronization

Offline support is conditional on the approved architecture.

Do not invent offline-write behavior if the product does not support it.

### If offline reads are supported

- Use valid cached data.
- Indicate stale data when necessary.
- Reconcile data after connectivity returns.
- Avoid presenting old data as newly confirmed.

### If offline writes are supported

- Store pending operations using the approved mechanism.
- Prevent duplicate synchronization.
- Define conflict-resolution behavior.
- Preserve pending operations appropriately across lifecycle events.
- Prevent cross-account leakage.
- Mark operations confirmed only after confirmation from the authoritative persistence layer.

### If offline writes are not supported

- Preserve the user's form input where possible.
- Explain the failure.
- Provide retry.
- Do not claim the operation has been queued.

---

## 17. Testing Requirements

Follow `TESTING.md`.

### 17.1 Required validation

Run relevant checks, including where applicable:

```bash
dart format --output=none --set-exit-if-changed .
flutter analyze
flutter test
```

Run additional backend, database, integration, and build checks when the change requires them.

### 17.2 Test selection

At minimum, determine whether the change requires:

- Unit tests.
- Widget tests.
- Integration tests.
- Backend tests.
- Database tests.
- Platform-specific tests.
- Regression tests.

### 17.3 Failure handling

- Do not ignore test failures.
- Do not delete assertions merely to obtain a passing result.
- Do not replace real integration verification with mocks and claim the integration passed.
- Do not claim that unexecuted commands succeeded.
- Document checks that could not be run.
- Investigate flaky tests instead of permanently bypassing them.

### 17.4 Test data

Use synthetic, isolated fixtures.

Never use production user data as a convenient testing shortcut.

### 17.5 Time-dependent tests

Use a controlled clock and explicit time-zone fixtures where required.

Do not write tests that depend on the machine's current time zone unless that behavior is specifically being tested.

---

## 18. Accessibility Requirements

All user-facing features must follow the accessibility requirements in `design.md` and `TESTING.md`.

Agents must consider:

- Accessible labels.
- Correct semantic roles.
- Current control states.
- Logical focus order.
- Keyboard navigation where applicable.
- Screen-reader support.
- Text scaling.
- Color contrast.
- Touch target size.
- Discoverable validation errors.
- Reduced-motion preferences where supported.

Do not remove accessibility semantics to simplify implementation.

Color alone must not communicate a critical state.

---

## 19. Performance Requirements

Avoid performance regressions and unnecessary complexity.

### Flutter

- Avoid expensive work in widget build methods.
- Avoid unnecessary rebuilds.
- Use efficient list rendering.
- Dispose resources appropriately.
- Avoid blocking the UI thread.
- Avoid unnecessary animations.
- Avoid repeated network requests caused by lifecycle mistakes.

### Backend and database

- Avoid unnecessary queries.
- Follow established indexing and pagination rules.
- Avoid repeated full-table retrieval.
- Respect request and concurrency limits.
- Evaluate migration and query-performance implications.

### Measurement

Do not invent performance budgets or benchmark values.

Use the project's approved targets and report measured results with the test environment identified.

---

## 20. Error Handling and Reliability

Every important operation must have defined success and failure behavior.

### Requirements

- Catch errors at appropriate boundaries.
- Avoid swallowing exceptions silently.
- Avoid displaying raw stack traces to users.
- Provide actionable feedback.
- Preserve user input where possible.
- Avoid indefinite loading states.
- Implement retry only where safe.
- Prevent duplicate operations.
- Reconcile uncertain write outcomes where possible.

### Uncertain write outcomes

A network timeout does not necessarily mean that the server rejected the operation.

When a write may have succeeded remotely:

1. Avoid blindly repeating a non-idempotent operation.
2. Query or reconcile the operation where possible.
3. Use operation identifiers or idempotency support where available.
4. Communicate uncertainty if the final result cannot be determined immediately.

---

## 21. Code Quality and Maintainability

Follow `coding-standards.md`.

### Required practices

- Use descriptive names.
- Keep functions focused.
- Keep responsibilities explicit.
- Prefer readable code over clever code.
- Avoid unexplained magic values.
- Use strong typing.
- Handle nullable values deliberately.
- Keep dependencies explicit.
- Follow established error-handling conventions.
- Remove obsolete code when its removal is within scope.
- Avoid duplicated business rules.
- Document non-obvious decisions.

### Avoid

- Giant widgets containing unrelated logic.
- Large methods with many responsibilities.
- Unnecessary inheritance.
- Excessive generic abstractions.
- Global mutable state without an approved reason.
- Broad exception swallowing.
- Scattered string literals for shared values.
- Unnecessary comments that merely restate code.
- Premature optimization without evidence.

### Comments

Comments should explain why a non-obvious decision exists, not merely repeat what the next line does.

Public APIs and complex domain rules should be documented according to project standards.

---

## 22. Dependency and Generated Code Management

### Dependencies

- Review the dependency's purpose before adding it.
- Check compatibility with supported Flutter and Dart versions.
- Follow package-locking conventions.
- Avoid unnecessary duplicate libraries.
- Do not change unrelated dependency versions.
- Verify platform support.

### Generated code

If the project uses generated models, serializers, dependency injection, or route code:

- Follow the approved generation commands.
- Do not hand-edit generated files when regeneration is required.
- Include required generated outputs according to repository policy.
- Verify that generated artifacts match their source definitions.
- Do not claim generation succeeded without running the relevant command.

---

## 23. Documentation Maintenance

Documentation must remain consistent with the implementation.

Update the relevant documents when a change affects:

- Product behavior.
- User flows.
- Architecture.
- Database schema.
- API contracts.
- Recurrence semantics.
- Notification behavior.
- Testing strategy.
- CI/CD.
- Setup instructions.
- Security practices.

Do not update unrelated documents unnecessarily.

### Documentation rules

- Distinguish approved decisions from proposals.
- Avoid unsupported claims.
- Use consistent terminology.
- Include examples only when they match the actual implementation.
- Keep commands accurate.
- Identify platform requirements.
- Document breaking changes.
- Avoid duplicating entire documents when a focused reference is sufficient.

---

## 24. Repository and Git Safety

Agents must preserve repository integrity.

### Required behavior

- Inspect the current Git status before significant changes.
- Do not overwrite unrelated user modifications.
- Keep changes focused.
- Review the diff before completion.
- Do not commit unless explicitly authorized or required by the approved workflow.
- Do not force-push or rewrite shared history without explicit approval.
- Do not delete branches or tags without authorization.
- Do not modify CI secrets or protected settings without authorization.
- Do not perform destructive database or infrastructure operations without approval.

### Existing modifications

If the repository already contains uncommitted changes:

- Identify which changes belong to the current task.
- Avoid reverting unrelated work.
- Do not use broad reset or cleanup commands that could destroy user work.
- Report conflicts that prevent safe continuation.

### Final diff review

Before reporting completion, inspect:

- Changed files.
- Unintended generated artifacts.
- Removed functionality.
- Added dependencies.
- Sensitive values.
- Debug code.
- TODOs introduced by the change.
- Formatting and test status.

---

## 25. UI Implementation Workflow

When asked to create or modify UI:

1. Read `design.md`.
2. Inspect existing shared components.
3. Identify the screen's purpose and user flow.
4. Reuse approved tokens and components.
5. Define loading, empty, error, success, and validation states.
6. Implement the smallest complete interaction.
7. Verify screen sizes and accessibility.
8. Run relevant widget and integration tests.
9. Review the final screen for design-rule violations.
10. Report actual validation results.

### UI prohibitions

Do not:

- Add prohibited gradients.
- Use pill-shaped buttons.
- Add fake ratings or user metrics.
- Use emoji icons.
- Add excessive scroll animations.
- Add a "Made with AI" tag.
- Use meaningless placeholder content.
- Leave controls without working behavior.
- claim that a backend operation succeeded when it was mocked.

### Demo and prototype behavior

If a task explicitly requests a demo or prototype:

- Clearly distinguish prototype data from production data.
- Avoid claiming that unavailable integrations work.
- Keep the demo aligned with the intended product design.
- Do not introduce prototype shortcuts into production code without approval.

---

## 26. Feature Implementation Workflow

For each feature:

### Requirements

- Identify the user flow.
- Confirm expected behavior.
- Identify required fields and constraints.
- Identify access-control requirements.
- Identify failure modes.

### Implementation

- Use the approved architecture.
- Reuse established components and services.
- Keep business rules testable.
- Preserve existing behavior.
- Avoid unapproved scope.

### Verification

- Test normal behavior.
- Test invalid input.
- Test relevant boundary cases.
- Test failure and retry behavior.
- Test permissions and authorization where relevant.
- Test platform-specific behavior where relevant.

### Completion

- Update documentation if needed.
- Review the final diff.
- Report test results.
- Identify unimplemented requirements and risks.

---

## 27. Bug-Fix Workflow

Bug fixes must address the underlying defect rather than hide its symptoms.

1. Reproduce the problem where possible.
2. Inspect relevant code and existing tests.
3. Identify the cause.
4. Add a regression test when practical.
5. Implement a focused correction.
6. Run the regression test.
7. Run relevant broader tests.
8. Review for unintended side effects.
9. Report the fix and verification.

Do not:

- Remove the failing assertion without justification.
- Hide an error state.
- Hardcode a successful result.
- Disable a security control.
- Rewrite unrelated modules.
- Claim a defect is fixed without supporting verification.

If the defect cannot be reproduced, report the limitation and explain the evidence supporting the proposed change.

---

## 28. Refactoring Rules

Refactoring must preserve externally observable behavior unless behavior change is explicitly requested.

### Before refactoring

- Identify current behavior.
- Identify relevant tests.
- Determine whether public contracts are affected.
- Identify migration or compatibility risks.

### During refactoring

- Keep changes focused.
- Avoid combining broad feature changes with structural cleanup.
- Preserve error handling.
- Preserve authorization.
- Preserve recurrence and time-zone semantics.
- Avoid unnecessary dependency changes.

### After refactoring

- Run relevant tests.
- Compare behavior before and after.
- Review the diff for accidental deletions.
- Update architecture documentation if the approved architecture changes.

A large diff is not automatically evidence of a better implementation.

---

## 29. Dependency Upgrades

Dependency upgrades require deliberate verification.

### Required process

1. Identify the reason for the upgrade.
2. Review compatibility and release notes.
3. Check platform support.
4. Review breaking changes.
5. Update constraints and lockfiles appropriately.
6. Run static analysis and tests.
7. Run relevant Android and iOS build checks.
8. Test affected integrations.
9. Document any migration or configuration changes.

Do not upgrade the entire dependency graph solely to resolve an unrelated minor issue unless required by the approved maintenance plan.

Security-related upgrades should be prioritized according to risk and the project's security policy.

---

## 30. CI/CD and Release Rules

Follow `CI_CD.md`.

Agents must not bypass required release checks to make a pipeline appear successful.

### Requirements

- Keep CI commands aligned with the repository.
- Preserve required test gates.
- Use the correct environment configuration.
- Keep secrets out of untrusted workflows.
- Validate relevant build targets.
- Respect signing and release permissions.
- Follow the approved migration and deployment process.
- Avoid unapproved production deployments.
- Preserve release artifacts and test evidence according to policy.

### Release claims

Do not claim that a release is ready merely because:

- The application compiles.
- Unit tests pass.
- A debug build succeeds.
- A mock integration succeeds.

Release readiness requires the applicable functional, security, platform, and operational criteria.

---

## 31. Accessibility and Localization

### Accessibility

Treat accessibility as a requirement for core flows.

Check semantic labels, focus order, text scaling, contrast, keyboard interaction, screen-reader behavior, and motion preferences where applicable.

### Localization

If localization is supported or introduced:

- Use the approved localization system.
- Avoid hardcoding user-facing strings.
- Respect locale-specific date and time formatting.
- Avoid assuming that text length remains constant across languages.
- Test layouts with longer translated strings.
- Keep date-only values distinct from timestamps.
- Follow the approved week-start and calendar conventions.

Do not introduce localization infrastructure without considering the project's current scope and architecture.

---

## 32. Performance and Resource Management

Agents must avoid introducing resource leaks or unnecessary workload.

### Flutter lifecycle

- Dispose controllers, subscriptions, and other owned resources correctly.
- Avoid updating disposed state objects.
- Avoid work that continues unnecessarily after a screen is removed.
- Prevent repeated initialization caused by lifecycle mistakes.

### Data handling

- Avoid loading unlimited records when a bounded query is appropriate.
- Avoid unnecessary serialization or deserialization.
- Avoid repeatedly computing expensive values during rendering.
- Follow pagination and caching rules where approved.

### Network behavior

- Avoid duplicate requests.
- Use the established timeout and retry strategy.
- Do not create aggressive polling loops.
- Handle app resume and connectivity changes without redundant synchronization.

Measure performance when a meaningful regression is suspected. Do not invent measurements.

---

## 33. AI Agent Communication Rules

Agents must communicate implementation status precisely.

### Before major work

For complex tasks, briefly identify:

- The intended change.
- Relevant architecture or files.
- Important risks.
- Any blocking ambiguity.

Do not provide lengthy progress narration when it adds no useful information.

### During work

If a critical ambiguity appears:

- Explain the decision that is blocked.
- Present the viable options.
- Ask for clarification if the decision materially affects architecture, data, security, or product behavior.

For small, reversible details, follow existing conventions and document meaningful assumptions.

### After work

Always report:

1. Summary of changes.
2. Important files or modules changed.
3. Tests added or updated.
4. Commands actually executed.
5. Actual pass/fail outcomes.
6. Checks not run and why.
7. Remaining risks or unresolved decisions.

Do not report a successful command that was not executed.

Do not claim a feature is complete when its required integration or testing remains unfinished.

---

## 34. Handling Uncertainty

When information is missing, distinguish among:

- Confirmed requirements.
- Existing implementation facts.
- Reasonable assumptions.
- Proposed options.
- Unresolved decisions.

Do not present assumptions as confirmed requirements.

### Ask for clarification when

- A database migration could cause data loss.
- Authentication or authorization behavior is ambiguous.
- A recurrence rule changes event meaning.
- A notification architecture decision is required.
- A breaking API change is proposed.
- A new dependency significantly changes architecture.
- Product scope would expand.
- A destructive operation could affect production.
- Conflicting documents cannot be reconciled safely.

### Proceed with a safe assumption when

- The detail is minor and reversible.
- Existing code establishes a clear convention.
- The assumption does not change product scope.
- The assumption does not weaken security.
- The assumption can be documented and tested.

---

## 35. Prohibited Agent Behaviors

The following are not permitted.

### Code and architecture

- Replacing the approved framework without permission.
- Introducing an unapproved architecture.
- Rewriting unrelated files.
- Adding unnecessary dependencies.
- Duplicating business rules inconsistently.
- Disabling lint rules to conceal problems.
- Removing tests to obtain a passing build.

### Product and UI

- Inventing features.
- Fabricating metrics, reviews, or testimonials.
- Adding prohibited visual patterns.
- Creating nonfunctional controls.
- Hardcoding success states.
- Misrepresenting mock data as real user data.
- Claiming unavailable features work.

### Security and data

- Hardcoding production secrets.
- Bypassing authorization.
- Weakening database policies.
- Running destructive production operations without approval.
- Exposing sensitive logs.
- Deleting user data outside the approved scope.

### Reporting

- Fabricating test results.
- Claiming commands were executed when they were not.
- Claiming integration tests passed based only on mocks.
- Hiding known failures.
- Presenting provisional architecture decisions as approved.
- Reporting incomplete work as finished.

---

## 36. Definition of Done

A task is complete only when all applicable conditions are satisfied.

### Requirements

- [ ] The requested behavior is understood.
- [ ] Relevant documentation has been reviewed.
- [ ] Acceptance criteria are satisfied.
- [ ] No unapproved scope was added.

### Implementation

- [ ] The approved architecture is followed.
- [ ] Existing functionality is preserved where required.
- [ ] Error and loading states are handled.
- [ ] Security and authorization are respected.
- [ ] UI follows `design.md`.
- [ ] No prohibited patterns were introduced.

### Verification

- [ ] Formatting has been checked.
- [ ] Static analysis has been run where available.
- [ ] Relevant tests have been executed.
- [ ] New behavior has appropriate test coverage.
- [ ] Regression risks have been reviewed.
- [ ] Unavailable checks are documented honestly.

### Documentation

- [ ] Relevant documentation has been updated.
- [ ] Assumptions are recorded where necessary.
- [ ] API or schema changes are documented.
- [ ] Unresolved decisions are identified.

### Final review

- [ ] The diff has been inspected.
- [ ] No unrelated changes were introduced.
- [ ] No secrets or sensitive data were added.
- [ ] Actual test results are reported.
- [ ] Remaining risks are disclosed.

Not every task requires every test layer. Inapplicable checks must be identified rather than silently marked as passed.

---

## 37. Standard Completion Report Template

Use the following structure when reporting completed engineering work.

### Summary

Describe the behavior implemented or defect corrected.

### Files changed

List the main files or modules changed and why.

### Testing

List the commands actually executed and their results.

Example format:

- `dart format --output=none --set-exit-if-changed .`: passed or failed.
- `flutter analyze`: passed or failed.
- `flutter test`: passed or failed.
- Backend or database tests: actual result, or not run with reason.
- Platform build checks: actual result, or not run with reason.

These are reporting examples, not prefilled results.

### Limitations

Identify:

- Tests that could not be run.
- Required infrastructure that was unavailable.
- Remaining unimplemented behavior.
- Known regressions or unresolved defects.

### Decisions required

List any product or architecture decisions that prevent safe completion.

---

## 38. Final Operating Rule

**Understand first. Inspect before changing. Implement only approved behavior. Protect data and security. Test the actual result. Report honestly.**

Every change must improve Schedular without introducing unnecessary complexity, violating the design system, weakening correctness, or creating false confidence.

When a requirement is unclear, resolve the ambiguity through the approved project process rather than inventing a product decision.

When a test fails, investigate the failure rather than hiding it.

When a feature is incomplete, report it as incomplete.

The objective is a reliable, maintainable Flutter application, not merely a codebase that compiles or a UI that looks finished.
