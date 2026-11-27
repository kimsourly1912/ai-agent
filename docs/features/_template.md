# Feature: `<name>`

Copy this file to `docs/features/<name>.md` at the first checkpoint of the feature guide (`docs/guides/start-a-feature.md`). Keep it short: one page is enough for the behavior, and details go in the linked docs. Replace every `<placeholder>`. Delete any section that does not apply, but say why in a comment.

Status: `draft | confirmed, <YYYY-MM-DD>, by <user and owner names>`
Owner: `<name of the person who keeps this spec up to date>`
Last updated: `<YYYY-MM-DD>`

Do not write code until the status says `confirmed`. Update `Last updated` and the slice status each time a slice ships.

## Problem

`<Who has this problem, and what goes wrong for them today. Two or three sentences.>`

## Goal

`<One or two sentences: what the user can do after this feature exists, and why it matters.>`

## Non-goals

- `<Something a reader might expect, that this feature will not do.>`

## Users and permissions

| Who | Can do | Permission name |
|---|---|---|
| `<role>` | `<action>` | `<resource>.<action>` |

Link to the permission table in `docs/standards/security.md` if a new permission is added.

## Behavior

Describe the main flow in order. Use numbered steps. Describe what happens, not how the code is written.

1. `<User or system action>`
2. `<What the server does>`
3. `<What the user sees>`

## Data

Describe contracts, not code.

- Owner feature: `<feature>` (see `docs/architecture/features.md`)
- Tables added or changed: `<tables>` (see `docs/domain/data-model.md`)
- Delete behavior: `<archive, delete, or history rule>`

## API

Describe contracts, not code.

| Method and path | Purpose | Success | Errors |
|---|---|---|---|
| `<METHOD> <path>` | `<purpose>` | `<status>` | `<codes>` |

Follow `docs/standards/api.md` for response shape, versions, and errors.

## Edge cases

- `<Case>`: `<what should happen>`
- `<Case, such as a stale version, a missing record, or a record in another tenant>`: `<what should happen>`

## Decided rules

Every business or authorization rule that the owner has decided. Fill in one row per rule. If the owner has not decided a rule, do not add it here. Add it under Open questions.

| Rule | Decision | Decided by | Date | Source |
|---|---|---|---|---|
| `<rule>` | `<what was decided>` | `<owner name>` | `<YYYY-MM-DD>` | `<link to spec section, decision record, or message>` |

## Open questions

Business or authorization rules that are not decided yet. Do not guess these. Write the question here, and build only the parts that do not depend on it. Until the question is answered, refuse or disable the affected action.

- `<Question>` (owner: `<name>`)

## Slices

Build in this order. Each slice must be buildable, testable, and shown to the user on its own. Stop after a slice and check it before the next one.

1. **`<Slice name>`**: done when `<one observable result>`. Depends on: `<slices or open questions, or none>`. Status: `not started | in progress | shipped <YYYY-MM-DD>`.

## Acceptance criteria

Write each slice's checks under its own heading. Use Given / When / Then for each behavior, so that each check maps to one test. Keep six to nine checks per slice.

### Slice 1: `<Slice name>`

- [ ] Given `<starting condition>`, when `<action>`, then `<observable result>`.
- [ ] Permission check: no access without the permission, and no access across tenants.
- [ ] Concurrency: a stale edit returns `409` and changes nothing.
- [ ] Tests listed in `docs/standards/testing.md` are written and pass.
