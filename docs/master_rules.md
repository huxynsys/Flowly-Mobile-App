# Schedular App: Master Rules

**File:** `master_rules.md`  
**Project:** Schedular  
**Document Version:** 1.0  
**Status:** Project-wide governing specification  
**Primary Client:** Flutter  
**Backend:** Must follow the approved TRD  
**Database:** Must follow the approved database schema  
**Audience:** Developers, AI coding agents, reviewers, testers, and project contributors

---

# 1. Purpose

This document defines the non-negotiable rules governing the development, design, architecture, security, testing, and maintenance of Schedular.

Every contributor must follow these rules before implementing or modifying any feature.

The objectives are to:

- Maintain a consistent product direction.
- Prevent unplanned features and architectural drift.
- Prevent insecure, fragile, or unnecessarily complex implementations.
- Ensure that the interface looks intentionally designed rather than automatically assembled.
- Keep the codebase maintainable and testable.
- Preserve consistency between requirements, user flows, design, database structure, and implementation.
- Ensure that AI-generated code follows the same standards as manually written code.

This document governs project-wide behavior. Detailed implementation instructions belong in their respective documentation files.

# 2. Rule Priority

When two requirements conflict, resolve them using the following order:

1. Applicable law, platform policies, and mandatory security requirements.
2. Explicit, approved project decisions documented in the TRD or an accepted architecture decision record.
3. `master_rules.md`.
4. `coding-standards.md`.
5. `database-schema.md` for persistent data and database behavior.
6. `flow.md` for application and business workflows.
7. `design.md` for visual and interaction requirements.
8. Existing implementation, provided it does not violate a higher-priority rule.
9. Contributor or AI coding-agent preferences.

This ordering does not permit a lower-level document to override a higher-level security requirement.

If two documents at the same priority conflict, do not guess. Identify the conflict, determine the intended behavior from the approved requirements, and update the affected documentation before making a consequential implementation decision.

# 3. Project Source of Truth

The project must maintain one authoritative source for each category of decisions.

| Document | Authority |
|---|---|
| `master_rules.md` | Global rules and project governance |
| `TRD.md` | Approved product requirements and scope |
| `flow.md` | User journeys, state transitions, and business workflows |
| `design.md` | Visual design, components, and interaction standards |
| `coding-standards.md` | Code organization, conventions, and implementation practices |
| `database-schema.md` | Tables, relationships, constraints, indexes, and data rules |
| `README.md` | Project setup, prerequisites, and developer instructions |
| Architecture decision records | Explicitly approved architecture decisions |
| Automated tests | Executable verification of documented behavior |

Use the actual filenames already present in the repository if capitalization or naming differs. Do not create duplicate documents merely to match an assumed filename.

## 3.1 Documentation consistency

Before implementing a significant feature:

1. Check the approved TRD.
2. Check the related application flow.
3. Check the design requirements.
4. Check the database and API implications.
5. Identify required changes to automated tests.
6. Update documentation when behavior or architecture changes.

A feature is not complete if its implementation contradicts the approved requirements.

# 4. Product Identity

Schedular is a scheduling-focused application intended to help users organize their time and manage schedule-related activities.

The precise feature set must come from the approved TRD.

The proposed core domain includes:

- User accounts and profiles.
- Tasks and scheduled events, if both are approved.
- Date and time management.
- Schedule browsing.
- Categories, if approved.
- Recurring items, if approved.
- Reminders and notifications, if approved.
- User preferences.
- Secure persistence and synchronization.

These examples establish direction, not permission to implement every possible scheduling feature.

## 4.1 Product decision rule

Every feature must have a clear user problem, a defined behavior, and an appropriate place in the product.

Do not add features solely because:

- A competing app has them.
- A template includes them.
- An AI coding agent can generate them.
- They make a dashboard appear more complex.
- They create an opportunity to add animations or visual effects.

# 5. Scope Control

The approved TRD defines the release scope.

