# Feature: `<name>`

Copy this file to `docs/features/<name>.md` at the first checkpoint of the feature guide (`docs/guides/start-a-feature.md`). Keep each section short, and put details in the linked docs. A new feature fits on about one page. A baseline spec for an existing feature is longer, because it records what the code does today. Replace every `<placeholder>`. Delete any section that does not apply, but say why in a comment.

Sections marked "(existing feature)" are for code that already exists and has no spec. Sections marked "(backend)" are for projects that own the API and the database. A frontend-only project fills in "API used" and leaves "Data" and "API" out.

Status: `draft | confirmed, <YYYY-MM-DD>, by <user and owner names> | inferred from code, not confirmed`
Owner: `<name of the person who keeps this spec up to date>`
Last updated: `<YYYY-MM-DD>`

Do not write code until the status says `confirmed`. A spec marked `inferred from code` describes what the code does, not what anyone decided, so confirm it with the owner first.

## Resume here

Update this block after every step, then commit. Do not wait until the end of a session, because the agent can stop at any time. A person with no agent must be able to continue from this block alone.

- Current slice: `<slice name>` (`<not started | in progress | shipped>`)
- Last finished: `<one sentence>`
- Next step: `<one sentence>`
- Blocked on: `<open question or missing API>`, ask `<who>`
- How to check: open `<page or URL>`, you should see `<result>`

## Sources

Where each requirement came from. Link to the exact place (the Figma frame, not the whole file). A chat message is a source too, but chat links break, so paste its text under Decided rules.

| Kind | Link | Note |
|---|---|---|
| Requirement | `<link>` | `<what it covers>` |
| Design | `<link to the frame>` | `<which screen>` |
| Ticket | `<link>` | `<what it asked for>` |
| API docs | `<link>` | `<which endpoints>` |

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

## Screens and states

One block per screen. List every state the user can see. A missing state is the most common frontend bug. Label each row Observed (seen in the code) or Decided (confirmed by the owner).

### `<Screen name>`

Design: `<link to the Figma frame>`

| State | What the user sees | Label |
|---|---|---|
| Loading | `<what is shown while data loads>` | `<Observed | Decided>` |
| Empty | `<what is shown when there is no data>` | `<Observed | Decided>` |
| Error | `<what is shown when a request fails, and whether the user can retry>` | `<Observed | Decided>` |
| No permission | `<what is shown when the user may not use this>` | `<Observed | Decided>` |
| Other | `<partial data, unsaved changes, a failed reload after success, ...>` | `<Observed | Decided>` |

## Observed behavior (existing feature)

What the code does today. True today, but it may not be intended. Do not treat it as a decided rule. Put screen states in Screens and states, not here.

- [Observed] `<what the screen or endpoint does>` (source: `<file path>`)

## Why the code is this way (existing feature)

For each block of code whose reason is not obvious. Look in git history, PRs, and tickets. If no reason is found, write "Unknown" and add an open question. Never invent a reason. If git history or tickets were not available to you, say so under the table, so nobody thinks they were checked.

| Code | Reason | Evidence |
|---|---|---|
| `<file and function>` | `<reason>` | `<commit, PR, or ticket link>` |
| `<file and function>` | Unknown | none found, see Open questions |

## Data (backend)

Describe contracts, not code.

- Owner feature: `<feature>` (see `docs/architecture/features.md`)
- Tables added or changed: `<tables>` (see `docs/domain/data-model.md`)
- Delete behavior: `<archive, delete, or history rule>`

## API (backend)

Endpoints this feature owns. Describe contracts, not code.

| Method and path | Purpose | Success | Errors |
|---|---|---|---|
| `<METHOD> <path>` | `<purpose>` | `<status>` | `<codes>` |

Follow `docs/standards/api.md` for response shape, versions, and errors.

## API used

Endpoints this feature calls but does not own. A missing or mismatched endpoint is an open question for its owner.

| Screen needs | Endpoint | Exists? | Matches? | Owner | Note |
|---|---|---|---|---|---|
| `<what the screen needs>` | `<METHOD> <path>` | `<yes | no>` | `<yes | partly | no | n/a>` | `<team or name>` | `<what is missing>` |

## Edge cases

- `<Case>`: `<what should happen>`
- `<Case, such as a stale version, a missing record, or a record in another tenant>`: `<what should happen>`

## Decided rules

Every business or authorization rule that the owner has decided. Fill in one row per rule. If the owner has not decided a rule, do not add it here. Add it under Open questions.

In Source, paste the owner's exact words and add the link. Links to tickets and chats get edited, moved, or lost. The words stay.

| Rule | Decision | Decided by | Date | Source |
|---|---|---|---|---|
| `<rule>` | `<what was decided>` | `<owner name>` | `<YYYY-MM-DD>` | `"<the owner's exact words>"` and `<link to message, ticket, or decision record>` |

## Open questions

Business or authorization rules that are not decided yet. Do not guess these. Write the question here, and build only the parts that do not depend on it. Until the question is answered, refuse or disable the affected action.

- `<Question>` (owner: `<name>`)

## Change request

The change being made now. For an existing feature, write this first, on top of the baseline above. Slices and Acceptance criteria come from it. For a new feature, leave this section out.

- Asked by: `<name>`, on `<YYYY-MM-DD>`
- In their words: `"<the exact request>"` (`<link>`)

| Today (Observed) | Wanted | Blocked by open question? |
|---|---|---|
| `<what the code does now>` | `<what it should do>` | `<question number, or no>` |

## Slices

Build in this order. Each slice must be buildable, testable, and shown to the user on its own. Stop after a slice and check it before the next one. For an existing feature, slices describe the change request, not the whole feature.

1. **`<Slice name>`**: done when `<one observable result>`. Depends on: `<slices or open questions, or none>`. Status: `not started | in progress | shipped <YYYY-MM-DD>`.

## Acceptance criteria

Write each slice's checks under its own heading. Use Given / When / Then for each behavior, so that each check maps to one test. Keep six to nine checks per slice.

### Slice 1: `<Slice name>`

- [ ] Given `<starting condition>`, when `<action>`, then `<observable result>`.
- [ ] Permission check: no access without the permission, and no access across tenants.
- [ ] Concurrency: a stale edit returns `409` and changes nothing.
- [ ] Tests listed in `docs/standards/testing.md` are written and pass.

## Change log

One row each time the owner changes the feature after it was confirmed. Update the spec first, then add the row, then change the code.

| Date | Asked by | What changed | Ticket |
|---|---|---|---|
| `<YYYY-MM-DD>` | `<name>` | `<one sentence>` | `<link>` |
