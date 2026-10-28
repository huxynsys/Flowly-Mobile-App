# Flowlly — Error Handling Standards

**File:** `ERROR_HANDLING.md`  
**Project:** Flowlly  
**Primary technology:** Flutter and Dart  
**Status:** Engineering standard; service-specific rules must follow `decisions.md`.

---

## 1. Purpose

This document defines how the Flowlly application must detect, classify, handle, communicate, log, test, and recover from errors.

The objectives are to:

- Prevent avoidable application crashes.
- Provide clear and actionable feedback to users.
- Preserve data integrity.
- Handle network failures and unreliable connectivity.
- Protect sensitive information.
- Make errors diagnosable without exposing internal details.
- Ensure consistent error handling across application layers.
- Prevent silent failures and false success messages.
- Support reliable scheduling-related workflows, subject to the features confirmed in the TRD.

These standards apply to Flutter/Dart UI and application code, Supabase repositories and Edge Functions, persistence, authentication, notifications, and background processing.

## 2. Core Principles

Every implementation must follow these principles.

### 2.1 Never fail silently

Every error must be handled, propagated to an appropriate handler, or recorded as an intentionally ignored and understood condition.

Empty `catch` blocks are prohibited unless a documented, narrowly scoped reason makes ignoring that specific exception safe.

### 2.2 Never report false success

The application must not display a success message, mark an operation as completed, or assume that data was saved unless the operation's outcome has been confirmed.

If the outcome is unknown, communicate that uncertainty.

### 2.3 Separate user messages from technical diagnostics

Users should see understandable messages. Developers should receive structured diagnostic information appropriate to the environment.

Never expose stack traces, SQL statements, server internals, API credentials, tokens, or sensitive payloads in the UI.

### 2.4 Handle errors at the correct layer

- **Presentation layer:** Converts application failures into user-facing states and actions.
- **Application/domain layer:** Applies business rules and represents expected failures.
- **Data layer:** Handles API, database, parsing, and persistence failures.
- **Infrastructure layer:** Handles platform, networking, authentication-provider, and external-service failures.

Each layer must preserve useful error context without unnecessarily duplicating logging or converting errors into misleading success values.

### 2.5 Preserve data integrity

Do not discard user input, overwrite valid data, or mark a partially completed operation as successful merely to recover from an error.

Use transactions, idempotency, validation, and reconciliation where appropriate.

### 2.6 Recover whenever safe

When recovery is possible, offer an appropriate action such as retrying, refreshing, editing input, reconnecting, or returning to a safe screen.

Do not automatically retry every operation.

---

## 3. Error Classification

All application errors should fit one or more of the following categories.

| Category | Examples | Expected handling |
|---|---|---|
| Validation | Missing field, invalid date range | Show field-specific feedback |
| Business rule | Conflicting or disallowed operation | Explain the rule and preserve input |
| Network | Offline device, timeout, DNS failure | Show connectivity state and safe retry |
| Authentication | Expired session, invalid credentials | Prompt for appropriate sign-in or recovery |
| Authorization | User lacks permission | Deny the operation and explain access limitations |
| Not found | Requested record no longer exists | Show a recoverable unavailable state |
| Conflict | Concurrent update or duplicate operation | Reconcile state and request user input if needed |
| Rate limiting | Too many requests | Respect retry guidance and avoid request loops |
| Server | Service unavailable or unexpected server error | Show a generic failure and retry when safe |
| Persistence | Database read/write failure | Preserve recoverable input and report the failure |
| Parsing | Invalid or unexpected response format | Reject invalid data and record safe diagnostics |
| Platform | Permission denied or unavailable OS capability | Explain the limitation and offer settings guidance when appropriate |
| Background task | Deferred or interrupted work | Record status and retry under safe conditions |
| Unexpected | Unclassified exception or invariant violation | Capture diagnostics and show a generic error |

Error categories are logical classifications. The implementation may use different class names, provided behavior remains consistent.

---

## 4. Error Model

### 4.1 Use typed application failures

Expected failures should be represented with explicit types instead of relying exclusively on strings or generic exceptions.

A conceptual Dart model is:

