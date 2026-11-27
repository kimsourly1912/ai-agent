# Testing Standard

Defines which tests must exist, what each test proves, and how to check that a test really protects a rule.
Read this before you finish any change. Replace every `<placeholder>` with your project's value.

## The problem this solves

A rule that is only written in a doc is a wish. People and agents forget it, and nothing stops a change that breaks it. A test turns a rule into a check that runs on every change. This doc says which rules must have tests, and how to prove the test works.

## Mental model

A test answers one question: **if someone breaks this rule, will something fail?**

- A passing test that would still pass after the rule is broken proves nothing.
- A test that fails when the rule is removed is the only kind that protects you.

So for every protective rule, you do two things: write the test, then **remove the guard and confirm the test fails**. Then put the guard back.

## Test levels

| Level | What it checks | Speed | Use for |
|---|---|---|---|
| Unit | One function, with no database | fast | Pure business rules, calculations, parsing |
| Integration | A service or repository against a real database | medium | Queries, constraints, atomic writes, tenant scope |
| End to end | A request through the whole app | slow | Login, a full user flow, a route's permission check |

Use the lowest level that proves the rule. A rule about a database constraint needs an integration test. A rule about a price calculation needs only a unit test.

## Required tests

Every project must have these tests, or record in `docs/decisions/` why one is skipped.

1. **Permission test.** For each protected route, test: no session, wrong tenant, missing permission, and allowed.
2. **Tenant isolation test.** For each tenant-owned table, create data in two tenants. Confirm that tenant A cannot read, update, or delete tenant B's data.
3. **Concurrency test.** For each editable record, send an update with an old version. Confirm the response is `409` and the data is unchanged.
4. **Constraint test.** For each unique or check constraint, try to break it. Confirm the database refuses the write.
5. **Atomic write test.** For each write that touches several rows, make one step fail. Confirm that no row changed.
6. **Idempotency test.** For each money or points action, send the same key twice. Confirm the action ran once.
7. **Feature boundary check.** Confirm that no feature reads another feature's repository or tables. Enforce this with the lint rule in `docs/architecture/features.md`, and run lint in CI so a broken boundary fails the build.
8. **Audit test.** For each privileged write, confirm that an audit event is recorded. Then make the audit write fail and confirm that the change does not apply.

## The guard check

For any test that protects a guard (a permission check, a version check, a tenant filter, an idempotency key):

1. Write the test.
2. Remove the guard from the code.
3. Run the test. It must **fail**.
4. Put the guard back. The test must **pass**.

If the test still passes after the guard is removed, the test is wrong. Fix it before you merge.

## Test data

- Create the data inside each test. Do not depend on rows left by another test.
- Use two tenants in any test that touches tenant data.
- Use fixed, readable names, such as `tenant-a` and `tenant-b`.
- Never use real customer data or real secrets in tests.

## Commands

- Run unit and integration tests on every change: `<command>`
- Run end-to-end tests before merging shared code: `<command>`
- Run lint and typecheck: `<command>`

A change is not done until all of these pass, or the blocked check is reported with the reason.

## Reporting

When you report work as done, say what you ran and what passed. If a check could not run, say which one and why. Do not claim a check passed when it did not run.

## Edge cases

- **Test database differs from production.** If production uses a different database engine, some tests may pass locally and fail in production. Run the integration tests against an engine as close to production as you can, and note any gap.
- **Mocked services hide real behavior.** A test that mocks the database cannot prove a query is correct. Use a real database for query tests.
- **Flaky tests.** A test that sometimes fails is a bug. Fix it or remove it. Do not retry until it passes.
- **Slow tests.** Keep most tests at the unit and integration level. Use end-to-end tests only for flows that cross many parts.
- **New guards need new tests.** A pull request that adds a permission, lock, or version check must include the test and the guard check.
