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