Contributors must not independently introduce major features, dependencies, services, or architecture changes.

Examples requiring approval when not already specified include:

- Social feeds.
- Collaboration and shared calendars.
- AI assistants or AI-generated schedules.
- Subscription systems.
- Billing and payment processing.
- Calendar provider integrations.
- Complex analytics.
- Location tracking.
- Attachments and file storage.
- Time tracking.
- Multi-tenant organization management.
- Advanced offline synchronization.

A small implementation detail that does not change product behavior may be decided by the developer. A change that affects user expectations, security, data structures, costs, or architecture must be documented and approved.

# 6. Non-Negotiable Design Rules

The visual interface must follow `design.md`. The following rules apply across the entire application.

## 6.1 Prohibited visual patterns

Do not use:

- Purple gradients.
- Blue gradients.
- Purple-to-blue gradient backgrounds.
- Pill-shaped buttons.
- Fake user reviews.
- Fake ratings.
- Fake usage metrics.
- Fake productivity statistics.
- Fake activity counts.
- Fabricated testimonials.
- Fabricated social proof.
- Vague hero headings.
- Generic marketing claims without a clear meaning.
- Emoji used as interface icons.
- Excessive scroll-triggered animations.
- Unnecessary parallax effects.
- Overly animated cards.
- Decorative effects that interfere with readability.
- A “Made with AI” label or badge in the product UI.
- Generic template sections that have no product purpose.
- Unnecessary glassmorphism or decorative gradients.
- Randomly generated visual styles between screens.
- Placeholder content presented as real user data.

Do not work around these rules by using slightly different shades, labels, or effects that produce the same unwanted result.

## 6.2 Buttons

Buttons must have a deliberate, consistent shape and visual hierarchy.

- Do not use pill-shaped buttons.
- Use the shape defined in `design.md`.
- Primary actions must be visually distinguishable from secondary actions.
- Destructive actions must be clearly identifiable.
- Disabled states must be visually apparent.
- Buttons must have meaningful labels.
- Button dimensions must be usable on mobile screens.
- A button must not imply an action that it does not perform.
- Do not use excessive shadows or gradients to create artificial emphasis.

## 6.3 Typography

- Use a defined typography system.
- Establish clear distinctions between page titles, section headings, body text, labels, and supporting information.
- Avoid inconsistent font sizes across equivalent components.
- Maintain readable line lengths and spacing.
- Do not use excessively large text solely to fill empty space.
- Avoid unnecessary uppercase text.
- Ensure contrast and legibility across supported themes.

## 6.4 Colors

- Use centralized design tokens.
- Follow the palette in `design.md`.
- Do not introduce new colors for individual screens without a defined purpose.
- Do not use color alone to communicate errors, status, or priority.
- Maintain accessible text contrast.
- Do not use gradients as a substitute for a coherent layout.

## 6.5 Icons

- Use a consistent icon family.
- Do not use emoji as functional icons.
- Use icons only when they improve recognition or reduce ambiguity.
- Use consistent stroke weight and visual scale.
- Prefer recognizable icons with labels where meaning could be unclear.
- Do not mix unrelated icon styles.

## 6.6 Animation

Animation must communicate a state change or improve spatial understanding.

- Avoid excessive scroll-triggered animations.
- Avoid animation that delays ordinary actions.
- Avoid distracting looping effects.
- Avoid unnecessary animated backgrounds.
- Respect reduced-motion preferences where supported.
- Keep navigation transitions purposeful and restrained.
- Do not add animation solely to make the product appear more sophisticated.

## 6.7 Honest interface content

All displayed data must be real, clearly identified sample data, or explicitly marked placeholder content.

Never fabricate:

- Reviews.
- Ratings.
- Number of users.
- Task completion statistics.
- Productivity improvements.
- Customer logos.
- Testimonials.
- Notifications or activity history.
- Integrations that do not exist.
- Success states for operations that have failed.

