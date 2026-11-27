# Gaps found by testing the template on nuk-cafe-admin

I filled the template in `AGENTS.md` with values from the real repo, and I compared its rules with the template's docs. This file lists what the real repo has that the template lacks, and what the template says that the real repo does not support.

## Rules the real repo has that the template lacks

1. **Module boundaries are enforced by lint.** The real repo uses `no-restricted-imports` in `eslint.config.mjs`. The template only says "enforce with a lint rule or a test." It should show a concrete example.
2. **Audit events in the same atomic write.** Every privileged write adds an audit event in the same batch. The template's data model mentions audit events only in the glossary. It needs a rule for this.
3. **Dangerous changes need a confirmation step.** The real repo calls `requireConfirmation` after the permission check for payment settings, admin access, and owners. The template's security doc has a general section, but it does not say where in the request flow the check goes.
4. **Strict input validation with size limits.** The real repo validates params, query, and body with strict schemas, and bounds their size. The template's API doc says "validate input," without saying strict or bounded.
5. **Surfaces.** The real repo separates public, shop, counter, and admin routes. The template has no concept of surfaces, but a larger project needs one.
6. **Explicit "race and replay" test case.** The real repo's route checklist requires a test for the race case (two requests at once) and the replay case (the same request sent twice). The template's testing doc mentions both, but not as one required case per route.

## Template rules the real repo does not follow directly

1. **"Every query on a tenant-owned table names the tenant."** The real repo matches this, but its tenant is a Better Auth organization called a cafe, not a generic tenant. The template's word "tenant" needs a note about mapping.
2. **Archive vs. delete.** The real repo follows this closely, but it adds a `deleteBlocker` field so the UI can explain why a delete is disabled. The template does not mention this.
3. **Single permission map.** The real repo keeps permissions in `server/features/identity/identity.permissions.ts`. The template says "one permission map," but it does not name a file, so the rule is hard to check.

## What the real repo does that is not in the template at all

- Workflow automation for dependency upgrades (`.claude/upgrade/`). This is useful, but it is specific to that project.
- Specific guidance for browser debugging and end-to-end tests.
- A decision log with numbered decisions (for example D135, D246, and D259). The template has `docs/decisions/` but does not say how to number or index them.

## Verdict

The template's core rules (tenant scope, versions, default deny, one owner per table, idempotency for money) match the real repo, so they are sound. The main gaps are the concrete tools that enforce them: lint rules, audit events in the same write, and the confirmation step. These should be added in the next review.
