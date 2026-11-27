# AGENTS.md (filled in for nuk-cafe-admin, as a test of the template)

This file is a test. It fills the template's placeholders with values from `kimsourly1912/nuk-cafe-admin`, and it maps each section to the doc that the real repo already has. The gaps it shows are listed in `GAPS.md`.

## Project

- Name: NUK Cafe
- Purpose: one Nuxt full-stack app for customer ordering, cafe admin, the counter, and the platform console.
- Stack: Nuxt 4 and Nitro, Better Auth, NuxtHub with Drizzle, local SQLite and Cloudflare D1, R2, and KV, Nuxt UI and Tailwind, pnpm, Valibot, Vitest.

## Commands

From `package.json` scripts:

- Install: `pnpm install`
- Dev server: `pnpm dev`
- Unit and server tests: `pnpm test:unit`
- Changed tests only: `pnpm test:changed`
- End-to-end tests: `pnpm test:e2e`
- Typecheck: `pnpm typecheck`
- Lint: `pnpm lint`
- Migrations, staging: `pnpm db:migrate:staging`
- Migrations, production: `pnpm db:migrate:production`

## Where to find things

The real repo has these docs. This table points to them, so the template's file names are replaced with the real paths.

| Question | Read in nuk-cafe-admin |
|---|---|
| How the system fits together | `docs/server/architecture.md` (Shape of the system) |
| Which module owns this code | `docs/server/architecture.md` (Features and Rules) |
| Tables, relations, constraints | `docs/server/data-model.md` |
| Meaning of domain terms | not present (gap) |
| Who may do what, tenant boundaries | `docs/server/security.md` |
| API format, status codes, errors | `docs/server/architecture.md` (Conventions, Errors) |
| Which tests are required | `docs/guides/development.md` |
| Why a decision was made | `docs/decisions.md` |
| What a feature must do | `docs/feature-standard.md` and `docs/plans/` |

## Essential rules

- A Better Auth organization is a cafe, which is the tenant. Tenant-owned rows carry `tenant_id`, and every query names the tenant.
- New tables and queries need a test that uses two cafes and checks isolation.
- Permissions are checked on the server. Platform superadmins do not gain access to cafe data.
- Every edit sends the version it read, and a stale edit gets a `409`.
- Server writes run in one atomic `db.batch`, with guards and the audit event in that batch.
- Money, points, and vouchers need idempotency keys.
- Orders and ledgers are never deleted.
- Cross-feature imports use the public `index.ts` only.
- Use `apiFetch` for the API, and `useTenantPath()` for links.

## How to work

1. Classify the change and read the matching doc in the table above.
2. Put the code in the owning feature under `app/features/<feature>/` or `server/features/<feature>/`.
3. Keep server routes thin. SQL goes in repositories, and business rules go in services.
4. Read the existing code in that feature and follow it.
5. Add tests, and run `pnpm lint`, `pnpm typecheck`, and `pnpm test:unit` before you report done.
6. Record decisions in `docs/decisions.md` when the architecture changes.
7. Do not refactor unrelated code.

## Ask before you

- Change the schema, or run a migration that deletes or rewrites data.
- Change a decision in `docs/decisions.md`.
- Change payment settings, admin access, or owners. These need `requireConfirmation` (D246).
- Add a dependency, or change deployment or environment configuration.
- Delete data, files, or branches.