Development fixtures are allowed when clearly separated from production data and used only in appropriate development or testing contexts.

# 7. User Experience Rules

Every feature must be understandable without requiring the user to infer what a control does.

## 7.1 Interaction requirements

- Every interactive control must perform its stated action.
- Every important action must provide feedback.
- Loading states must be represented appropriately.
- Empty states must explain the situation and provide a useful next step when applicable.
- Error states must explain what failed and how to recover.
- Destructive actions must have appropriate safeguards.
- Forms must validate user input.
- Failed submissions must preserve input whenever safe.
- Repeated taps must not create duplicate operations.
- Navigation must behave consistently across screens.
- Back navigation must not expose protected information after logout.

## 7.2 Empty states

Empty states must describe the actual data condition.

For example, an empty schedule should explain that no items are scheduled for the selected period and offer a relevant creation action when permitted.

Do not fill empty screens with fabricated tasks, fake statistics, or decorative marketing content.

## 7.3 Feedback

Use feedback appropriate to the action.

- Small reversible actions may use concise confirmation.
- Form submissions may use a clear saving state.
- Failed operations must display recoverable errors.
- Destructive actions must communicate their consequences.
- Long-running operations must communicate their actual status.

Never show success feedback before the relevant operation has been confirmed.

# 8. Flutter Technology Rules

Flutter is the selected mobile application framework for the current project direction.

Do not replace Flutter with React Native, native Android, native iOS, or another framework without an explicit approved architecture decision.

## 8.1 Flutter implementation

- Follow the project's approved Flutter and Dart versions.
- Use null safety.
- Keep widgets focused on presentation and interaction.
- Separate presentation, application logic, and data access according to `coding-standards.md`.
- Use the approved state management solution consistently.
- Use the approved routing solution consistently.
- Centralize configuration.
- Avoid oversized widgets and monolithic files.
- Keep platform-specific behavior isolated where practical.
- Use asynchronous APIs safely.
- Handle widget and application lifecycle changes correctly.
- Dispose of resources when required.
- Avoid blocking the main isolate with expensive operations.

## 8.2 Dependency policy

Before adding a package:

1. Confirm that it solves a real requirement.
2. Check whether an existing dependency already provides the functionality.
3. Evaluate maintenance status and compatibility.
4. Review permissions and security implications.
5. Consider application size and runtime performance.
6. Check the license where relevant.
7. Add it only when its benefits justify the dependency.

Do not add packages merely for convenience if a small, clear implementation already exists in the project.

Do not upgrade major dependencies without evaluating breaking changes and testing the affected features.

## 8.3 Architecture consistency

Follow the architecture approved for the repository.

Do not introduce competing patterns for routing, state management, networking, or dependency injection without a documented reason.

Avoid unnecessary abstraction. An abstraction must improve testability, maintainability, reuse, or separation of responsibilities.

# 9. Backend and API Rules

The final backend platform must be confirmed by the approved TRD and architecture decisions.

If the project uses Supabase, follow its authentication, database, security, and migration practices. If it uses a different backend, follow that platform's equivalent controls.

## 9.1 Backend responsibilities

The trusted backend must enforce:

- Authentication.
- Authorization.
- Data validation.
- Ownership checks.
- Business rules.
- Transactional consistency.
- Rate limits where needed.
- Error response conventions.
- Secure handling of sensitive operations.
- Trusted background jobs.

The Flutter client must not be the only enforcement point for any security-sensitive rule.

## 9.2 API behavior

- Use consistent request and response models.
- Validate input at the server boundary.
- Return errors that the client can classify.
- Do not expose stack traces or internal database details.
- Apply pagination to potentially large result sets.
- Avoid unbounded queries.
- Handle timeouts and retry behavior explicitly.
- Use idempotency controls where repeated writes could create duplicates.
- Keep business logic out of presentation widgets.

## 9.3 Secrets

Never place privileged secrets in:

