# Documentation Index

Start here if you are new to this project. Each line says what a doc answers. Read the rules file first: `AGENTS.md`.

## Rules and routing

- `AGENTS.md`: shared rules for every coding agent, and which doc to read for each task.
- `CLAUDE.md`: a wrapper that makes Claude Code read `AGENTS.md`.

## Architecture

- `architecture/overview.md`: the main parts of the system and how a request flows through them.
- `architecture/features.md`: which feature owns which code, and what may depend on what.

## Domain

- `domain/data-model.md`: tables, relations, constraints, and delete behavior.
- `domain/glossary.md`: the exact meaning of project words, and which synonyms not to use.

## Standards

- `standards/security.md`: who may do what, tenant boundaries, sessions, and audit events.
- `standards/api.md`: response shapes, error codes, versions, lists, and idempotency.
- `standards/frontend.md`: how screens, hooks, models, and states are built. For fullstack and frontend-only projects.
- `standards/testing.md`: which tests are required, and how to prove a test protects a rule.
- `standards/review.md`: what a reviewer checks before a change is merged.

## Decisions and features

- `decisions/README.md`: when to record a decision, how to number it, and the index of decisions.
- `features/_template.md`: copy this to start a large feature spec, or to document an existing feature.
- `features/_template-lite.md`: copy this to start a small new feature spec (one screen or one action).
- `guides/start-a-feature.md`: how to turn an idea into a confirmed spec, for small and large features. It also covers documenting an existing feature, and a checklist for working without the agent. Start here if you are not sure how to describe what you want.

## Operations

- `ops/operations.md`: environments, configuration, migrations, deploy, rollback, and backups.

## Which docs apply

Set the project type in `AGENTS.md`, then keep, trim, or delete each doc as shown. Record any doc you delete in `docs/decisions/`.

| Doc | Fullstack | Frontend-only | Backend-only |
|---|---|---|---|
| `architecture/overview.md`, `architecture/features.md` | keep | keep | keep |
| `domain/glossary.md` | keep | keep | keep |
| `domain/data-model.md` | keep | delete. Link to the API owner's docs instead. | keep |
| `standards/security.md` | keep | trim to: the server decides, the UI is not a boundary, secrets | keep |
| `standards/api.md` | keep | trim to: error format, versions, and listing, as the caller sees them | keep |
| `standards/frontend.md` | keep | keep | delete |
| `standards/testing.md` | keep both test lists | keep "Frontend tests" and the guard check | keep "Required tests" and the guard check |
| `standards/review.md` | keep | drop the Data and "Writes and API" sections | drop the Screens section |
| `ops/operations.md` | keep | trim to: environments, configuration, deploy, rollback | keep |
| `decisions/`, `features/_template.md`, `guides/start-a-feature.md` | keep | keep | keep |

A frontend-only project still needs the API owner's contract. List each endpoint it calls in the feature spec's "API used" table.

## Using this template in a new project

1. Copy `AGENTS.md`, `CLAUDE.md`, and the `docs/` folder to the root of the new repository.
2. Set the project type in `AGENTS.md`, and trim the docs as the table above shows.
3. Fill in the placeholders in this order. Search for `<` to find what is left.
   - `AGENTS.md`: project name, purpose, stack, type, and commands.
   - `docs/architecture/features.md`: one row per feature.
   - `docs/domain/data-model.md`: one entry per table.
   - `docs/domain/glossary.md`: the project's own terms.
   - `docs/standards/security.md`: the permission table.
   - `docs/ops/operations.md`: environments, variables, and commands.
3. Delete any doc that does not apply, and record why in `docs/decisions/`.
4. Keep decisions `0001` to `0003` unless the project decides otherwise. They are the default rules for error codes, versions, and idempotency. A change to them is a new decision.
5. Use `docs/standards/review.md` on the first pull request, to check that the setup works.
6. Once the placeholders are filled, delete this section.
