# Operations

Explains how the system is configured, deployed, migrated, backed up, and recovered. Read this before you change configuration, deploy, run a migration, or respond to an incident.
Replace every `<placeholder>` with your project's value.

## The problem this solves

Most production incidents are not code bugs. They are a wrong setting, a migration run in the wrong order, or a deploy with no way back. This doc makes those steps explicit and repeatable.

## Mental model

- **Code** moves through environments in a fixed order: local, then staging, then production.
- **Configuration** (environment variables and secrets) is separate from code. Names are in the repo. Values are in the platform's secret store.
- **Data** changes only through migrations. A migration is a versioned, ordered change to the schema.
- **Production changes are deliberate.** Nothing reaches production automatically unless the project decided so in `docs/decisions/`.

## Environments

| Environment | Purpose | Who may deploy | Data |
|---|---|---|---|
| Local | Development on one machine | Each developer | Fake or seeded data |
| Staging | Test a change before release | `<role>` | Fake or copied data, never real customer data |
| Production | Real users | `<role>`, with approval | Real data |

## Configuration

- Every variable has a name, a purpose, and a default in `.env.example`.
- Values are never committed. Staging and production values live in `<secret store>`.
- Adding or renaming a variable is an "ask before you" item in `AGENTS.md`.

| Variable | Purpose | Required in |
|---|---|---|
| `<NAME>` | `<purpose>` | `<environments>` |

## Migrations

Run in this order:

1. Write the migration. It must be backward compatible with the code that is running now.
2. Test it on a copy of staging data, or on fake data of similar size.
3. Run it on staging. Check the result.
4. Deploy the code that uses the new schema.
5. Run the migration on production, then deploy the code.

Rules:

- Never change or delete a migration that has already run in any environment. Add a new one.
- A migration that deletes or rewrites data needs a backup first and an approval, both recorded in the change.
- Adding a required column in one step breaks running code. Add it as optional first, fill it, then make it required in a later migration.

Command: `<migration command>`

## Deploy

1. Confirm that all checks pass: typecheck, lint, and tests (see `docs/standards/testing.md`).
2. Deploy to staging, and run a smoke test: sign in, read one record, make one change.
3. Deploy to production with the same steps.
4. Watch errors and the main health check for `<time window>` after the deploy.

Command: `<deploy command>`

## Rollback

- Code: redeploy the previous version. `<command or platform action>`
- Schema: do not roll back a migration that has deleted data. Write a new forward migration to fix the problem.
- Decide in advance what counts as "roll back now." Example: error rate above `<threshold>` for `<minutes>`.

## Backups

- Backup schedule: `<schedule>`
- Where backups are stored: `<location>`
- How long they are kept: `<retention>`
- The last restore test: `<date>`. A backup that has never been restored is not proven.

## Incidents

1. Stop the bleeding first. Roll back or disable the feature.
2. Tell the people who need to know.
3. Fix the cause.
4. Write what happened, what was done, and what will prevent it. Record any new rule in `docs/decisions/`.

## Edge cases

- **A migration fails halfway.** Check whether it is atomic. If not, fix the data by hand under an approval, and record what was done.
- **Staging data is too small to show a problem.** Test on a larger copy before a risky migration.
- **Secrets leak into logs or a commit.** Rotate the secret first, then remove it from history. Do not wait.
- **Time zones.** Run scheduled jobs and compare times in UTC.