- Flutter source code.
- Public configuration.
- Mobile assets.
- Version-controlled environment files.
- Client-side constants.
- Logs.
- Error messages.
- Documentation examples that resemble real credentials.

Public client configuration may be included only when the service explicitly supports public client credentials and all required server-side security policies are correctly configured.

# 10. Database Rules

`database-schema.md` governs database structure.

- Follow the approved schema.
- Use migrations for structural changes.
- Define primary keys, foreign keys, and constraints intentionally.
- Add indexes based on actual query patterns.
- Use transactions for related changes that must remain consistent.
- Enforce ownership and access rules.
- Use explicit deletion and retention policies.
- Avoid storing duplicated information without a documented reason.
- Do not perform ad hoc production schema changes outside the migration process.
- Do not expose internal database implementation details to the UI.

## 10.1 Schema change procedure

Before changing the database:

1. Identify the requirement.
2. Update `database-schema.md`.
3. Write a migration.
4. Consider existing records and migration safety.
5. Update backend models and validation.
6. Update client models and repository logic.
7. Add or update tests.
8. Verify authorization policies.
9. Confirm rollback or recovery expectations where applicable.

A migration must not silently destroy existing user data.

## 10.2 Row-level security

If Supabase or another row-level security mechanism is used:

- Enable appropriate policies for every exposed table.
- Verify read, insert, update, and delete permissions separately.
- Do not rely on client-side filtering for authorization.
- Test attempts to access another user's records.
- Review foreign-key relationships for ownership leaks.
- Keep privileged service access on trusted infrastructure.
- Ensure background jobs use a deliberate, restricted authorization model.

# 11. Authentication and Authorization

- Use the approved authentication provider.
- Never implement custom password storage without explicit approval and a professionally reviewed security design.
- Never log credentials or session tokens.
- Validate sessions before protected operations.
- Enforce authorization on every protected backend operation.
- Handle session expiration explicitly.
- Clear sensitive client state on logout.
- Do not assume that a valid user can access every record.
- Require appropriate verification for sensitive account operations.
- Follow the approved account deletion and retention policy.

A hidden route, disabled button, or client-side ownership check is not sufficient security.

# 12. Scheduling and Time Rules

Scheduling and time handling are core domain concerns. Implement them consistently.

## 12.1 Date and time storage

- Follow the approved database schema for temporal data.
- Use timezone-aware timestamps for absolute instants.
- Store an IANA time zone identifier when local-time interpretation matters.
- Do not treat a UTC offset as a permanent time zone.
- Distinguish all-day dates from timed events.
- Define the meaning of task due times and event start/end times.
- Validate end times against start times.
- Use consistent date range boundaries in queries.

## 12.2 Time zone behavior

The application must distinguish between:

- An event fixed to a particular instant.
- A schedule intended to remain at a local wall-clock time.
- An all-day item associated with a local calendar date.
- A recurring schedule evaluated in a designated time zone.

Do not silently reinterpret existing schedule items when a user changes their preferred time zone.

## 12.3 Recurrence

If recurring items are approved:

- Use one documented recurrence model.
- Define occurrence identity.
- Define how exceptions are stored.
- Define completion behavior for individual occurrences.
- Define series-editing scope.
- Define how reminder jobs are reconciled.
- Test month boundaries, leap years, and relevant daylight saving transitions.
- Avoid uncontrolled generation of an unlimited number of future records.

Do not implement recurrence as a simple repeated date increment without handling the approved recurrence semantics.

# 13. Reminder and Notification Rules

If reminders and notifications are part of the approved scope:

- Separate reminder configuration from delivery state.
- Use trusted background processing for remote delivery.
- Do not rely on the mobile app remaining open.
- Validate the parent item and occurrence before delivery.
- Cancel or invalidate stale jobs when items change or are deleted.
- Use bounded retries.
- Prevent duplicate processing where practical.
- Record provider outcomes accurately.
- Handle invalid device tokens.
- Respect user preferences and operating system permissions.
- Minimize sensitive information in notification payloads.

