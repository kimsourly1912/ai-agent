# CLAUDE.md

Entry point for AI agents working in this repository. Read this file first.
Keep this file short. Details live in `docs/`. Do not copy rules from `docs/` into here; link to them.

Replace every `<placeholder>` with your project's value. Delete any section that does not apply.

## Project

- Name: `<project name>`
- Purpose: `<one sentence: what the system does and for whom>`
- Stack: `<language, framework, database, hosting>`

## Commands

- Install: `<command>`
- Dev server: `<command>`
- Test: `<command>`
- Typecheck and lint: `<command>`
- Database migrations: `<command>`

## Where to find things

Read the linked doc before you change code in that area. If a doc does not exist yet, say so. Do not guess its content.

| Question | Read |
|---|---|
| How the system fits together | `docs/architecture/overview.md` |
| Which module owns this code, and what may depend on what | `docs/architecture/modules.md` |
| Tables, relations, constraints, delete behavior | `docs/domain/data-model.md` |
| Meaning of domain terms | `docs/domain/glossary.md` |
| Who may do what | `docs/standards/security.md` |
| API format, status codes, errors | `docs/standards/api.md` |
| Which tests are required | `docs/standards/testing.md` |
| Why a decision was made | `docs/decisions/` |
| What a feature must do | `docs/features/<name>.md` |

## Hard rules

These are non-negotiable. Examples are given in brackets; replace them with your project's rules.

1. `<Security rule. Example: every server-side read and write is checked against the authenticated user.>`
2. `<Data rule. Example: every query on a tenant-owned table is scoped by tenant ID.>`
3. `<Concurrency rule. Example: every update to a versioned record requires the current revision; stale updates return a conflict.>`
4. Database constraints are the final integrity check. Application validation does not replace them.
5. Never trust the client for identity, roles, ownership, or tenant membership. Derive them from server context.

## How to work

1. Read the docs that apply to your task (see the table above).
2. Find the owning module in `docs/architecture/modules.md`. Put code there.
3. Look at existing code in the same module and follow its patterns. Do not invent a new pattern when one already exists.
4. Add or update tests for the change, as required by `docs/standards/testing.md`.
5. Run typecheck, lint, and tests before you report the work as done.
6. If the docs and the code disagree, say so in your reply. Do not silently pick one.
7. If a hard rule blocks the task, stop and ask. Do not break the rule to finish.
8. Do not refactor unrelated code while doing a task. Mention it instead.

## Ask before you

- Change the database schema, or run a migration that deletes or rewrites data.
- Change a decision recorded in `docs/decisions/`.
- Add a dependency, or change deployment or environment configuration.
- Delete data, files, or branches.

## Never

- Put secrets, tokens, passwords, or real environment values in code, docs, or replies.
- Copy a rule into a second file. Link to the one place where it is defined.