```dart
enum AppErrorCode {
  validation,
  unauthenticated,
  forbidden,
  notFound,
  conflict,
  networkUnavailable,
  timeout,
  rateLimited,
  serverFailure,
  persistenceFailure,
  parsingFailure,
  permissionDenied,
  cancelled,
  unknown,
}

class AppError {
  const AppError({
    required this.code,
    required this.message,
    this.cause,
    this.correlationId,
    this.retryable = false,
  });

  final AppErrorCode code;
  final String message;
  final Object? cause;
  final String? correlationId;
  final bool retryable;
}
```

This is an example contract, not a requirement to use this exact class. If the project adopts a sealed class hierarchy or a result type, follow that established design.

### 4.2 Keep technical and presentation messages separate

The error model should not be responsible for exposing raw internal exceptions to users.

Use separate mechanisms for:

- A stable error code.
- A safe diagnostic cause.
- A user-facing localized message.
- Optional retry guidance.
- A correlation identifier when available.

Do not put secrets or sensitive request payloads in an error object that may be serialized or logged.

### 4.3 Exceptions versus expected failures

Use exceptions for exceptional control flow and failures that naturally arise from an operation.

Use explicit result types for expected outcomes when they improve clarity, such as validation failures, conflicts, or a normal not-found result.

Do not use exceptions for ordinary branching when a direct return value is clearer.

---

## 5. Global Error Handling

Global handlers are a final safety net, not a replacement for local error handling.

### 5.1 Flutter framework errors

Configure `FlutterError.onError` to capture framework-reported errors and forward them to an approved diagnostic service in production.

Example:

```dart
import 'package:flutter/foundation.dart';
import 'package:flutter/material.dart';

void configureFlutterErrorHandling() {
  FlutterError.onError = (FlutterErrorDetails details) {
    FlutterError.presentError(details);

    if (kReleaseMode) {
      // Send sanitized diagnostics to the approved
      // crash-reporting service, if configured.
    }
  };
}
```

The production reporting integration must be implemented and tested before relying on this handler.

Do not expose technical diagnostics in production UI merely because they are available in `FlutterErrorDetails`.

### 5.2 Uncaught asynchronous and root-isolate errors

Use `PlatformDispatcher.instance.onError` where appropriate to capture uncaught root-isolate errors.

```dart
import 'dart:ui';

void configurePlatformErrorHandling() {
  PlatformDispatcher.instance.onError = (error, stackTrace) {
    // Record sanitized diagnostics using the approved logger.
    // Return true only when the error has been handled
    // according to the application's reporting policy.
    return true;
  };
}
```

The handler must be wired into application startup. Its return value must reflect the actual reporting and handling policy; it must not be used to hide serious failures.

Errors in additional isolates require their own explicit error-reporting strategy.

### 5.3 Startup configuration

Initialize required dependencies and error reporting before the application starts.

```dart
import 'package:flutter/widgets.dart';

Future<void> main() async {
  WidgetsFlutterBinding.ensureInitialized();

  // Initialize required configuration and approved
  // error-reporting services here.

  configureFlutterErrorHandling();
  configurePlatformErrorHandling();

  runApp(const SchedularApp());
}
```

The application must define which startup failures are fatal and which permit a degraded mode.

If required configuration is missing, show a safe startup failure rather than pretending the app initialized successfully.

### 5.4 Avoid duplicate reporting

The same exception may reach several layers. Define a reporting policy so that one underlying failure does not generate multiple identical crash events.

A lower layer may add diagnostic context while an owning boundary decides whether the failure should be reported.

---

## 6. Presentation and UI Error Handling

### 6.1 Required UI states

Every asynchronous screen or feature must explicitly account for the states relevant to its operation:

- Initial.
- Loading.
- Success.
- Empty.
- Recoverable failure.
- Authentication required.
- Permission denied.
- Offline or degraded connectivity, when relevant.
- Submitting or saving.
- Outcome unknown, when a request may have completed remotely despite a lost response.

Do not assume that every screen needs every state. Choose states based on actual behavior.

### 6.2 User-facing messages

Messages must be:

- Clear and concise.
- Free from internal implementation details.
- Actionable where recovery is possible.
- Consistent with the app's localization conventions.
- Specific enough to distinguish invalid input from a failed service operation.

Examples:

