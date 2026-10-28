# API_CONTRACTS.md

## 1. Purpose

This document defines the API contracts for the Schedular application.

It establishes consistent rules for communication between the Flutter mobile application and the backend, including:

- Authentication and session management.
- Schedule item CRUD operations.
- Recurring schedule items and occurrence exceptions.
- Reminder preferences and notification settings.
- User preferences and profile data.
- Request validation and response formats.
- Error handling.
- Pagination and filtering.
- Idempotency and concurrency control.
- Time-zone and date-time semantics.
- Security and authorization.
- API versioning.
- Testing and implementation standards.

The goal is to ensure that the Flutter client and backend implement the same behavior and can evolve without silently breaking each other.

This document defines a proposed contract. It does not claim that the endpoints already exist or that a backend has been deployed.

---

## 2. Status and Technology Decisions

### 2.1 Current status

| Decision | Status |
|---|---|
| Mobile framework | Flutter |
| Client language | Dart |
| API style | Proposed REST API |
| Payload format | JSON |
| API base path | Proposed `/api/v1` |
| Authentication | Bearer access token, provider-dependent |
| Backend provider | Not finalized |
| Database provider | Not finalized |
| API hosting | Not finalized |
| Push notification provider | Not finalized |
| Offline synchronization | Requires approved architecture |
| OpenAPI specification | To be generated or maintained |

All backend-specific details must be reviewed when the backend is selected.

Do not implement provider-specific assumptions in the Flutter application before confirming the actual authentication, database, and API architecture.

### 2.2 Contract authority

This document defines the intended client-server behavior.

When implementation begins:

1. The approved product requirements define the intended product behavior.
2. This document defines the agreed API contract.
3. The OpenAPI specification, once established as the generated or maintained contract source, must remain consistent with this document.
4. Backend implementation and Flutter client models must conform to the approved contract.
5. Any incompatible change requires explicit approval and a documented migration plan.

Do not change a contract silently to accommodate an implementation shortcut.

---

## 3. API Design Principles

Every API endpoint must follow these principles:

- Use resource-oriented URLs.
- Use standard HTTP methods and status codes.
- Use JSON for request and response bodies unless a specific endpoint requires another format.
- Validate all client-supplied values on the server.
- Enforce authorization on every protected resource.
- Return consistent error structures.
- Use stable identifiers for resources.
- Represent timestamps and time zones explicitly.
- Support safe retries for eligible operations.
- Avoid exposing internal database structures unnecessarily.
- Avoid returning secrets or sensitive information.
- Keep the contract independent of a specific Flutter state-management package.
- Document every endpoint's authentication, validation, side effects, and failure behavior.

The server is authoritative for access control and persisted data. Client-side validation improves usability but does not replace server-side validation.

---

## 4. Base URL and Environment Configuration

The proposed API path is:

`/api/v1`

Each environment must use a separately configured base URL.

| Environment | Purpose |
|---|---|
| Development | Local development and developer testing |
| Testing | Automated and integration testing |
| Staging | Pre-release validation |
| Production | Real user traffic |

The actual base URLs must be configured through the approved build and deployment process.

### Requirements

- Do not hardcode production URLs throughout the Flutter codebase.
- Do not embed backend credentials or privileged API secrets in the mobile application.
- Use HTTPS for non-local environments.
- Do not send production user data to development or staging systems.
- Keep environment-specific settings explicit.
- Validate the configured API URL during startup or initialization.
- Ensure release builds cannot accidentally point to development services.

Environment selection must be controlled by the approved build configuration.

---

## 5. Common HTTP Conventions

### 5.1 Methods

| Method | Intended use |
|---|---|
| `GET` | Retrieve a resource or collection |
| `POST` | Create a resource or execute an explicitly documented operation |
| `PUT` | Replace a resource representation |
| `PATCH` | Partially update a resource |
| `DELETE` | Delete a resource |

Use HTTP methods consistently. Do not use `GET` for operations that modify data.

### 5.2 Headers

Protected requests should include:

```http
Authorization: Bearer <access_token>
Accept: application/json
Content-Type: application/json
```

`Content-Type` is required when sending a JSON body. It may be omitted for requests without a body.

Optional request headers:

```http
X-Request-ID: <unique-request-id>
Idempotency-Key: <unique-operation-key>
If-Match: "<resource-version>"
```

These headers are used only where the endpoint contract supports them.

- `X-Request-ID` helps correlate client and server diagnostics.
- `Idempotency-Key` allows supported operations to be retried safely.
- `If-Match` supports conditional updates when version-based concurrency is implemented.

The server must not trust a request ID as proof of identity or authorization.

### 5.3 JSON rules

- Use UTF-8 encoded JSON.
- Use `snake_case` for JSON field names.
- Use stable, documented field names.
- Represent identifiers as strings.
- Represent boolean values as JSON booleans.
- Represent numeric values using suitable JSON number types.
- Use explicit `null` semantics where supported.
- Do not encode structured data as JSON strings without a specific reason.

Example:

```json
{
  "id": "sch_01JEXAMPLE",
  "title": "Team meeting",
  "is_completed": false
}
```

The identifier is illustrative, not a required production identifier format.

---

## 6. Standard Response Formats

### 6.1 Successful response

A successful single-resource response should use the resource representation directly unless the endpoint documents an envelope.

