# Glossary

Defines the words this project uses with a specific meaning. Read this when a term is unclear, or before you name a new table, field, or route.
Replace the placeholders with your project's terms. Add a term only when people or agents get it wrong without a definition.

## Rule for names

Use the exact term from this glossary in code, docs, and the UI. Do not invent a synonym. If two terms mean the same thing, pick one and mark the other as "do not use."

## Terms

| Term | Meaning in this project | Do not confuse with | Do not use |
|---|---|---|---|
| Tenant | One customer organization that uses the system. All its data is isolated from other tenants. | A user account, or a branch inside a tenant | `company`, `account` |
| Member | A user who belongs to a tenant, with a role | A user who has an account but no tenant | `staff`, `employee` (unless your domain uses them) |
| User | A person who can sign in. A user may belong to several tenants. | A member, who belongs to one tenant | `account` |
| Owner | A member with full control of a tenant | Platform admin, who manages the whole system | `admin` (ambiguous) |
| Platform admin | A staff member of the service itself. Not part of any tenant. | Owner of a tenant | `superuser` |
| Module | One feature area with one owner and a public service | A folder, or a file | `feature` (in code), `service` |
| Version | A counter that changes on every edit, used to detect stale updates | A timestamp, or the software release number | `revision` (choose one of the two) |
| Archive | Hide a record from lists, keep its history, and allow restore | Delete | `soft delete`, `trash` |
| Delete | Remove a record permanently. Only allowed when no history refers to it. | Archive | `remove` (choose one) |
| History | Data that must stay explainable later, such as orders, payments, and audit events | Ordinary editable data | `logs` (logs are a separate thing) |
| Audit event | A permanent record of who did what and when | A debug log | `activity` |
| `<Domain term>` | `<definition in one sentence>` | `<similar term>` | `<synonym>` |

## Common terms for agents

- **Idempotency key:** a unique value the client sends with a money or points action, so the server runs it only once.
- **Outbox:** a record written in the same transaction as a change, which a background worker later uses to send an email or call an external service.
- **Scope:** the tenant (and sometimes the branch or team) that a query is limited to. Every tenant query names its scope.

## Edge cases

- **A term changes meaning over time.** Update this file in the same change, and note the change in `docs/decisions/`.
- **A term is used in the UI differently from the code.** Decide which one is correct, then align the other. Write the decision here.
