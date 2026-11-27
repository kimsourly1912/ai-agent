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
- `standards/testing.md`: which tests are required, and how to prove a test protects a rule.
- `standards/review.md`: what a reviewer checks before a change is merged.

## Decisions and features

- `decisions/README.md`: when to record a decision, how to number it, and the index of decisions.
- `features/_template.md`: copy this to start a new feature spec.

## Operations

- `ops/operations.md`: environments, configuration, migrations, deploy, rollback, and backups.

## Using this template in a new project

1. Copy `AGENTS.md`, `CLAUDE.md`, and the `docs/` folder to the root of the new repository.
2. Fill in the placeholders in this order. Search for `<` to find what is left.
   - `AGENTS.md`: project name, purpose, stack, and commands.
   - `docs/architecture/features.md`: one row per feature.
   - `docs/domain/data-model.md`: one entry per table.
   - `docs/domain/glossary.md`: the project's own terms.
   - `docs/standards/security.md`: the permission table.
   - `docs/ops/operations.md`: environments, variables, and commands.
3. Delete any doc that does not apply, and record why in `docs/decisions/`.
4. Keep decisions `0001` to `0003` unless the project decides otherwise. They are the default rules for error codes, versions, and idempotency. A change to them is a new decision.
5. Use `docs/standards/review.md` on the first pull request, to check that the setup works.
6. Once the placeholders are filled, delete this section.