| Technical condition | User-facing message |
|---|---|
| Required field missing | "Please complete this field." |
| Invalid date range | "Choose a valid start and end time." |
| Device offline | "You're offline. Check your connection and try again." |
| Request timed out | "This is taking longer than expected. Please try again." |
| Session expired | "Your session has expired. Please sign in again." |
| Permission denied | "You don't have permission to perform this action." |
| Record unavailable | "This item is no longer available." |
| Service failure | "We couldn't complete your request. Please try again." |
| Save outcome unknown | "We couldn't confirm whether your changes were saved. Refresh before trying again." |

These are example messages. Adjust them to the confirmed product terminology and UX design.

### 6.3 Error display conventions

- Use inline messages for field validation.
- Use a persistent page-level error state for a failed page load.
- Use a snackbar or equivalent transient feedback for a recoverable action when appropriate.
- Use a dialog only when user attention or a decision is required.
- Do not rely exclusively on transient messages for important failures.
- Do not show raw exception text in snackbars, dialogs, or form fields.
- Preserve user-entered data when possible.
- Prevent duplicate submissions while an operation is in progress.
- Do not disable unrelated UI actions unnecessarily.

### 6.4 Avoid infinite loading

Every network or persistence operation must have a defined completion, timeout, cancellation, or recovery strategy.

If an operation is interrupted, do not leave the UI permanently loading. Return to a safe state and provide an appropriate message.

---

## 7. Input Validation and Business Rules

### 7.1 Validate at the correct boundaries

Validation must occur before invalid input reaches critical business operations.

Use:

1. UI validation for immediate user feedback.
2. Domain validation for business invariants.
3. Backend validation for all trusted operations involving remote data.

Client-side validation never replaces backend validation.

### 7.2 Validation behavior

- Identify the affected field when possible.
- Explain how to correct the input.
- Preserve valid fields and user-entered content.
- Avoid repeating identical validation messages for the same error.
- Revalidate after relevant changes.
- Validate imported, restored, and API-provided data as well as manually entered data.

### 7.3 Scheduling-related rules

If the approved TRD includes scheduling features, define and test the applicable rules for:

- Required dates and times.
- Start and end ordering.
- Time zones and daylight-saving transitions.
- Past-date restrictions, if required.
- Conflicting or overlapping events, if prohibited.
- Recurring schedules, if supported.
- Duplicate submissions.
- Concurrent changes by multiple clients.
- Invalid or deleted references.

Do not assume all of these rules apply to Schedular. Confirm the actual requirements before enforcing them.

### 7.4 Server-side rejection

If the server rejects input that passed client validation:

- Display a safe explanation.
- Preserve user input where possible.
- Update client validation only if the rejection reveals a confirmed rule.
- Do not repeatedly submit the same invalid request.
- Record diagnostic context without logging sensitive values.

---

## 8. Network and API Errors

### 8.1 Handle common failure conditions

API and network code must account for:

- No connectivity.
- DNS or connection failures.
- Connection reset.
- Request timeout.
- TLS or certificate errors.
- Unexpected HTTP status codes.
- Malformed response bodies.
- Unexpected content types.
- Rate limiting.
- Server overload.
- Authentication expiration.
- Authorization failure.
- Partial or ambiguous operation outcomes.

### 8.2 HTTP status handling

Interpret status codes according to the actual API contract.

Typical behavior:

- `400`: Invalid request; map to a safe validation or request error.
- `401`: Authentication is missing or invalid; follow the session recovery flow.
- `403`: The request is not permitted.
- `404`: The requested resource is unavailable.
- `409`: A conflict exists; reconcile or request user input.
- `422`: Semantic validation failed, if supported by the API.
- `429`: Rate limited; respect `Retry-After` when provided.
- `500`: Internal server error.
- `502`, `503`, `504`: Gateway or service availability failure.

Do not assume every backend uses these codes identically. Follow its documented contract.

### 8.3 Timeouts

Set appropriate connection and response timeouts based on the operation and client library.

When a timeout occurs:

- Cancel or release resources when supported.
- Stop unnecessary duplicate requests.
- Preserve local state.
- Determine whether retrying is safe.
- Do not assume the server did not process the request.

A timeout means the client did not receive a confirmed outcome within the allowed period. It does not necessarily mean the operation failed remotely.

### 8.4 Retry policy

Automatic retries must be limited, observable, and operation-aware.

