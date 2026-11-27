# Decisions

Records important architecture and product decisions, and the reasons for them. Read this when you want to change something that looks wrong, because it may have been decided on purpose.

## When to write a decision

Write one when a choice:

- affects more than one feature, or the database schema,
- had real alternatives, and someone may later ask "why not X?",
- is hard or expensive to reverse.

Do not write one for small choices, such as a variable name or a helper function.

## Format

File name: `NNNN-short-title.md`, with a four-digit number that is never reused. Use lowercase words separated by hyphens.

```text
# NNNN: Title

Status: proposed | accepted | superseded by NNNN
Date: YYYY-MM-DD

## Context
What problem or constraint led to this decision?

## Decision
What we decided, in one or two sentences.

## Alternatives
- Option A: why not.
- Option B: why not.

## Consequences
What becomes easier, what becomes harder, and what must change elsewhere.
```

## Rules

1. A decision is never edited to change its meaning. To change a decision, write a new one, and mark the old one `superseded by NNNN`.
2. Each accepted decision must be reflected in the doc that owns the rule. The decision says why, and the owning doc says what.
3. Changing a decision is an "ask before you" item in `AGENTS.md`.

## Index

| Number | Title | Status |
|---|---|---|
| 0001 | Error codes for rule violations (400, 422, 409) | accepted |
| 0002 | Send the version in the If-Match header | accepted |
| 0003 | Idempotency keys for money, points, and create requests | accepted |
