# Feature: `<name>`

Copy this file to `docs/features/<name>.md` before you build the feature. Keep it short: one page is enough. Replace every `<placeholder>`. Delete any section that does not apply, but say why in a comment.

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

Describe the main flow in order. Use numbered steps.

1. `<User or system action>`
2. `<What the server does>`
3. `<What the user sees>`

## Data

- Owner feature: `<feature>` (see `docs/architecture/features.md`)
- Tables added or changed: `<tables>` (see `docs/domain/data-model.md`)
- Delete behavior: `<archive, delete, or history rule>`

## API

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

## Acceptance criteria

Each item must be testable.

- [ ] `<Observable behavior>`
- [ ] Permission check: no access without the permission, and no access across tenants.
- [ ] Concurrency: a stale edit returns `409` and changes nothing.
- [ ] Tests listed in `docs/standards/testing.md` are written and pass.