Example:

```json
{
  "id": "sch_01JEXAMPLE",
  "title": "Team meeting",
  "description": null,
  "start_at": "2026-10-26T10:00:00Z",
  "end_at": "2026-10-26T10:30:00Z",
  "time_zone": "Asia/Karachi",
  "is_completed": false,
  "created_at": "2026-10-25T12:00:00Z",
  "updated_at": "2026-10-25T12:00:00Z",
  "version": 1
}
```

This is an example of a proposed representation, not a statement that the current database already uses these fields.

### 6.2 Collection response

List endpoints should use a consistent pagination structure.

Example:

```json
{
  "data": [
    {
      "id": "sch_01JEXAMPLE",
      "title": "Team meeting",
      "is_completed": false
    }
  ],
  "pagination": {
    "next_cursor": null,
    "has_more": false
  }
}
```

Resource-specific fields may be omitted from this shortened example.

### 6.3 Empty responses

Use `204 No Content` when the endpoint succeeds and intentionally returns no response body.

Do not attempt to decode an empty body as JSON in the Flutter client.

### 6.4 Response consistency

Each endpoint must document:

- Successful status codes.
- Response schema.
- Whether the response contains a resource, collection, or no body.
- Pagination behavior.
- Relevant headers.
- Possible error responses.

Do not mix incompatible response envelopes across endpoints without a documented reason.

---

## 7. Standard Error Contract

Errors should follow a consistent structure.

