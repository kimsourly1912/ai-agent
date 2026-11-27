# Security and Authorization

Defines who may do what, how the server decides, and how tenant boundaries are kept.
Read this before you add a route, a permission, an action that changes data, or anything that touches identity. Replace every `<placeholder>` with your project's value.

## The problem this solves

Most security bugs are not clever attacks. They are simple gaps: a route that forgets the check, a query that forgets the tenant, a button that is hidden but still works through the API. This doc makes the check explicit, so it is hard to forget.

## Mental model

- **Authentication** answers: who is this?
- **Tenant resolution** answers: which tenant is this request about?
- **Authorization** answers: may this user do this action in this tenant?

The server answers all three. The client may ask, but it never decides. A hidden button in the UI is a convenience, not a permission.

## Request flow

```text
Request arrives
        ↓
Authenticate: read the session from a cookie, check it on the server
        ↓
Resolve tenant: from the URL or session, checked against membership
        ↓
Authorize: does this user have the named permission in this tenant?
        ↓  no → 403, stop
Confirm dangerous change (only for actions that need it, see below)
        ↓  missing → refuse, ask the user to confirm
Validate input (schema)
        ↓
Service: business rules, calls repository
        ↓
Repository: query names the tenant
        ↓
Response: map to the response shape, never return raw rows
```

## Rules

1. **Default deny.** A route with no permission check is a bug. If no permission is declared, the request is refused.
2. **Permissions are named exactly.** Use a fixed name such as `<resource>.<action>`. No wildcards, no pattern matching.
3. **One permission map.** All permissions and the roles that hold them live in one file. Routes reference a permission name; they do not list roles.
4. **No role-name checks in routes or components.** Write `can('order.refund')`, not `role === 'admin'`. Roles can change; permission names are stable.
5. **Unknown permission is an internal error.** If code asks about a permission that is not in the map, fail loudly. Do not treat it as allowed or denied silently.
6. **Tenant from server context.** The tenant comes from the session and the URL, and it is checked against membership. Never accept a tenant ID from the request body alone.
7. **Platform admins do not get tenant data access by default.** If a platform role can act inside a tenant, that must be an explicit, logged permission.
8. **The UI is not a security boundary.** Hide or disable controls for users who lack the permission, and check again on the server.
9. **Ownership is a check too.** For a record that belongs to one user, check that the user owns it, not only that they are logged in.
10. **Privileged writes leave an audit event.** A write that changes access, money, or another member's data records who did it, what changed, which tenant, and when. The audit event is written in the same atomic write as the change. If the audit write fails, the change fails too. Audit records are never updated or deleted.

## Route checklist

Use this for every route that reads or changes data.

- [ ] The session is read on the server, not trusted from the client.
- [ ] The tenant is resolved from server context and checked against membership.
- [ ] A named permission is checked before any work happens.
- [ ] Every query names the tenant.
- [ ] Input is validated against a schema.
- [ ] A dangerous change asks for re-confirmation after the permission check.
- [ ] A privileged write records an audit event in the same atomic write.
- [ ] The response does not include internal columns or secrets.
- [ ] A test covers: no session, wrong tenant, missing permission, and allowed.

## Permission table

Keep one row per permission. Add the row in the same change that adds the route.

| Permission | Allowed roles | Scope | Notes |
|---|---|---|---|
| `<resource>.read` | `<roles>` | tenant | `<notes>` |
| `<resource>.update` | `<roles>` | tenant | requires current version (see `api.md`) |
| `<resource>.delete` | `<roles>` | tenant | blocked while history exists (see `data-model.md`) |

## Sessions

- Sessions are checked on the server. The cookie holds only an opaque reference to a session record.
- Cookies are `HttpOnly` and `Secure`.
- Sessions have an idle timeout and an absolute lifetime.
- Access tokens are short-lived. Refresh tokens rotate, and only a hash is stored.
- If a refresh token is used twice, treat it as theft: revoke the session family and require sign-in again.

Use your auth library's built-in features before you write your own. Check the current docs for the version you install.

## Dangerous changes

Some actions need the user to confirm again, even when already signed in. Examples: changing a password, transferring ownership, removing an owner, or exporting all data. For these, require a recent sign-in or a one-time code, and write an audit record.

## Secrets and logging

- Never commit secrets. Keep names in `.env.example` and values in the platform's secret store.
- Never log tokens, passwords, session IDs, or full personal data.
- Never store form drafts that contain passwords or secrets.
- Log the request ID and the outcome, not the request body.

## Uploads

- Check file type and size on the server.
- Store files under generated names, not user-supplied names.
- Serve files through a route that checks permission, unless the file is public by design.

## Edge cases

- **Role changed while the user is signed in.** The next permission check uses the new role. Do not cache permissions beyond one request, unless the cache is invalidated on change.
- **Tenant suspended.** Decide this explicitly. Usually, reads are allowed for the owner, and writes are refused.
- **Platform admin needs to help a tenant.** Use a time-limited, logged support grant. Do not give standing access.