Do not describe a push notification as viewed by the user merely because a provider accepted the request.

# 14. Error Handling Rules

Every feature must define its expected failure behavior.

At minimum, account for:

- Invalid input.
- Unauthenticated requests.
- Unauthorized access.
- Missing records.
- Network failures.
- Timeouts.
- Server errors.
- Conflicting updates.
- Rate limiting.
- Permission denial.
- Notification delivery failures.
- Unsupported application states.

## 14.1 Error presentation

- Show useful, user-facing messages.
- Keep technical diagnostics out of the user interface.
- Preserve form data when safe.
- Provide a recovery action where possible.
- Avoid repeated error dialogs for the same failure.
- Never suppress a failure and pretend the operation succeeded.
- Never retry a write blindly when the original result is uncertain.

# 15. Data Integrity and Privacy

- Collect only data required by approved features.
- Store only what the product needs.
- Define retention and deletion behavior.
- Avoid exposing private schedule information unnecessarily.
- Protect locally cached user data.
- Enforce server-side ownership.
- Use secure transport for network communication.
- Avoid logging personal schedule content unless there is a justified, controlled need.
- Do not send private data to external services without an approved purpose and appropriate safeguards.
- Follow applicable privacy and data protection requirements.

Do not introduce analytics, crash reporting, advertising SDKs, or other external data collection without evaluating and approving their privacy implications.

# 16. Testing Rules

Testing is part of implementation, not an optional final step.

## 16.1 Required test categories

Use the categories appropriate to the feature:

- Unit tests.
- Widget tests.
- Integration tests.
- Backend tests.
- Database constraint and migration tests.
- Authorization and security tests.
- Recurrence and time zone tests.
- Notification worker tests.
- Regression tests.

## 16.2 Minimum expectations

- New business logic must have appropriate tests.
- Bug fixes should include a regression test when practical.
- Critical flows must be tested end to end.
- Authorization boundaries must be tested.
- Time-dependent logic must include boundary cases.
- Destructive operations must be tested.
- Tests must not rely on production user data.
- Test fixtures must not be presented as real production metrics.

## 16.3 Definition of tested

Do not claim that a test, build, or static analysis check passed unless it was actually executed and its result was observed.

If a check cannot be run, state that it remains unverified.

# 17. Code Quality Rules

Follow `coding-standards.md` for detailed language and repository conventions.

At project level:

- Write readable, maintainable code.
- Prefer clear names over cryptic abbreviations.
- Keep responsibilities focused.
- Avoid duplicated business logic.
- Avoid giant widgets, classes, and functions.
- Avoid unnecessary abstraction and premature optimization.
- Remove unused imports and dead code.
- Avoid suppressing compiler or analyzer warnings without a justified reason.
- Handle asynchronous errors deliberately.
- Keep business logic testable.
- Do not commit debug code or temporary workarounds.
- Do not use placeholder implementations for production behavior.
- Do not silently swallow exceptions.

## 17.1 Type safety

- Use Dart null safety correctly.
- Avoid unnecessary `dynamic`.
- Validate data at external boundaries.
- Avoid unsafe casts when validation or typed parsing is possible.
- Use explicit domain models for important data.
- Represent loading, success, and error states clearly.

## 17.2 Maintainability

Code must be understandable to another contributor who has not participated in its original implementation.

Do not compress code merely to reduce line count. Do not introduce complex abstractions for hypothetical future requirements.

# 18. AI Coding Agent Rules

These rules apply to ChatGPT, coding agents, automated code generation tools, and contributors using AI-assisted development.

## 18.1 Before implementation

The agent must:

1. Inspect the relevant project files.
2. Read the applicable governing documentation.
3. Identify the actual framework, package versions, and repository structure.
4. Understand existing patterns before introducing new ones.
5. Identify the requested scope.
6. Determine which files and tests are affected.
7. Ask for clarification if a consequential requirement is ambiguous.
8. Avoid assuming that a proposed feature is already approved.

