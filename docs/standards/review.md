# Review Checklist

Use this checklist when you review a change, or before you ask someone to review yours. Each item points to the rule it checks. If an item does not apply, skip it and say why.

## Scope

- [ ] The change does only what the task asked. Unrelated refactors are in a separate change.
- [ ] The change is in the owning feature (see `docs/architecture/features.md`).
- [ ] No feature reads another feature's repository or tables.

## Security

- [ ] Every new or changed route checks the permission on the server (see `docs/standards/security.md`).
- [ ] Every query on tenant data names the tenant.
- [ ] A test covers: no session, wrong tenant, missing permission, and allowed.
- [ ] Identity and tenant come from server context, not from the request body.
- [ ] No secrets, tokens, or personal data appear in code, logs, or responses.

## Data (backend)

- [ ] New tables and columns follow the conventions in `docs/domain/data-model.md`.
- [ ] Every relation has an explicit delete rule.
- [ ] Unique and check rules exist in the database, not only in code.
- [ ] Business records are archived. Nothing with history is deleted.

## Writes and API (backend)

- [ ] Editable records check the version (see `docs/standards/api.md`). A stale edit returns `409`.
- [ ] Writes that touch several rows run in one atomic write.
- [ ] Privileged writes record an audit event in the same write.
- [ ] Money, points, and vouchers require an `Idempotency-Key` (see `docs/decisions/0003-idempotency-key-scope.md`).
- [ ] Errors use the standard shape and codes. Rule violations use `422`; state conflicts use `409`.
- [ ] The response is mapped to its shape. No raw rows are returned.

## Screens (frontend)

- [ ] Every state in the feature spec's Screens and states is built (loading, empty, error with retry, no permission, partial).
- [ ] Data goes through a hook and a model. No component calls the API (see `docs/standards/frontend.md`).
- [ ] A response that fails its model shows an error, not an empty screen.
- [ ] Each write disables while it runs, shows its result, and handles a stale edit (`409`).
- [ ] Hiding or disabling a control is not the only protection. The server checks the same rule.
- [ ] Unsaved changes are visible and guarded. Controls have labels, and the keyboard reaches everything.

## Tests

- [ ] Each new guard has a test, and the test fails when the guard is removed (see `docs/standards/testing.md`).
- [ ] Typecheck, lint, and tests were run, and the results are reported.

## Docs

- [ ] A changed rule is updated in the doc that owns it, not copied into another doc.
- [ ] A new decision is recorded in `docs/decisions/`.
- [ ] A changed term is updated in `docs/domain/glossary.md`.
- [ ] A change that alters behavior updates the feature spec in the same pull request: Resume here, Observed behavior or Decided rules, and the Change log. A spec that falls behind the code is worse than none.

## Business and authorization rules

- [ ] Every business or authorization rule in this change has a source: a feature spec's "Decided rules" table, a decision record, or the owner's message. The spec quotes the owner's exact words, so the source survives a broken link.
- [ ] A rule with no source is listed as an open question in the feature spec. Its behavior is refused or disabled, not guessed.
- [ ] No rule is "assumed" because the author or the agent inferred it from the code.
