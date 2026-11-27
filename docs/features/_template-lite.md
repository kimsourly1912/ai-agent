# Feature: `<name>`

Use this for a **small new feature**: one screen or one action, no new tables, no money or points, and no new permissions. If any of those is false, use `docs/features/_template.md`. For a feature that already exists in code, also use the full template, because it records what the code does today.

Copy this file to `docs/features/<name>.md` at the start of the small flow (`docs/guides/start-a-feature.md`). Replace every `<placeholder>`. Delete a section only if it does not apply, and say why in a comment. Keep the whole file to about one page.

Status: `draft | confirmed, <YYYY-MM-DD>, by <user and owner names>`
Owner: `<name of the person who keeps this spec up to date>`
Last updated: `<YYYY-MM-DD>`

Do not write code until the status says `confirmed`.

## Resume here

Update after every step, then commit. A person with no agent must be able to continue from this block alone.

- Current step: `<what you are doing>` (`<not started | in progress | done>`)
- Last finished: `<one sentence>`
- Next step: `<one sentence>`
- Blocked on: `<open question or missing API>`, ask `<who>`
- How to check: open `<page or URL>`, you should see `<result>`

## Goal

`<Who can do what after this feature exists, and why it matters. One or two sentences.>`

## Must never happen

- `<Something that must not be possible. If nobody can say, write "not sure yet" and add an open question.>`

## Sources

- `<link to the requirement, the design frame, or the ticket>`

## States (frontend)

Delete this section for a backend-only project.

- Loading: `<what the user sees>`
- Empty: `<what the user sees>`
- Error: `<what the user sees, and whether they can retry>`
- No permission: `<what the user sees>`

## Decided rules

Only rules the owner has decided. Paste the owner's exact words, and add the link. If the owner has not decided it, put it under Open questions.

| Rule | Decision | Decided by | Date | Source |
|---|---|---|---|---|
| `<rule>` | `<what was decided>` | `<owner name>` | `<YYYY-MM-DD>` | `"<the owner's exact words>"` and `<link>` |

## Open questions

Do not guess these. Build only the parts that do not depend on them. Refuse or disable the affected action until the question is answered.

- `<Question>` (ask: `<name>`)

## Acceptance criteria

Each item must be testable. Write behavior as Given / When / Then. Keep it to six to nine items. If you need more, the feature is not small: switch to the full template.

- [ ] Given `<starting condition>`, when `<action>`, then `<observable result>`.
- [ ] The screen shows every state listed above.
- [ ] Permission check: the server refuses the action without the permission.
- [ ] Concurrency (if the feature edits a record): a stale edit returns `409` and changes nothing.
- [ ] Tests listed in `docs/standards/testing.md` are written and pass.

## Change log

One row each time the owner changes the feature after it was confirmed. Update the spec first, then add the row, then change the code.

| Date | Asked by | What changed | Ticket |
|---|---|---|---|
| `<YYYY-MM-DD>` | `<name>` | `<one sentence>` | `<link>` |
