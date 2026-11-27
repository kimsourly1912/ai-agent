# Module Map

Defines which module owns which code, and which modules may depend on which.
Read this before you add or move code. Replace every `<placeholder>` with your project's value.

## The problem this solves

Without clear ownership, code drifts. A feature reads another feature's tables directly, a route contains business rules, or a helper gets copied into three places. Later changes then break code far away from the change.

## Mental model

A module is one feature area with one owner. It has a single public door: its service. Other modules call that door. They never reach inside.

```text
Module A                         Module B
  route                            route
    ↓                                ↓
  service  ───── public call ───→  service  (public door)
    ↓                                ↓
  repository (private)             repository (private)
    ↓                                ↓
  database: A's tables             database: B's tables
```

## Layer flow inside one module

```text
Route (transport)
   ↓  parses input, returns response
Service (business rules)
   ↓  decides what happens, checks authorization
Repository (data access)
   ↓  runs queries, scoped by tenant/user
Database
```

Dependencies point one way only: route → service → repository → database. Nothing lower may import from something higher.

## Module table

Each module must have one row. A module is not allowed to exist without a row.

| Module | Owns (tables / concepts) | Public API (service entry) | May depend on | Must not depend on |
|---|---|---|---|---|
| `<module>` | `<tables>` | `<modules/<module>/index.ts>` | `<modules>` | `<modules>` |

## Rules

1. **One owner per table.** Only the owning module's repository reads or writes its tables.
2. **Cross-module calls go through the public service only.** Module A calls `B.service` methods. It never imports `B.repository` and never joins `B`'s tables.
3. **Routes hold no business rules.** A route parses the request, calls a service, and maps the result to a response.
4. **Repositories hold no business rules.** They run queries and return data. Authorization and decisions live in the service.
5. **Shared code is for stable concepts only.** Examples: pagination, error types, request context. Put code in `shared/` only after the same pattern appears in at least two modules. Do not predict a shared abstraction.
6. **No circular dependencies.** If A needs B and B needs A, the shared concept belongs in a third place, or one module owns it.

## Folder layout

```text
src/
  modules/
    <module>/
      <module>.routes.ts       # route: transport only
      <module>.service.ts      # business rules, public API
      <module>.repository.ts   # data access, private to this module
      <module>.schema.ts       # input validation and types
      index.ts                 # the only file other modules may import
  shared/                      # only stable, reused concepts
```

## Adding a module

1. Give it a name and list the tables it owns.
2. Write its public service in `index.ts`.
3. Add a row to the module table above.
4. Confirm that no other module's table is touched by this module's repository.

## Enforcement

Rules that exist only in this doc can be broken without anyone noticing. Enforce rules 1 and 2 with a lint rule or a test. The enforcement method is defined in `docs/standards/testing.md`, once that file exists.

## Edge cases

- **Module needs data from another module.** Call that module's service method. Do not join its tables.
- **Two modules need the same logic.** First ask whether one owns it. If neither owns it, it is a shared concept. Move it to `shared/` only if it is stable.
- **A route needs to call two modules.** Allowed. The route coordinates calls, but a business decision that spans both modules belongs in one service, not in the route.
- **A module's table is referenced by another module's table.** This is a foreign key. The owning module decides the delete behavior, and it is documented in `docs/domain/data-model.md`.