- Use bounded exponential backoff with jitter for eligible transient failures.
- Respect server-provided retry guidance.
- Apply retry limits and an overall deadline.
- Avoid retrying invalid requests, permission failures, or expired authentication without an appropriate recovery action.
- Do not retry non-idempotent operations blindly.
- Use idempotency keys or equivalent backend safeguards for operations where duplicate execution would be harmful.
- Avoid retrying in both the repository and HTTP client independently without a coordinated policy.

Example backoff concept:

```text
delay = min(maxDelay, baseDelay * 2^attempt) + randomJitter
```

This formula is illustrative. Select actual limits based on the API and product requirements.

### 8.5 Offline behavior

If offline access is supported:

- Clearly distinguish cached data from confirmed remote state.
- Indicate when an operation is pending synchronization.
- Queue operations only when their semantics are understood.
- Define how conflicts and rejected queued operations are surfaced.
- Do not mark remote work as synchronized before confirmation.
- Provide a manual refresh or retry action when appropriate.

If offline access is not a product requirement, fail gracefully rather than implementing an undocumented synchronization system.

### 8.6 API response parsing

Treat external responses as untrusted input.

- Validate required fields and types.
- Handle missing optional fields explicitly.
- Reject structurally invalid payloads.
- Avoid unchecked casts and assumptions about non-null fields.
- Do not silently replace invalid business-critical values with defaults.
- Record a sanitized diagnostic when parsing fails.

---

## 9. Authentication and Authorization Errors

### 9.1 Authentication failures

Handle invalid credentials, expired sessions, revoked tokens, failed refreshes, and provider outages according to the approved authentication design.

- Never display credentials or tokens in error messages.
- Do not repeatedly refresh a token in a loop.
- Prevent concurrent refresh operations from corrupting session state.
- If the session cannot be recovered, clear invalid authentication state safely and request sign-in.
- Preserve unsaved user input where safe and appropriate.

### 9.2 Authorization failures

A forbidden operation must not proceed.

- Enforce authorization at the trusted API or data layer.
- Do not rely on hidden buttons or client-side role checks.
- Show a safe permission message.
- Refresh role or ownership information only when a legitimate state change could explain the failure.
- Avoid automatic retries that cannot change the user's permissions.

### 9.3 Account recovery

Password reset, account recovery, sign-out, and account deletion must follow the approved product and authentication flows.

Do not invent a recovery method or reveal whether an account exists unless the approved design permits it.

---

## 10. Database and Persistence Errors

### 10.1 General requirements

Handle failures in local storage, remote databases, caches, and file persistence.

Potential causes include:

- Read/write failures.
- Corrupted data.
- Missing records.
- Schema mismatches.
- Constraint violations.
- Disk exhaustion.
- Transaction failure.
- Migration failure.
- Serialization/deserialization errors.
- Permission or initialization failures.

### 10.2 Transactions and consistency

- Use transactions for logically atomic multi-step database operations.
- Roll back failed transactions where supported.
- Never report a transaction as committed if commit confirmation failed.
- Preserve the original error when adding context.
- Avoid partial writes that leave related records inconsistent.
- Define idempotent recovery or reconciliation for interrupted operations.

### 10.3 Database migrations

- Test migrations from supported existing schema versions.
- Do not drop or overwrite user data as a shortcut.
- Back up or preserve data when the migration strategy requires it.
- Fail safely when an unsupported schema is detected.
- Document recovery procedures for migration failures.
- Test fresh installation and upgrade paths separately.

### 10.4 Cache failures

A cache failure should not corrupt authoritative data.

If the cache is optional, fall back to the source of truth when feasible. If cached data is stale or invalid, do not present it as confirmed current data.

### 10.5 Duplicate operations

For create, update, and delete operations, define how repeated requests are handled.

Use database constraints, stable identifiers, idempotency keys, or reconciliation as appropriate. UI button disabling alone is not a sufficient guarantee against duplicate operations.

---

## 11. Scheduling, Reminders, and Background Work

Apply this section only to scheduling and reminder features confirmed in the TRD.

### 11.1 Scheduling failures

When creating or updating a scheduled item fails:

- Preserve the user's input where possible.
- Clearly indicate whether the operation failed or its outcome is unknown.
- Refresh the relevant state after ambiguous remote outcomes.
- Detect and resolve conflicts according to approved business rules.
- Do not create duplicate entries automatically during recovery.

### 11.2 Notifications and reminders