Do not claim to have inspected files or repository history that were not actually accessible.

## 18.2 During implementation

The agent must:

- Follow approved project architecture.
- Reuse existing components and utilities where appropriate.
- Preserve unrelated functionality.
- Follow `design.md` for all UI work.
- Follow `database-schema.md` for persistence changes.
- Follow `flow.md` for user journeys.
- Follow `coding-standards.md` for implementation.
- Avoid adding unapproved features.
- Avoid fabricating data or integrations.
- Avoid adding dependencies without justification.
- Keep changes focused on the requested task.
- Update relevant documentation when requirements or architecture change.

## 18.3 After implementation

The agent must:

1. Review the modified files.
2. Run relevant tests and static checks when tools are available.
3. Inspect errors and fix issues introduced by the change.
4. Verify that the requested behavior is implemented.
5. Report which checks were actually run.
6. Identify any unresolved limitations.
7. Avoid claiming full completion when important work remains.

## 18.4 Prohibited AI behavior

An AI coding agent must not:

- Invent project requirements.
- Invent backend endpoints.
- Invent environment variables without explaining their purpose.
- Invent database columns that contradict the schema.
- Replace existing architecture without approval.
- Generate fake production reviews or metrics.
- Leave critical paths as unexplained placeholders.
- hardcode secrets.
- Disable security controls to make a demo work.
- claim untested code is verified.
- rewrite unrelated modules without justification.
- introduce visual styles prohibited by this document.
- create a second implementation of an existing feature without investigating the existing one.

## 18.5 Ambiguity policy

When a missing decision would materially affect architecture, security, data integrity, cost, or user behavior, ask a focused clarification question.

For low-risk implementation details, choose the simplest approach consistent with the existing project standards and document the assumption when relevant.

# 19. File and Repository Rules

- Respect the existing repository structure.
- Do not create duplicate files for the same responsibility.
- Use the naming conventions in `coding-standards.md`.
- Keep documentation in the agreed documentation directory.
- Keep environment-specific values out of source code.
- Keep generated files out of version control when they are reproducible and intended to remain untracked.
- Do not delete existing files without confirming their purpose.
- Do not overwrite user changes without understanding their impact.
- Keep migrations versioned.
- Keep sample configuration files free of real secrets.
- Update setup instructions when development requirements change.

The exact directory structure is governed by the repository and approved architecture, not by assumptions in this document.

# 20. Git and Change Management

- Use meaningful commit messages.
- Keep changes focused.
- Do not commit credentials, private keys, tokens, or personal data.
- Review diffs before committing.
- Do not rewrite shared history without authorization.
- Do not force-push to shared branches without approval.
- Do not merge unverified changes into protected branches.
- Document breaking changes.
- Include migration instructions when needed.
- Keep generated artifacts out of commits unless explicitly required.

Follow the project's actual branch and release policy if one exists.

# 21. Performance and Resource Management

- Measure before optimizing.
- Avoid unnecessary backend requests.
- Avoid unbounded database queries.
- Use pagination for large collections.
- Use indexes based on real access patterns.
- Avoid expensive work on the Flutter main isolate.
- Avoid unnecessary rebuilds.
- Dispose of controllers and other resources when required.
- Avoid loading full histories when only a visible date range is needed.
- Keep recurring occurrence generation bounded.
- Avoid unnecessary background polling.
- Do not introduce complex caching without a defined consistency policy.

Do not invent numeric performance guarantees. Define measurable targets in the approved non-functional requirements.

# 22. Accessibility and Platform Compatibility

- Follow Flutter and platform accessibility practices.
- Provide meaningful accessible labels.
- Maintain readable contrast.
- Do not use color as the sole status indicator.
- Support text scaling where practical.
- Use appropriate touch target sizes.
- Ensure error messages are accessible.
- Support Android and iOS behavior required by the approved TRD.
- Test platform-specific permissions and notification behavior.
- Avoid hardcoding dimensions that break common screen sizes.
- Respect reduced-motion preferences where applicable.