Example:

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "The request contains invalid fields.",
    "details": [
      {
        "field": "start_at",
        "code": "INVALID_DATETIME",
        "message": "A valid ISO 8601 timestamp is required."
      }
    ],
    "request_id": "req_example_123"
  }
}
```

The `details` array may be empty when no field-level information is appropriate.

### 7.1 Standard error codes

| Code | Meaning |
|---|---|
| `VALIDATION_ERROR` | One or more input values are invalid |
| `UNAUTHENTICATED` | Authentication is missing or invalid |
| `FORBIDDEN` | The authenticated user lacks permission |
| `RESOURCE_NOT_FOUND` | The resource does not exist or is not accessible |
| `CONFLICT` | The requested operation conflicts with current state |
| `VERSION_CONFLICT` | The resource changed since the client last read it |
| `IDEMPOTENCY_CONFLICT` | An idempotency key was reused incompatibly |
| `RATE_LIMITED` | The request exceeded an allowed rate |
| `PAYLOAD_TOO_LARGE` | The request body exceeds the supported limit |
| `UNSUPPORTED_MEDIA_TYPE` | The content type is unsupported |
| `INTERNAL_ERROR` | An unexpected server failure occurred |
| `SERVICE_UNAVAILABLE` | The service is temporarily unavailable |

Error codes are part of the client-server contract and must remain stable.

### 7.2 HTTP status mapping

| HTTP status | Intended meaning |
|---|---|
| `200 OK` | Successful read or update |
| `201 Created` | Resource created |
| `202 Accepted` | Operation accepted for asynchronous processing |
| `204 No Content` | Successful operation without a response body |
| `400 Bad Request` | Malformed request |
| `401 Unauthorized` | Authentication required or invalid |
| `403 Forbidden` | Authenticated but not authorized |
| `404 Not Found` | Resource unavailable at the requested location |
| `409 Conflict` | State or idempotency conflict |
| `412 Precondition Failed` | Conditional update precondition failed |
| `413 Content Too Large` | Payload exceeds the limit |
| `415 Unsupported Media Type` | Unsupported body format |
| `422 Unprocessable Content` | Valid JSON that fails semantic validation |
| `429 Too Many Requests` | Rate limit exceeded |
| `500 Internal Server Error` | Unexpected server failure |
| `503 Service Unavailable` | Temporary service unavailability |

The backend may choose `400` or `422` for validation failures, but the mapping must be consistent and documented.

Do not expose stack traces, SQL errors, internal service credentials, or private infrastructure details in client-visible errors.

---

## 8. Authentication Contract

### 8.1 Authentication model

The exact identity provider and token lifecycle are pending backend approval.

The proposed protected API model uses bearer access tokens.

The client must:

- Obtain tokens through the approved authentication flow.
- Attach valid access tokens to protected requests.
- Handle expired sessions.
- Clear local session data when required by the authentication contract.
- Avoid logging tokens.
- Avoid storing credentials in plain text.
- Use approved secure storage for sensitive session material where applicable.

The client must not invent its own token format or decode a token as proof of authorization.

### 8.2 Proposed endpoints

| Method | Endpoint | Purpose |
|---|---|---|
| `POST` | `/auth/register` | Register an account, if supported |
| `POST` | `/auth/login` | Authenticate, if supported |
| `POST` | `/auth/refresh` | Refresh an access token, if supported |
| `POST` | `/auth/logout` | End a session, if supported |
| `GET` | `/users/me` | Retrieve the current authenticated user's profile |

These authentication endpoints are optional contract placeholders. If the selected identity provider manages authentication directly, use its approved SDK and contract instead of creating redundant endpoints.

### 8.3 Session expiration

When an access token expires:

1. The client handles the authentication error.
2. If refresh is supported, it attempts a controlled refresh.
3. It retries an eligible original request at most according to the approved retry policy.
4. It avoids retry loops.
5. It requires authentication again if refresh fails.

A failed authorization response must not cause every request to retry indefinitely.

### 8.4 Authorization

The server must derive the acting user from the authenticated session.

For every user-owned resource, the server must verify ownership or the applicable access policy.

Never trust a client-supplied `user_id` to establish ownership.

---

## 9. Resource: User Profile

### 9.1 Retrieve the current profile

`GET /users/me`

Authentication: Required.

Successful response:

```json
{
  "id": "usr_01JEXAMPLE",
  "display_name": "Alex",
  "email": "alex@example.com",
  "created_at": "2026-10-25T12:00:00Z",
  "updated_at": "2026-10-25T12:00:00Z"
}
```

Only return fields permitted by the approved user profile model.

### 9.2 Update the current profile

`PATCH /users/me`

Example request:

```json
{
  "display_name": "Alex Morgan"
}
```

Requirements:

- Accept only supported fields.
- Validate field lengths and formats.
- Ignore no invalid fields silently.
- Reject attempts to modify protected fields such as account ownership or authorization roles.
- Return the updated representation.

Account email changes and password changes must follow the approved identity provider's security requirements.

---

## 10. Resource: Schedule Items

The schedule item resource represents a user-owned scheduled activity.

The final field list must align with `database-schema.md` and the approved scheduling domain model.

### 10.1 Proposed resource representation

```json
{
  "id": "sch_01JEXAMPLE",
  "title": "Team meeting",
  "description": null,
  "start_at": "2026-10-26T10:00:00Z",
  "end_at": "2026-10-26T10:30:00Z",
  "time_zone": "Asia/Karachi",
  "is_all_day": false,
  "is_completed": false,
  "created_at": "2026-10-25T12:00:00Z",
  "updated_at": "2026-10-25T12:00:00Z",
  "version": 1
}
```

All identifiers and dates above are examples.

The approved domain model may require additional fields such as category, location, recurrence reference, or occurrence-specific state.

### 10.2 Create a schedule item

`POST /schedule-items`

Authentication: Required.

Example request:

```json
{
  "title": "Team meeting",
  "description": null,
  "start_at": "2026-10-26T10:00:00Z",
  "end_at": "2026-10-26T10:30:00Z",
  "time_zone": "Asia/Karachi",
  "is_all_day": false,
  "is_completed": false
}
```

Expected response: `201 Created`.

The server generates the resource identifier and ownership information.

Validation rules:

- `title` must be non-empty after trimming whitespace.
- `title` and `description` must respect configured length limits.
- `start_at` must be a valid timestamp according to the agreed date-time contract.
- `end_at` must be later than `start_at` when the item has a defined duration.
- `time_zone` must be valid when required by the scheduling model.
- `is_all_day` and `is_completed` must be booleans when supplied.
- Client-supplied ownership and server-managed timestamps must not override trusted server values.
- Unsupported fields must be rejected or handled according to the documented schema policy.

The API must define whether an item without an end time is supported. Do not infer that behavior from a nullable database column.

### 10.3 List schedule items

`GET /schedule-items`

Authentication: Required.

Proposed query parameters:

| Parameter | Purpose |
|---|---|
| `cursor` | Continue a paginated query |
| `limit` | Maximum number of returned items |
| `from` | Inclusive start of the requested time range |
| `to` | Exclusive end of the requested time range |
| `is_completed` | Filter by completion status |
| `updated_since` | Retrieve resources changed after a timestamp |

Example:

`GET /schedule-items?from=2026-10-26T00:00:00Z&to=2026-11-02T00:00:00Z&limit=50`

Requirements:

- Enforce a server-defined maximum page size.
- Validate date ranges.
- Document whether filtering is based on event start time, overlap with an interval, or another rule.
- Use stable ordering, including a tie-breaker.
- Avoid duplicate or missing results when paginating a stable dataset.
- Return only resources accessible to the authenticated user.

The exact filtering semantics must be finalized before implementation.

### 10.4 Retrieve a schedule item

`GET /schedule-items/{schedule_item_id}`

Authentication: Required.

Expected response: `200 OK`.

If the resource does not exist or the user is not permitted to access it, return the documented unavailable-resource response without leaking another user's data.

### 10.5 Replace a schedule item

`PUT /schedule-items/{schedule_item_id}`

Authentication: Required.

Use only when the client supplies a complete valid representation under the approved replacement semantics.

A missing field must not be interpreted ambiguously.

### 10.6 Partially update a schedule item

`PATCH /schedule-items/{schedule_item_id}`

Authentication: Required.

Example request:

```json
{
  "title": "Updated team meeting",
  "start_at": "2026-10-26T10:30:00Z"
}
```

Requirements:

- Only supported mutable fields may be updated.
- Omitted fields remain unchanged.
- Explicit `null` must have a documented meaning for each nullable field.
- Validate the resulting resource, not only the fields included in the patch.
- Recalculate affected reminder plans and recurrence data where applicable.
- Return the updated resource.

### 10.7 Delete a schedule item

`DELETE /schedule-items/{schedule_item_id}`

Authentication: Required.

Expected response: `204 No Content`.

The backend must enforce ownership and apply the approved deletion and retention rules.

If the app uses soft deletion or supports undo, the behavior and representation must be documented rather than assumed.

---

## 11. Resource: Recurrence Rules

Recurring schedule items must use the recurrence model approved for the application.

Do not implement a second, incompatible recurrence engine in the backend and Flutter client.

### 11.1 Proposed recurrence representation

A recurrence configuration may contain:

```json
{
  "frequency": "weekly",
  "interval": 1,
  "days_of_week": [1, 3, 5],
  "until": "2026-12-31",
  "count": null
}
```

This example uses a proposed weekday numbering convention of Monday = 1 through Sunday = 7. The convention must be documented and consistent throughout the system.

Supported frequencies, count rules, exception handling, and time-zone behavior must match the approved recurrence specification.

### 11.2 Proposed endpoints

| Method | Endpoint | Purpose |
|---|---|---|
| `GET` | `/schedule-items/{id}/recurrence` | Retrieve a recurrence rule |
| `PUT` | `/schedule-items/{id}/recurrence` | Create or replace the rule |
| `DELETE` | `/schedule-items/{id}/recurrence` | Remove the recurrence rule |
| `GET` | `/schedule-items/{id}/occurrences` | Retrieve occurrences in a range |

These endpoints may be consolidated into schedule-item representations if that better fits the final architecture.

### 11.3 Occurrence query

`GET /schedule-items/{id}/occurrences?from=...&to=...`

The API must document:

- Whether returned occurrences are calculated or persisted.
- How exceptions are applied.
- How modified occurrences are represented.
- How occurrence identifiers are generated.
- How time-zone transitions are handled.
- Whether the response contains complete occurrences or summary records.

### 11.4 Editing recurrence

The API must distinguish between:

- Updating the entire recurring series.
- Updating a single occurrence.
- Updating an occurrence and future occurrences, if supported.

Do not represent these different operations using an ambiguous generic update.

### 11.5 Recurrence validation

Reject recurrence configurations that violate the approved model, including invalid intervals, invalid weekday values, incompatible count and end conditions, or unsupported combinations.

The API must not silently change recurrence rules to make invalid requests acceptable.

---

## 12. Resource: Occurrence Exceptions

An occurrence exception describes a deviation from a recurring schedule.

Possible exception types include:

- Skipped occurrence.
- Cancelled occurrence.
- Moved occurrence.
- Modified occurrence.
- Completed occurrence, if completion is modeled at occurrence level.

### Proposed endpoints

| Method | Endpoint | Purpose |
|---|---|---|
| `GET` | `/schedule-items/{id}/exceptions` | List occurrence exceptions |
| `PUT` | `/schedule-items/{id}/exceptions/{occurrence_key}` | Create or replace an exception |
| `DELETE` | `/schedule-items/{id}/exceptions/{occurrence_key}` | Remove an exception |

These are proposed endpoints. The final design may use a dedicated occurrence resource instead.

### Requirements

- An exception must reference a valid parent series.
- An occurrence key must be stable and deterministic.
- The server must validate ownership of the parent schedule.
- Removing an exception must have documented restoration semantics.
- Exceptions must not unintentionally modify unrelated occurrences.
- Any affected reminders must be reconciled.
- Repeated requests must not create duplicate exceptions.

---

## 13. Resource: Reminders

Reminder configuration is separate from the platform notification queue.

The API persists reminder intent. Local operating-system notification scheduling remains the responsibility of the Flutter application unless the approved architecture introduces a remote delivery service.

### 13.1 Proposed reminder representation

```json
{
  "id": "rem_01JEXAMPLE",
  "schedule_item_id": "sch_01JEXAMPLE",
  "offset_minutes": 15,
  "enabled": true,
  "created_at": "2026-10-25T12:00:00Z",
  "updated_at": "2026-10-25T12:00:00Z"
}
```

### 13.2 Proposed endpoints

| Method | Endpoint | Purpose |
|---|---|---|
| `GET` | `/schedule-items/{id}/reminders` | List reminder rules |
| `POST` | `/schedule-items/{id}/reminders` | Create a reminder |
| `PATCH` | `/schedule-items/{id}/reminders/{reminder_id}` | Update reminder settings |
| `DELETE` | `/schedule-items/{id}/reminders/{reminder_id}` | Delete a reminder |

### 13.3 Validation

- Verify ownership of the parent schedule item.
- Validate the reminder offset against approved limits.
- Prevent unsupported duplicate rules.
- Reject malformed identifiers.
- Preserve server-managed fields.
- Ensure the response represents persisted configuration rather than claiming device delivery.

### 13.4 Local versus remote scheduling

For a local-notification implementation:

1. Persist the reminder configuration.
2. Receive the updated reminder resource.
3. Recalculate the local notification plan.
4. Schedule or cancel platform notifications.
5. Record local scheduling outcomes in the approved local state store.

The client must not assume that saving a reminder through the API means the operating system has scheduled it.

If the backend later supports remote push delivery, define a separate contract for device registration, delivery attempts, and delivery observability.

---

## 14. Resource: Notification Preferences

### Proposed endpoint

`GET /users/me/notification-preferences`

Retrieve the current user's persisted notification preferences.

`PATCH /users/me/notification-preferences`

Update supported preferences.

Example request:

```json
{
  "reminders_enabled": true,
  "default_offset_minutes": 15
}
```

These fields are proposed and must match the approved preference model.

### Requirements

- Validate supported preference fields.
- Preserve omitted fields during partial updates.
- Define explicit `null` behavior.
- Do not allow client preferences to override operating-system permissions.
- Do not claim that a preference guarantees delivery.
- Keep local notification permission state distinct from synchronized user preferences unless the approved architecture explicitly defines synchronization.

A preference such as `reminders_enabled: true` means the user has enabled the app-level preference. It does not mean device permission is granted or that a reminder has been delivered.

---

## 15. Resource: User Preferences

If general user preferences are required, expose:

`GET /users/me/preferences`

`PATCH /users/me/preferences`

Possible fields include:

- Locale.
- Preferred time format.
- Preferred calendar presentation.
- Theme preference.
- Default schedule duration, if approved.
- Default time zone, where appropriate.

Do not duplicate notification-specific preferences in multiple resources without a defined source of truth.

### Requirements

- Validate locale and time-zone identifiers.
- Apply defaults consistently.
- Document whether a missing preference means a system default or an unset value.
- Keep server-side defaults and client-side defaults aligned.
- Do not infer a user's time zone from an arbitrary device timestamp.

---

## 16. Resource: Categories

If schedule categories are part of the approved product scope, use a consistent category contract.

### Proposed endpoints

| Method | Endpoint | Purpose |
|---|---|---|
| `GET` | `/categories` | List available categories |
| `POST` | `/categories` | Create a user-owned category, if supported |
| `PATCH` | `/categories/{category_id}` | Update a category |
| `DELETE` | `/categories/{category_id}` | Delete a category |

Whether categories are system-defined, user-defined, or both must be decided before implementation.

### Requirements

- Validate category names and allowed attributes.
- Enforce ownership for user-defined categories.
- Define what happens to associated schedule items when a category is deleted.
- Avoid hardcoding server-owned category identifiers into the Flutter client.
- Document whether category deletion is blocked, cascaded, or replaced with a default category.

---

## 17. Resource: Device Registration

Device registration is needed only if the approved architecture includes remote push notifications or server-side device management.

It is not required solely to schedule local notifications.

### Proposed endpoints

| Method | Endpoint | Purpose |
|---|---|---|
| `POST` | `/devices` | Register a device or push token |
| `DELETE` | `/devices/{device_id}` | Unregister a device |

A future device representation may include:

- Device identifier.
- Platform.
- Push token.
- App version.
- Last-seen timestamp.

Never accept a device registration as proof of user identity. Associate it with the authenticated account and validate the submitted platform and token fields.

### Security requirements

- Do not expose push tokens in general profile responses.
- Remove or invalidate stale tokens through the approved lifecycle.
- Do not log full push tokens.
- Define behavior for account sign-out and account deletion.
- Never include privileged provider credentials in the Flutter application.

Do not implement these endpoints unless remote push or device management is approved.

---

## 18. Date-Time Contract

Scheduling APIs must use an explicit date-time policy.

### 18.1 Absolute instants

Use ISO 8601 timestamps with an explicit UTC indicator or offset.

Example:

`2026-10-26T10:00:00Z`

The API must not accept ambiguous timestamps such as:

`2026-10-26 10:00`

unless an endpoint explicitly defines how the missing time-zone information is interpreted.

### 18.2 Time-zone identifiers

When wall-clock semantics matter, use an IANA time-zone identifier, such as:

`Asia/Karachi`

Do not use an offset alone to represent a time zone for future recurring events, because daylight-saving rules can change.

### 18.3 All-day items

Represent all-day items using a date-based contract, such as:

`2026-10-26`

Do not force an all-day date into midnight UTC without documenting the intended behavior. This can shift the displayed date for users in other time zones.

### 18.4 Recurring schedules

The recurrence contract must define whether the series follows:

- The event's original time zone.
- A specified user time zone.
- Another explicitly approved rule.

The client and server must use the same recurrence semantics.

### 18.5 Validation

Reject invalid dates and unsupported time-zone identifiers with a documented validation error.

The server must not silently reinterpret ambiguous local times.

---

## 19. Pagination, Sorting, and Filtering

### 19.1 Cursor pagination

Cursor pagination is preferred for large or frequently changing collections.

Proposed parameters:

- `cursor`
- `limit`

Proposed response:

```json
{
  "data": [],
  "pagination": {
    "next_cursor": null,
    "has_more": false
  }
}
```

### 19.2 Requirements

- Set a documented maximum page size.
- Use deterministic ordering.
- Bind cursors to the relevant query and authorization context.
- Reject malformed or unsupported cursors.
- Avoid exposing internal database query details in cursor values.
- Document how concurrent updates affect subsequent pages.

### 19.3 Sorting

Only documented sort fields may be accepted.

For schedule items, a proposed default order is ascending by start time, with a stable identifier as a tie-breaker.

Do not accept arbitrary client-supplied database column names.

---

## 20. Idempotency and Safe Retries

Mobile networks can fail after the server has already processed a request. Retrying blindly can create duplicate resources or actions.

### 20.1 Idempotency keys

Support idempotency keys for operations where duplicate execution would be harmful, particularly resource creation and other non-idempotent operations.

Example:

```http
Idempotency-Key: 2d6e8d71-example-operation
```

The exact key format and retention period must be documented by the backend.

### 20.2 Required behavior

- A repeated request with the same key and equivalent payload must not create a duplicate effect.
- Reusing a key with a materially different payload must return a documented conflict.
- Keys must be scoped appropriately to the authenticated user and operation.
- The server must define how long idempotency records are retained.
- The client must reuse the same key when retrying the same logical operation.
- A new user action must use a new key.

### 20.3 Naturally idempotent operations

`GET`, a correctly implemented `PUT`, and `DELETE` should follow their defined HTTP semantics.

Do not assume a `POST` operation is safe to retry unless the endpoint explicitly supports idempotency.

---

## 21. Concurrency and Versioning

A schedule item may be edited from multiple devices or clients.

### 21.1 Version field

The proposed resource representation includes a monotonically increasing `version` field.

Every accepted mutation increments the version when the resource changes.

### 21.2 Conditional updates

If the backend supports conditional requests, the client may send:

```http
If-Match: "3"
```

The server must reject an update if the supplied version does not match the current resource version.

The exact ETag representation must follow HTTP semantics and the implementation's versioning model.

### 21.3 Conflict behavior

Return `412 Precondition Failed` for a failed conditional request or `409 Conflict` for a documented application-level conflict.

The Flutter client must not silently overwrite a newer server version after a conflict.

The client should refresh the resource and apply an explicit conflict-resolution policy.

If the first release is single-device and does not need concurrency control, document that limitation rather than implementing a misleading partial solution.

---

## 22. Offline Behavior and Synchronization

Offline behavior depends on the approved local database and synchronization architecture.

Do not assume that offline-first synchronization has been approved solely because the app uses Flutter.

### If offline writes are approved

The architecture must define:

- Local persistence.
- Pending operations.
- Stable client-generated operation identifiers where required.
- Synchronization ordering.
- Retry and backoff policy.
- Conflict detection and resolution.
- Deletion tombstones, if needed.
- Idempotent server writes.
- Recovery after app termination.
- Handling of local changes made on multiple devices.

### Synchronization requirements

- Do not lose local user changes because a request timed out.
- Do not duplicate resources when retrying a request.
- Do not treat a timed-out request as proof that the server rejected it.
- Do not silently overwrite conflicting server changes.
- Do not report synchronization success before the applicable persistence guarantees are met.
- Keep local notification scheduling separate from remote synchronization status.

If offline writes are not approved, implement explicit connectivity and error states rather than creating an undocumented synchronization system.

---

## 23. API Rate Limits and Retry Policy

The backend must define rate limits for authentication and resource operations.

When a response is `429 Too Many Requests`:

- Respect `Retry-After` when supplied.
- Avoid immediate repeated requests.
- Show a useful message if the delay materially affects the user's action.

For transient server or network failures:

- Retry only safe or idempotent operations.
- Use bounded exponential backoff with jitter where appropriate.
- Limit the number of retries.
- Avoid retrying permanent validation and authorization failures.
- Preserve user input when a request fails.

Do not retry all errors indiscriminately.

---

## 24. Flutter Client Implementation

### 24.1 API layer

Use a dedicated API layer rather than issuing HTTP requests directly from widgets.

A proposed organization is:

```text
lib/
  core/
    network/
      api_client.dart
      api_exception.dart
      request_interceptor.dart
      response_parser.dart
  features/
    auth/
      data/
      domain/
    schedule/
      data/
      domain/
    reminders/
      data/
      domain/
    preferences/
      data/
      domain/