Notification delivery and scheduling can fail because of permission changes, operating-system restrictions, device restarts, time-zone changes, or service outages.

- Distinguish saving a schedule from successfully registering a local notification.
- Handle notification permission denial gracefully.
- Reconcile scheduled notifications after relevant changes or app restarts where required.
- Avoid claiming that a notification will definitely appear when the operating system cannot guarantee delivery.
- Do not silently discard failed reminder registrations.
- Keep notification scheduling idempotent to avoid duplicate reminders.
- Record failures with enough safe context to diagnose the issue.

### 11.3 Background tasks

Background work may be deferred, interrupted, or terminated by the operating system.

- Do not rely on continuous background execution unless the platform and approved architecture support it.
- Persist enough state to resume safe work when required.
- Make repeated execution safe.
- Use bounded retries.
- Respect operating-system scheduling and battery constraints.
- Surface important unresolved failures to the user when appropriate.

### 11.4 Time zones and daylight-saving changes

Where date/time behavior is relevant:

- Store authoritative timestamps according to the approved data model.
- Preserve the intended time zone for recurring local-time events when required.
- Distinguish an instant in time from a local calendar date and time.
- Handle ambiguous or nonexistent local times deliberately.
- Test daylight-saving transitions where applicable.
- Avoid silently shifting schedules when the device time zone changes.

---

## 12. Cancellation and Concurrency

Cancellation is not always equivalent to failure.

- Represent user-initiated cancellation distinctly when it is a normal outcome.
- Do not display alarming error messages for expected cancellation.
- Stop unnecessary work when cancellation is supported.
- Ensure disposed widgets do not update UI state after their lifecycle ends.
- Handle concurrent requests so that older responses do not overwrite newer state.
- Prevent duplicate submissions and conflicting writes where appropriate.
- Do not assume cancelling a client request reverses work already committed by a server.

Use operation identifiers, request versioning, cancellation tokens where supported, or other suitable mechanisms when concurrency creates a real risk.

---

## 13. Permissions and Platform Errors

For device capabilities such as notifications or other platform integrations:

- Request permissions only when needed.
- Explain the feature benefit before requesting permission when appropriate.
- Handle denied, permanently denied, restricted, and unavailable states as supported by the platform.
- Provide settings guidance when a permission cannot be requested again normally.
- Never create a permission-request loop.
- Provide a usable alternative if the feature can operate without the permission.
- Treat platform-channel failures as recoverable where possible.
- Test relevant behaviors on actual supported platforms.

Do not assume a permission is granted because it was granted during an earlier session.

---

## 14. File, Media, and Import Errors

If the app supports file or media operations:

- Validate file types and sizes.
- Handle missing, inaccessible, malformed, and corrupted files.
- Handle denied storage or media permissions.
- Prevent path traversal and unsafe file access.
- Avoid loading arbitrarily large files into memory.
- Validate imported records against current business rules.
- Do not overwrite existing user data without an approved confirmation or merge policy.
- Preserve partial progress only when the import/export format and recovery behavior support it.
- Report incomplete exports clearly.

Apply these controls only to file operations the product actually supports.

---

## 15. Logging and Monitoring

### 15.1 Structured error logs

Where logging is approved, capture useful, sanitized fields such as:

- Timestamp.
- Severity.
- Stable error code.
- Affected application component.
- Operation name.
- Application version and build.
- Platform and OS version where relevant.
- Correlation or request identifier, if available.
- Retry count.
- Sanitized exception type.
- Stack trace in a protected diagnostic system where appropriate.

Do not automatically log every field above in every environment. Collect only what is needed and permitted.

### 15.2 Sensitive information that must not be logged

Never log:

- Passwords or one-time codes.
- Access or refresh tokens.
- API keys or private keys.
- Session cookies.
- Full payment credentials.
- Sensitive personal data without a documented, approved need.
- Full request or response bodies that may contain private records.
- Database credentials or connection strings containing secrets.

Redaction must occur before data is sent to a remote logging or crash-reporting service.

### 15.3 Development versus production

**Development:**

- Detailed diagnostics may be available to developers.
- Use local tools and test accounts.
- Keep secrets and sensitive test data out of logs.

**Production:**

- Show safe user-facing messages.
- Restrict access to diagnostics.
- Redact sensitive data.
- Apply retention and access-control policies.
- Use approved crash reporting and monitoring services only.

