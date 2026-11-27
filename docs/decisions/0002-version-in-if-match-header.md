# 0002: Send the version in the If-Match header

Status: accepted
Date: 2026-11-27

## Context

Edits and deletes need the version the client read, so the server can refuse a stale change. Our first draft put the version in the request body for edits and in the query string for deletes. Both have problems:

- Some hosts drop request bodies on `DELETE`, so a body version can disappear silently.
- A version in the URL is written to access logs, and it makes URLs look like data.
- Each endpoint would use a different place for the version.

## Decision

- Edits (`PATCH`) and deletes (`DELETE`) send the version in the `If-Match` header, as a quoted value: `If-Match: "7"`.
- A stale version returns `409 VERSION_CONFLICT` and changes nothing.
- The version is never placed in the query string or the body for these methods.

## Alternatives

- **Query string (`?version=7`).** Works, but puts the version in logs and URLs.
- **Body.** Works for `PATCH`, but some hosts drop bodies on `DELETE`.
- **`412 Precondition Failed`.** This is the strict HTTP answer to a failed `If-Match`. We chose `409` instead, so every conflict uses one code, and clients handle it in one place. This is a deliberate choice, not an oversight.

## Consequences

- Clients must send the `If-Match` header. The API must allow that header in CORS settings for browser clients.
- The response for a successful read should include the version in an `ETag` header, so clients can copy it into `If-Match`. Optional, but recommended.
- `docs/standards/api.md` uses this rule in its editing and deleting sections.
