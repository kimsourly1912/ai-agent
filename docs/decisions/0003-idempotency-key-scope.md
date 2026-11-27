# 0003: Idempotency keys for money, points, and create requests

Status: accepted
Date: 2026-11-27

## Context

A network retry or a double click can send the same request twice. For most requests that is harmless. For money, points, or vouchers, it can charge a customer twice or give away a voucher twice. Creating an order can also duplicate data if a retry happens.

## Decision

- **Required:** every action that moves money, points, or vouchers. The request must send an `Idempotency-Key` header. Without it, the server refuses with `400`.
- **Recommended:** every `POST` that creates a record whose duplicate would cause harm, such as an order. The server accepts the key when sent and ignores it when missing.
- **Storage:** the server stores the key with the response it returned, for 24 hours. A repeated key returns the stored response and does not run the action again.
- **Key reuse with a different request:** the server refuses with `422` and code `IDEMPOTENCY_KEY_REUSED`. A key identifies one request, so the same key with a different body is a client bug.

## Alternatives

- **Only money.** Simpler, but a duplicate order is still a real problem for the customer and the cafe.
- **Every write.** Costs storage and adds a header to every call, for little benefit on simple edits. Edits are already protected by the version rule (`0002`).
- **Let the database handle it with unique constraints.** Good for some cases, but money actions often involve an external service that the database cannot protect.

## Consequences

- `docs/standards/api.md` lists which endpoints require the key.
- `docs/standards/testing.md` requires a test that sends the same key twice.
- The key store needs a cleanup job, because keys expire after 24 hours.
