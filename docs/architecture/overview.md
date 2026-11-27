# Architecture Overview

Explains the main parts of the system, how they connect, and where data lives.
Read this first when you need to understand how a request moves through the system. Replace every `<placeholder>` with your project's value.

## The problem this solves

Without a shared picture, each change is made in isolation. An agent may put logic in the wrong place, call an external service directly from a UI component, or store state where it is lost on restart. This doc gives the big picture so detailed changes fit into it.

## Mental model

The system has three kinds of parts:

1. **Clients:** what people and other systems use (web app, mobile app, admin console, third-party services).
2. **The server:** the one place where rules are enforced. It owns permissions, business logic, and data integrity.
3. **Storage and external services:** the database, file storage, cache, email, payments, and so on. The server talks to them. Clients do not.

The server owns every rule. A client asks; the server decides.

## Components

| Component | Role | Owns |
|---|---|---|
| `<client app>` | Shows data, collects input | Nothing persistent. Sends requests. |
| `<server>` | Enforces rules, serves the API | All business rules and permissions |
| `<database>` | Stores records | All persistent records |
| `<file storage>` | Stores uploaded files | Files, referenced by ID |
| `<cache>` | Speeds up repeated reads | Temporary copies only. Never the source of truth. |
| `<email or notifications>` | Sends messages | Message delivery status |
| `<external service>` | Payments, identity, and so on | Its own data. Store only the reference we need. |

## Request flow

```text
Client
  ↓ HTTPS request + session cookie
Server: authenticate and resolve tenant
  ↓
Server: check permission
  ↓
Server: validate input
  ↓
Module service: apply business rules
  ↓
Module repository: query database (scoped to tenant)
  ↓
Database
  ↓
Response back to client
```

The details of each step are in `security.md`, `api.md`, and `modules.md`.

## Background work

Some work should not block a request: sending email, retrying a failed payment check, cleaning up old files.

```text
Request changes data
  ↓
Write an outbox or job record in the same atomic write
  ↓
Background worker picks it up
  ↓
Worker performs the side effect (send email, call external service)
  ↓
Worker records success or failure
```

Rule: a side effect is written in the same transaction as the change that caused it. A background job must never depend on a request staying alive.

## Where data lives

- **Source of truth:** the database. Anything not in the database can be lost.
- **Cache:** safe to delete at any time. Never store a permission or a price only in a cache.
- **Files:** in file storage, with a reference in the database.
- **Secrets:** in the platform's secret store. Never in the repository.

## Environments

| Environment | Purpose | Data |
|---|---|---|
| Local | Development on one machine | Fake or seeded data |
| Staging | Test before release | Copy or fake data, never real customer data |
| Production | Real users | Real data |

Changes move from local to staging to production. Production changes are made deliberately, not automatically.

## Edge cases

- **Service is down.** Decide what the user sees: a retry message, a cached value, or a refusal. Write the decision in the feature spec.
- **Clock differences.** Store and compare times in UTC.
- **Two servers at once.** Assume any request can run on any instance. Do not keep per-user state in memory between requests.
- **External service changes its API.** Keep the integration in one place, so only one module changes.