### 15.4 Correlation IDs

Where supported, associate a request and its resulting error with a correlation ID. This helps investigate a failure across mobile, API, and backend logs without exposing user data.

Do not invent a server correlation ID if the server did not return one.

---

## 16. Error Reporting and User Support

For failures that users may need help resolving:

- Provide a safe reference identifier when available.
- Avoid requiring users to submit secrets or private records.
- Offer a clear support path if the product includes one.
- Collect diagnostic information only with appropriate disclosure and authorization.
- Do not promise that support has received a report unless the reporting operation was confirmed.

User-submitted diagnostic information must be treated as untrusted input.

---

## 17. Error Boundaries and State Management

Error handling must work with the selected state-management solution without coupling business rules to the UI framework.

Every asynchronous feature should define how its state transitions when:

- A request begins.
- A request succeeds.
- A request returns no data.
- A request fails.
- A user cancels.
- A session expires.
- A retry starts.
- A response arrives after the user has left the screen.
- A newer request supersedes an older request.

Use a consistent representation such as sealed states or an equivalent project-approved pattern.

Do not allow an error state to coexist with a success state in ways that misrepresent the current operation.

Avoid catching an error, converting it to an empty list, and showing an empty-state message when the actual meaning is "data could not be loaded."

---

## 18. Retry and Recovery UX

Every recoverable error should have an intentional recovery path.

Examples:

- Network failure → Retry or check connection.
- Invalid input → Correct the relevant field.
- Expired session → Sign in again.
- Conflict → Refresh and resolve the conflict.
- Permission denied → Continue without the feature or open settings where appropriate.
- Corrupt cached data → Rebuild the cache from the authoritative source when safe.
- Unknown save outcome → Refresh or query the operation status before submitting again.

Recovery actions must be real and connected to the relevant operation.

Do not add a Retry button if retrying cannot reasonably change the outcome.

---

## 19. Error Handling Anti-Patterns

The following practices are prohibited:

1. Empty `catch` blocks without a justified, documented reason.
2. Showing raw exception messages directly to users.
3. Swallowing exceptions and returning fabricated success values.
4. Treating every failed request as an empty result.
5. Retrying non-idempotent requests without safeguards.
6. Logging passwords, tokens, secrets, or sensitive payloads.
7. Ignoring asynchronous errors.
8. Updating widget state after disposal.
9. Leaving the UI in an infinite loading state.
10. Assuming a timed-out write did not reach the server.
11. Catching every error and classifying it as a network failure.
12. Hiding authorization failures by retrying indefinitely.
13. Marking reminders or background tasks as completed before confirmation.
14. Silently discarding unsaved user input.
15. Claiming that a bug is fixed without testing the relevant behavior.
16. Suppressing all diagnostics to keep logs clean.
17. Implementing generic error handling that loses the original cause.
18. Introducing a global handler as a substitute for operation-level recovery.

---

## 20. Testing Requirements

Error handling must be tested deliberately, not only through successful flows.

### 20.1 Unit tests

Test:

- Validation errors.
- Business rule violations.
- Error-to-message mapping.
- API status mapping.
- Parsing failures.
- Retry eligibility and retry limits.
- Conflict resolution.
- State transitions after failure.
- Cancellation behavior.
- Idempotency-related logic.

### 20.2 Widget tests

Test:

- Loading indicators.
- Inline validation.
- Page-level error states.
- Retry controls.
- Empty states versus failed loads.
- Preserved user input.
- Disabled submission during an active request.
- Session-expired behavior.
- Permission-denied messaging.
- Accessible labels and relevant semantics.

### 20.3 Integration tests

Test important end-to-end failures such as:

- Offline data loading.
- Server timeout.
- Invalid or expired session.
- Unauthorized requests.
- Database failure.
- Duplicate submissions.
- Conflicting updates.
- App restart during pending work.
- Failed notification registration, if reminders are supported.
- Recovery from an ambiguous save outcome.

### 20.4 Fault injection

Where practical, simulate:

- Slow responses.
- Dropped connections.
- Malformed payloads.
- HTTP 429 responses.
- HTTP 500 and 503 responses.
- Database constraints and transaction failures.
- Permission changes while the app is running.
- Background execution interruption.

### 20.5 Test expectations