Do not claim support for a platform or device configuration that has not been tested.

# 23. Environment and Configuration Rules

Separate development, testing, staging, and production configuration where applicable.

- Never commit production secrets.
- Document required environment variables.
- Validate configuration at startup where practical.
- Fail clearly when essential configuration is missing.
- Avoid silently substituting production endpoints in development.
- Ensure test environments do not mutate production data.
- Restrict production access to authorized contributors.
- Keep configuration examples clearly marked as examples.

Do not place backend service-role credentials in Flutter assets or compiled client code.

# 24. Release Rules

A release must not proceed solely because the application launches successfully.

Before release, verify:

- [ ] Approved features match the TRD.
- [ ] Critical user flows work.
- [ ] Authentication and session handling are correct.
- [ ] Authorization and data isolation are tested.
- [ ] Database migrations have been reviewed.
- [ ] No privileged secrets are exposed.
- [ ] Relevant automated tests pass.
- [ ] Critical errors are resolved.
- [ ] Loading, empty, and failure states are implemented.
- [ ] Scheduling and time zone behavior has been tested.
- [ ] Reminder behavior has been tested if applicable.
- [ ] UI follows `design.md`.
- [ ] No prohibited fake data or design patterns are present.
- [ ] Required platform permissions are justified.
- [ ] Privacy and deletion policies are respected.
- [ ] Build and release configuration has been reviewed.
- [ ] Known limitations are documented.

A release blocker must be resolved or explicitly accepted by the authorized project owner.

# 25. Definition of Done

A feature is complete only when all applicable criteria are met.

## Requirements

- The feature is approved by the TRD.
- Its expected behavior is defined.
- Its user journey is consistent with `flow.md`.

## Design

- Its interface follows `design.md`.
- It contains no prohibited visual patterns.
- Loading, empty, success, and failure states are handled.
- Accessibility has been considered.

## Implementation

- Code follows `coding-standards.md`.
- Architecture remains consistent.
- Backend validation and authorization are correct.
- Database changes follow `database-schema.md`.
- Errors and retries are handled deliberately.
- No secrets or fake production data have been introduced.

## Verification

- Relevant tests have been added or updated.
- Appropriate checks have been executed.
- Regressions have been investigated.
- Any unverified behavior is explicitly reported.
- Relevant documentation has been updated.

A feature must not be marked complete if critical security, data integrity, or acceptance requirements remain unverified.

# 26. Change Approval

Changes to the following require explicit review:

- Primary application framework.
- Backend platform.
- Authentication provider.
- Database architecture.
- State management or routing conventions.
- Core domain model.
- Recurrence architecture.
- Notification worker architecture.
- Security model.
- Data retention policy.
- Approved feature scope.
- Major visual design direction.
- Significant new third-party dependencies.

Document the reason, alternatives considered, consequences, and migration requirements when applicable.

# 27. Project Decision Log

Maintain an architecture decision record for major choices.

Each record should include:

- Decision ID.
- Date.
- Status.
- Context.
- Decision.
- Alternatives considered.
- Consequences.
- Affected documents.
- Migration or implementation notes.

Do not silently change a previously approved architecture decision.

# 28. Final Governing Principle

Schedular must be developed as a coherent, secure, maintainable product rather than a collection of disconnected screens or generated code.

Every contribution must:

1. Solve an approved user problem.
2. Respect the documented architecture.
3. Preserve data integrity and user privacy.
4. Follow the established design system.
5. Handle failures honestly.
6. Include appropriate verification.
7. Avoid unnecessary complexity.
8. Keep all project documents consistent.

When uncertain, inspect the existing project, follow the highest-priority approved requirement, and clarify consequential ambiguity before implementation.

**End of `master_rules.md`.**