```

Adapt this structure to the approved architecture and existing repository.

### 24.2 Responsibilities

`ApiClient`:

- Configures the base URL.
- Applies common headers.
- Adds authentication credentials through the approved mechanism.
- Enforces request timeouts.
- Parses responses.
- Maps errors into application-level exceptions.
- Supports request cancellation where required.
- Applies the approved retry policy.

Feature repositories:

- Convert API data to domain models.
- Expose domain-oriented operations.
- Coordinate API calls with approved local persistence.
- Avoid exposing raw HTTP responses to presentation code.

Presentation code:

- Uses feature-level application interfaces.
- Displays loading, success, empty, and error states.
- Does not contain endpoint strings or authentication logic.

### 24.3 Serialization

- Use explicit, typed request and response models.
- Validate required fields.
- Handle nullable fields consistently.
- Keep serialization independent of widget code.
- Use code generation only where approved.
- Test serialization against representative contract examples.
- Avoid silently replacing invalid server values with misleading defaults.

### 24.4 Timeouts

Configure bounded connection and response timeouts appropriate to the client library.

A timeout means the client did not receive a response within the configured period. It does not prove the server failed to process the operation.

The client must account for this distinction when deciding whether to retry.

---

## 25. Backend Implementation Requirements

The backend must:

- Authenticate protected requests.
- Authorize every resource operation.
- Validate request bodies and query parameters.
- Enforce data ownership.
- Apply database constraints and transaction boundaries where required.
- Return stable error codes.
- Avoid leaking internal errors.
- Support safe retries for designated operations.
- Enforce pagination limits.
- Document all public fields.
- Avoid returning fields that the client does not need.
- Log request IDs and useful diagnostic metadata without logging secrets.
- Test authorization across different users.
- Keep contract and implementation documentation synchronized.

Never rely exclusively on client-side checks to enforce security or data integrity.

---

## 26. Security Requirements

### 26.1 Authentication and access control

- Require authentication for user-owned resources.
- Enforce ownership at the backend or database authorization layer.
- Validate every resource identifier against the authenticated user's access.
- Prevent insecure direct object reference vulnerabilities.
- Ensure list endpoints do not expose other users' data.
- Verify authorization for nested resources such as reminders and occurrence exceptions.

### 26.2 Input validation

- Validate types, lengths, ranges, and allowed values.
- Reject malformed identifiers.
- Prevent mass assignment of server-managed fields.
- Validate recurrence and date-time input.
- Limit request body sizes.
- Treat all client input as untrusted.

### 26.3 Secret management

- Do not ship service-role keys, database passwords, or privileged API secrets in the Flutter app.
- Keep server credentials in the approved secret-management system.
- Do not log authorization headers.
- Redact sensitive fields from diagnostics.

### 26.4 Transport

- Require HTTPS in production.
- Avoid disabling certificate validation.
- Do not transmit tokens over insecure channels.
- Apply the approved platform network security configuration.

### 26.5 Privacy

- Return only the data needed by the client.
- Define account deletion and data-retention behavior.
- Avoid collecting unnecessary analytics or device information.
- Do not include private schedule data in error messages.

---

## 27. Observability and Diagnostics

Every request should be traceable through a request identifier.

The server should record appropriate metadata, such as:

- Request ID.
- Endpoint and method.
- Response status.
- Processing duration.
- Relevant internal error category.
- Deployment or service version where appropriate.

Logs must not contain:

- Access tokens.
- Passwords.
- Service credentials.
- Full sensitive request bodies by default.
- Unnecessary personal schedule details.

The Flutter app may log sanitized diagnostic information in development builds. Production logging must follow the approved privacy and observability policy.

Do not expose internal stack traces through the public API.

---

## 28. API Versioning and Compatibility

The proposed version prefix is `/api/v1`.

### Backward-compatible changes

Potentially compatible changes include:

- Adding an optional request field.
- Adding an optional response field.
- Adding a new endpoint.
- Adding a new documented error code.

Even these changes require review because strict client deserializers or exhaustive enum handling may break.

### Breaking changes

Examples include:

- Renaming or removing a field.
- Changing a field's type.
- Changing timestamp semantics.
- Changing required-field rules.
- Changing recurrence semantics.
- Changing authorization behavior.
- Changing an endpoint's response envelope.
- Changing an existing enum value's meaning.

Breaking changes require an approved migration plan and, where necessary, a new API version.

The backend must not silently change the meaning of an existing field.

---

## 29. OpenAPI and Contract Documentation

Maintain an OpenAPI specification for the approved REST API.

The specification should include:

- Base servers by environment.
- Authentication schemes.
- Every implemented endpoint.
- Request and response schemas.
- Required and optional fields.
- Nullable-field semantics.
- Status codes.
- Standard error schemas.
- Pagination.
- Date-time formats.
- Idempotency requirements.
- Conditional request support.
- Security requirements.
- Deprecated fields and endpoints.

### Contract validation

Where feasible:

- Validate the OpenAPI specification in CI.
- Generate or verify typed client models using the approved workflow.
- Run schema validation against representative requests and responses.
- Detect breaking changes before deployment.
- Keep API documentation synchronized with backend changes.

Do not claim that a generated client or OpenAPI specification exists until it has actually been created and verified.

---

## 30. API Testing Requirements

### 30.1 Unit tests

Test:

- Request validation.
- Response serialization.
- Error mapping.
- Date-time parsing.
- Recurrence validation.
- Pagination parameter validation.
- Idempotency logic.
- Version and concurrency checks.

### 30.2 Integration tests

Test:

- Authentication success and failure.
- Resource creation, retrieval, update, and deletion.
- User ownership enforcement.
- Nested reminder authorization.
- Recurrence and exception behavior.
- Validation errors.
- Pagination.
- Retry and idempotency behavior.
- Conditional update conflicts.
- Database transaction behavior.
- Server error handling.

### 30.3 Security tests

Verify that:

- User A cannot retrieve User B's schedule items.
- User A cannot update or delete User B's items.
- Nested resources cannot bypass parent ownership checks.
- Forged `user_id` values cannot change ownership.
- Privileged fields cannot be mass-assigned.
- Tokens and credentials do not appear in logs.
- Invalid identifiers do not expose internal database information.

### 30.4 Flutter tests

Test:

- Request serialization.
- Response deserialization.
- API error mapping.
- Expired-session behavior.
- Request cancellation where applicable.
- Retry limits.
- Timeouts.
- Empty responses.
- Missing optional fields.
- Unexpected server responses.
- Conflict handling.
- Offline behavior, if approved.

Use mock HTTP clients or a controlled test server rather than relying on production services for unit tests.

### 30.5 Contract tests

The backend and Flutter client must be tested against the same agreed contract examples.

A change to a request or response schema must be accompanied by the corresponding test and documentation update.

---

## 31. Endpoint Summary

The following table summarizes the proposed contract. It is a design inventory, not a declaration that these routes have been implemented.

| Resource | Method | Endpoint |
|---|---|---|
| Current user | `GET` | `/users/me` |
| Current user | `PATCH` | `/users/me` |
| Schedule items | `POST` | `/schedule-items` |
| Schedule items | `GET` | `/schedule-items` |
| Schedule item | `GET` | `/schedule-items/{id}` |
| Schedule item | `PUT` | `/schedule-items/{id}` |
| Schedule item | `PATCH` | `/schedule-items/{id}` |
| Schedule item | `DELETE` | `/schedule-items/{id}` |
| Recurrence | `GET` | `/schedule-items/{id}/recurrence` |
| Recurrence | `PUT` | `/schedule-items/{id}/recurrence` |
| Recurrence | `DELETE` | `/schedule-items/{id}/recurrence` |
| Occurrences | `GET` | `/schedule-items/{id}/occurrences` |
| Exceptions | `GET` | `/schedule-items/{id}/exceptions` |
| Exception | `PUT` | `/schedule-items/{id}/exceptions/{occurrence_key}` |
| Exception | `DELETE` | `/schedule-items/{id}/exceptions/{occurrence_key}` |
| Reminders | `GET` | `/schedule-items/{id}/reminders` |
| Reminders | `POST` | `/schedule-items/{id}/reminders` |
| Reminder | `PATCH` | `/schedule-items/{id}/reminders/{reminder_id}` |
| Reminder | `DELETE` | `/schedule-items/{id}/reminders/{reminder_id}` |
| Notification preferences | `GET` | `/users/me/notification-preferences` |
| Notification preferences | `PATCH` | `/users/me/notification-preferences` |
| General preferences | `GET` | `/users/me/preferences` |
| General preferences | `PATCH` | `/users/me/preferences` |
| Categories | `GET` | `/categories` |
| Categories | `POST` | `/categories` |
| Category | `PATCH` | `/categories/{category_id}` |
| Category | `DELETE` | `/categories/{category_id}` |
| Device registration | `POST` | `/devices` |
| Device registration | `DELETE` | `/devices/{device_id}` |

Authentication endpoints are provider-dependent. Device registration is conditional on remote push or another approved device-management requirement. Category and recurrence endpoints depend on the confirmed product scope.

---

## 32. Definition of Done

An API feature is complete only when all applicable criteria are met.

- [ ] Endpoint and method are documented.
- [ ] Request and response schemas are defined.
- [ ] Required and optional fields are explicit.
- [ ] Nullable-field semantics are documented.
- [ ] Success and failure status codes are defined.
- [ ] Authentication and authorization are implemented.
- [ ] Server-side validation is complete.
- [ ] Resource ownership is enforced.
- [ ] Date-time and recurrence semantics are consistent.
- [ ] Idempotency and concurrency behavior are defined where needed.
- [ ] Pagination is stable and bounded where applicable.
- [ ] Flutter serialization and error handling are implemented.
- [ ] Unit and integration tests pass.
- [ ] Security tests cover unauthorized resource access.
- [ ] OpenAPI documentation is updated.
- [ ] Logging avoids secrets and unnecessary personal data.
- [ ] Deployment and compatibility implications are reviewed.

---

## 33. Implementation Workflow

Before implementing any endpoint:

1. Read `AGENTS.md`, `master_rules.md`, `coding-standards.md`, `database-schema.md`, `notifications.md`, and the relevant product requirements.
2. Inspect the existing Flutter data layer and backend architecture.
3. Confirm the actual backend provider, authentication model, and database schema.
4. Confirm the API fields against the approved domain model.
5. Define request and response schemas.
6. Define authorization and validation rules.
7. Define error cases and retry behavior.
8. Implement backend behavior and tests.
9. Implement typed Flutter models and repository methods.
10. Add client and server contract tests.
11. Validate OpenAPI documentation.
12. Update related documents when the contract changes.
13. Report implemented endpoints, test results, and unresolved decisions.

Do not build endpoints simply because they appear in the proposed inventory. Confirm that each endpoint is required by the approved product scope.

---

## 34. Open Decisions

The following decisions must be finalized before treating this contract as implementation-ready:

1. Backend framework or managed backend provider.
2. Database provider and final schema.
3. Authentication provider and token lifecycle.
4. Whether the application uses a custom REST API or a provider-specific SDK for some resources.
5. Whether offline-first synchronization is required.
6. Whether schedule items are stored locally, remotely, or in both places.
7. Whether recurring occurrences are calculated dynamically or persisted.
8. Whether occurrence exceptions are separate resources.
9. Whether notification preferences are stored remotely, locally, or in both places.
10. Whether remote push notifications are required.
11. Whether categories are user-defined, system-defined, or both.
12. Whether ETag/version-based concurrency is required in the first release.
13. Whether soft deletion and undo are supported.
14. Whether OpenAPI is maintained manually or generated from backend code.
15. Supported pagination, filtering, and maximum page-size limits.

Once these decisions are approved, update the endpoint schemas, examples, and implementation guidance to match the actual architecture.

---

## 35. Final Rule

The API contract is a shared agreement between the Flutter application and the backend.

Every request, response, status code, error, authorization rule, and date-time value must have an explicit meaning.

Never rely on undocumented behavior, client-only security checks, ambiguous timestamps, or silent schema changes.

Implement only approved endpoints, validate every boundary, preserve schedule integrity, and ensure that the Flutter client and backend remain consistent as the Schedular app evolves.