- Tests must assert behavior, not merely that an exception was caught.
- Do not use production credentials or real private data in tests.
- Keep failure simulations deterministic where possible.
- Record untested scenarios as limitations when they matter to release readiness.

---

## 21. Example Repository Error Handling Pattern

The following is a conceptual pattern. Adapt names and types to the selected architecture.

### Repository layer

The repository translates implementation-specific failures into the application's agreed error model while preserving diagnostic context.

```dart
Future<Result<Event, AppError>> loadEvent(String id) async {
  try {
    final event = await dataSource.fetchEvent(id);

    if (event == null) {
      return Result.failure(
        const AppError(
          code: AppErrorCode.notFound,
          message: 'Event not found',
        ),
      );
    }

    return Result.success(event);
  } on TimeoutException catch (error) {
    return Result.failure(
      AppError(
        code: AppErrorCode.timeout,
        message: 'The request timed out',
        cause: error,
        retryable: true,
      ),
    );
  } catch (error) {
    // In real code, map known transport and persistence
    // exceptions explicitly before reaching this branch.
    return Result.failure(
      AppError(
        code: AppErrorCode.unknown,
        message: 'Unable to load the event',
        cause: error,
      ),
    );
  }
}
```

`Result`, `Event`, and `dataSource` are illustrative names. The snippet assumes a `Result.success` / `Result.failure` API and is not directly compilable until that API and the project's actual types are defined. Import the correct `TimeoutException` type and handle known network, parsing, and persistence exceptions explicitly.

Do not let a generic catch erase important classification or turn an unexpected programming error into an ordinary user mistake.

### Presentation layer

The UI converts the application error into a safe display state.

```dart
switch (state) {
  case EventLoading():
    return const LoadingView();

  case EventLoaded(:final event):
    return EventDetailsView(event: event);

  case EventFailure(:final error):
    return ErrorView(
      message: error.code == AppErrorCode.networkUnavailable
          ? 'Check your connection and try again.'
          : 'We could not load this item.',
      onRetry: error.retryable ? retryLoad : null,
    );
}
```

This example assumes a sealed-state design and project-defined widgets. Adapt it to the actual state-management solution. Only show a retry action when retrying is appropriate.

---

## 22. Incident Severity

Classify issues by impact to help prioritize response.

| Severity | Description | Example |
|---|---|---|
| Critical | Security breach, widespread data loss, or core service completely unavailable | Unauthorized access to private records |
| High | Major user workflow broken with no reasonable workaround | Users cannot save important records |
| Medium | Important feature degraded but a workaround exists | Search temporarily unavailable |
| Low | Minor inconvenience or cosmetic error | Non-critical display formatting issue |

Severity must be based on real impact, scope, security, and data integrity. Do not use the category of exception alone to determine severity.

For security incidents or potential data exposure, follow the project's approved incident-response process. Do not wait for an ordinary bug-fix release if immediate containment is required.

---

## 23. Definition of Done for Error Handling

A feature is not complete until applicable requirements are met:

- [ ] Expected failure conditions have been identified.
- [ ] User-facing messages are safe and understandable.
- [ ] Relevant loading, success, empty, and failure states are implemented.
- [ ] User input is preserved where appropriate.
- [ ] Retry behavior is bounded and safe.
- [ ] Duplicate and ambiguous operations are handled.
- [ ] Sensitive data is excluded from logs.
- [ ] Authorization failures cannot be bypassed.
- [ ] Errors are observable without exposing internal details.
- [ ] Relevant unit, widget, or integration tests pass.
- [ ] Failure behavior has been reviewed on supported platforms where necessary.
- [ ] Documentation and known limitations are updated.
- [ ] The final report accurately states what was tested.

---

## 24. Remaining Implementation Details

The architecture and product boundaries are defined in `decisions.md`; do not reopen those choices here. Before release, document:

- Logging and crash-reporting provider.
- Store submission process and supported OS versions.
- Privacy, retention, and support-reporting requirements.
- Any background execution behavior required by an approved feature.

Do not add a vendor-specific implementation merely because it appears in an example.

---

## Final Rule

**Every failure must have an intentional outcome: recover safely, communicate clearly, preserve data, or escalate for diagnosis. Never hide a failure, fabricate success, expose sensitive information, or retry an unsafe operation blindly.**

This document defines error-handling standards. The approved TRD defines the product behavior that must be implemented.
