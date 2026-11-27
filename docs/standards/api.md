# API Standard

Defines the shape of requests and responses, the error format, and the rules for editing and listing data.
Read this before you add or change an endpoint. Replace every `<placeholder>` with your project's value.

## The problem this solves

Clients are apps, pages, and other services. Each one depends on the exact shape of what the server returns. If each endpoint invents its own shape, error format, or list format, clients break in small ways that are hard to trace. A shared standard keeps every endpoint predictable.

## Mental model

The API is a contract. Once a client depends on a field, removing or renaming it breaks that client. So:

- Responses describe **resources** (things), not internal tables.
- Errors have **one format**, so clients can handle them in one place.
- Edits are **safe under concurrency**, so two people cannot silently overwrite each other.

## Request flow

```text
Request arrives
        ↓
Authenticate and resolve tenant (see security.md)
        ↓
Check permission
        ↓
Validate input against a schema → 400 with field errors if invalid
        ↓
Call the service
        ↓
Map the result to the response shape
        ↓
Return the response (or a standard error)
```

## Resources and URLs

- Use **plural nouns**: `/<resource>s`, `/<resource>s/:id`.
- Use **HTTP methods** for the action: `GET` read, `POST` create, `PATCH` change some fields, `DELETE` remove.
- Put actions that are not CRUD in a sub-path with a verb: `POST /<resource>s/:id/<action>`.
- Keep the tenant in the path if your project has tenant URLs (for example `/api/<tenant>/<resource>s`). Check it against the session on every request.

## Responses

- Return the resource itself, with no wrapper. `{ "id": "...", "name": "..." }`, not `{ "data": { ... } }`.
- Nullable fields are always present, with `null`. A field is never missing.
- Never return a raw database row. Map it to the response shape, so internal columns cannot leak.
- Use one naming style in JSON. Pick `camelCase` and keep it.

## Status codes

| Code | Use it when |
|---|---|
| `200` | Read or update succeeded |
| `201` | Create succeeded. Return the new resource. |
| `204` | Delete succeeded. No body. |
| `400` | Input failed validation. Include field errors. |
| `401` | No valid session |
| `403` | Signed in, but not allowed to do this |
| `404` | Resource does not exist, or it belongs to another tenant |
| `409` | The request conflicts with the record's current state: a stale version, or a record that has history. See `docs/decisions/0001-business-rule-error-codes.md`. |
| `422` | The input is valid, but a business rule rejects it no matter what the record's state is. |
| `500` | Something went wrong on the server. Log it. Do not show details. |

**Return `404` for other tenants' records, not `403`.** A `403` tells the caller that the record exists. A `404` reveals nothing.

## Error format

Every error uses the same shape:

```json
{
  "statusCode": 409,
  "message": "This item was changed by someone else. Reload it and try again.",
  "data": {
    "code": "VERSION_CONFLICT",
    "requestId": "..."
  }
}
```

Rules:

- `code` is a fixed, machine-readable string. Clients branch on `code`, not on `message`.
- `message` is for people. It is safe to show.
- `requestId` lets support find the log entry. Never include stack traces or SQL in responses.
- For `400`, add `fieldErrors`, a map from field path to message, such as `{ "items.0.price": "..." }`.

## Editing: versions

Every editable record has a `version`.

1. The client reads the record and gets `version: 7`.
2. The client sends the update with the header `If-Match: "7"`. See `docs/decisions/0002-version-in-if-match-header.md`.
3. The server applies the update only if the stored version is still `7`, and sets it to `8`.
4. If the stored version is no longer `7`, the server returns `409 VERSION_CONFLICT` and changes nothing.

The UI should say "changed by someone else, reload," not overwrite silently. Silent last-write-wins is not allowed.

The check must happen in the same atomic write as the change. Checking first and writing later leaves a gap where another request can slip in.

## Listing

Use one list style across the project.

- **Pages with numbers:** `?page=1&pageSize=20` (set a maximum, for example 100). Response: `{ "items": [...], "page": 1, "pageSize": 20, "total": 143 }`.
- **Growing feeds:** `?limit=50&cursor=...`. Response: `{ "items": [...], "nextCursor": "..." }`. Use this for logs, history, and anything that grows without limit. Cursors avoid skipped and repeated rows, and keep queries fast.
- **Filters:** name them after fields, such as `?status=active`. Use `q` for text search.
- **Sorting:** `?sort=name` ascending, `?sort=-createdAt` descending.
- **Unknown parameters are rejected with `400`.** Silent ignoring hides bugs.

## Deleting

- Send the version of the record to delete in the `If-Match` header, such as `If-Match: "7"`. Do not use the query string or the body. Some hosts drop bodies on `DELETE`, and a version in the URL is written to logs.
- If the record has history, refuse with `409` and a reason code, such as `ITEM_HAS_HISTORY`. Do not silently archive. Archive is a separate action (see `data-model.md`).

## Money and repeated requests

Actions that move money, points, or vouchers **require** an `Idempotency-Key` header. Create requests whose duplicate would cause harm, such as orders, should send one. The client sends a unique key with each attempt. If the server sees the same key again, it returns the stored response and does not run the action twice. Keys are kept for 24 hours. Reusing a key with a different request returns `422 IDEMPOTENCY_KEY_REUSED`. See `docs/decisions/0003-idempotency-key-scope.md`.

## Edge cases

- **Breaking a field.** Adding a field is safe. Removing, renaming, or changing the meaning of a field breaks clients. Add the new field, keep the old one until clients move, then remove it in a separate change.
- **Validation vs. business rules.** Malformed input is `400`. A valid request that breaks a rule which does not depend on the record's state is `422`. A request that conflicts with the record's current state is `409`. See `docs/decisions/0001-business-rule-error-codes.md`.
- **Dates and money in responses.** Send dates as ISO 8601 strings in UTC. Send money as integers in minor units with a currency code. Never send floats for money.
