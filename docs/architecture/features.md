# Feature Map

Defines which feature owns which code, and which features may depend on which.
Read this before you add or move code. Replace every `<placeholder>` with your project's value.

## The problem this solves

Without clear ownership, code drifts. A feature reads another feature's tables directly, a route contains business rules, or a helper gets copied into three places. Later changes then break code far away from the change.

## Mental model

A feature is one feature area with one owner. It has a single public door: its service. Other features call that door. They never reach inside.

```text
Feature A                         Feature B
  route                            route
    ↓                                ↓
  service  ───── public call ───→  service  (public door)
    ↓                                ↓
  repository (private)             repository (private)
    ↓                                ↓
  database: A's tables             database: B's tables
```

## Layer flow inside one feature

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

## Feature table

Each feature must have one row. A feature is not allowed to exist without a row.

| Feature | Owns (tables / concepts) | Public API (service entry) | May depend on | Must not depend on |
|---|---|---|---|---|
| `<feature>` | `<tables>` | `<features/<feature>/index.ts>` | `<features>` | `<features>` |

## Rules

1. **One owner per table.** Only the owning feature's repository reads or writes its tables.
2. **Cross-feature calls go through the public service only.** Feature A calls `B.service` methods. It never imports `B.repository` and never joins `B`'s tables.
   - Exception: a repository may **read** a few named columns of another feature's table in a join (for example, a user's name for an audit log). Those columns must be listed in the shared table definitions, not written ad hoc.
   - Write statements that span features stay in one atomic write. The owner of the action asks the other feature for its statements through its public service, then runs them together.
3. **Routes hold no business rules.** A route parses the request, calls a service, and maps the result to a response.
4. **Repositories hold no business rules.** They run queries and return data. Authorization and decisions live in the service.
5. **Shared code is for stable concepts only.** Examples: pagination, error types, request context. Put code in `shared/` only after the same pattern appears in at least two features. Do not predict a shared abstraction.
6. **No circular dependencies.** If A needs B and B needs A, the shared concept belongs in a third place, or one feature owns it.

## Folder layout

```text
src/
  features/
    <feature>/
      <feature>.routes.ts       # route: transport only
      <feature>.service.ts      # business rules, public API
      <feature>.repository.ts   # data access, private to this feature
      <feature>.schema.ts       # input validation and types
      index.ts                 # the only file other features may import
  shared/                      # only stable, reused concepts
```

## Adding a feature

1. Give it a name and list the tables it owns.
2. Write its public service in `index.ts`.
3. Add a row to the feature table above.
4. Confirm that no other feature's table is touched by this feature's repository.

## Enforcement

Enforce rule 2 with a lint rule. The example below blocks one feature from importing another feature's repository. Add one block per pair of features that must not depend on each other. Check the current ESLint docs for the syntax your version uses.

```js
// eslint.config.mjs (flat config, illustrative)
export default [
  {
    // Files in the orders feature
    files: ['src/features/orders/**'],
    rules: {
      'no-restricted-imports': ['error', {
        patterns: [{
          group: ['**/features/payments/**/*.repository*'],
          message: 'Import the payments feature through its index.ts, not its repository.',
        }],
      }],
    },
  },
];
```

With many features, one block per pair becomes hard to keep correct. At that point, generate the blocks from the feature table above, or use a dependency-check tool.

## Edge cases

- **Feature needs data from another feature.** Call that feature's service method. Do not join its tables.
- **A feature's table is referenced by another feature's table.** This is a foreign key. The owning feature decides the delete behavior, and it is documented in `docs/domain/data-model.md`.
