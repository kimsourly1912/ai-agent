# AGENTS.md

Shared rules for AI coding agents in this repository (Claude Code, Codex, Cursor, and others).
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

## Essential rules

Each rule below is a short pointer. The full rule lives in the linked doc. Do not copy it back into this file.

- Preserve security, permissions, and data integrity. If a rule is unclear, ask. Do not guess. See `docs/standards/security.md`.
- Tenant data is always scoped to its tenant. See `docs/standards/security.md` and `docs/domain/data-model.md`.
- The server decides identity and permissions. See `docs/standards/security.md`.
- Each module owns its tables and is used only through its public service. See `docs/architecture/modules.md`.
- Edits carry the version that was read. Stale edits are refused. See `docs/standards/api.md`.
- Database constraints are the final integrity check. See `docs/domain/data-model.md`.
- Secrets never go into code, docs, or replies. See `docs/standards/security.md`.

## Load context for the task

Classify the change **before you edit**. Then read the docs that apply. Read only the sections you need, and follow a link only when you need it to resolve a dependency or an uncertainty.

| Task | Read |
|---|---|
| How the system fits together | `docs/architecture/overview.md` |
| Which module owns this code, and what may depend on what | `docs/architecture/modules.md` |
| Tables, relations, constraints, delete behavior | `docs/domain/data-model.md` |
| Meaning of domain terms | `docs/domain/glossary.md` |
| Who may do what, tenant boundaries | `docs/standards/security.md` |
| API format, status codes, errors | `docs/standards/api.md` |
| Which tests are required | `docs/standards/testing.md` |
| Why a decision was made | `docs/decisions/` |
| What a feature must do | `docs/features/<name>.md` (copy `docs/features/_template.md`) |
| Configuration, migrations, deploy, rollback | `docs/ops/operations.md` |

If a doc does not exist yet, say so in your reply. Do not guess its content.

## How to work

1. Classify the task and read the docs that apply (see the table above).
2. Find the owning module in `docs/architecture/modules.md`. Put code there.
3. Read the existing code in that module and follow its patterns. Do not invent a new pattern when one exists.
4. Add or update tests for the change, as required by `docs/standards/testing.md`.
5. Run typecheck, lint, and tests before you report the work as done.
6. If the docs and the code disagree, say so in your reply. Do not silently pick one.
7. If a rule blocks the task, stop and ask. Do not break the rule to finish.
8. Do not refactor unrelated code. Mention it instead.

## Ask before you

- Change the database schema, or run a migration that deletes or rewrites data.
- Change a decision recorded in `docs/decisions/`.
- Add a dependency, or change deployment or environment configuration.
- Delete data, files, or branches.
