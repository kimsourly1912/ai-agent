# 0001: Error codes for rule violations

Status: accepted
Date: 2026-11-27

## Context

A request can fail in three different ways:

1. The input is malformed, such as a missing field or a wrong type.
2. The input is well formed, but a business rule rejects it. Example: a discount is larger than the price.
3. The input is well formed, but the record's current state does not allow the change. Example: the item has history and cannot be deleted.

The API standard used `400` for the first case and "409 or 422" for the others, without choosing.

## Decision

- `400 Bad Request`: the input is malformed or fails its schema. Include `fieldErrors`.
- `422 Unprocessable Content`: the input is valid, and a business rule rejects it. The answer would be the same no matter what the record's current state is.
- `409 Conflict`: the request conflicts with the record's current state. Examples: a stale version, an item that has history, an order that is already paid.

A quick test: **does the answer depend on the record's current state?** If yes, use `409`. If no, use `422`.

## Alternatives

- **Use `409` for every business rule.** Simpler, but a client cannot tell "fix your input" from "reload and try again." The two need different messages.
- **Use `400` for every business rule.** This is common, but RFC 9110 defines `400` as a malformed request, so it hides the difference between a bad request and a rule violation.

## Consequences

- Clients branch on the `code` field, not on the status alone. The `code` says which rule failed.
- `docs/standards/api.md` uses this rule in its status code table and its edge cases.
- If a project already uses `400` for rule violations, it must record a new decision to change, not edit this one.
