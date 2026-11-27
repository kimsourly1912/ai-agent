# Frontend Standard

Defines how screens, data loading, actions, and states are built, so every screen behaves the same way.
Read this before you add or change a screen. Applies to fullstack and frontend-only projects. Replace every `<placeholder>` with your project's value.

## The problem this solves

Without a standard, each screen loads data in its own way, shows errors in its own way, and forgets some states (loading, empty, failed). The screen also starts to make decisions that only the server can enforce, such as who may approve something. The result looks fine in the happy case and breaks in the others.

## Mental model

A screen is a view of the server's data. The server decides what is true and what is allowed. The screen shows it, and shows every state honestly.

```text
Route
  ↓
Screen            puts the parts together
  ↓
Hook              owns the data, the actions, and the derived state
  ↓
API client        generated from the API contract
  ↓
Server

Parts (components) only display what they are given, and report what the user did.
```

| Piece | Owns | Must not |
|---|---|---|
| Screen | Layout, which parts to show for which state | Call the API directly |
| Hook (`<composable or hook>`) | Fetching, actions, and derived state for one concern | Render anything |
| Part (component) | Display, and reporting user events | Fetch data, or decide a business rule |
| Model | The shape of an API response, and which fields are required | Hold state |
| Util | A pure function | Touch the network or the screen |

## Rules

1. **The server decides. The screen shows.** Hiding or disabling a button helps the user. It is not a security check. The server checks again. See `docs/standards/security.md`.
2. **One folder per feature, with a small public face.** Only the screens are exported. Parts, hooks, models, and utils are private. Another feature uses this one only through what it exports. See `docs/architecture/features.md`.
3. **Data and actions live in hooks.** A part does not call the API. Split hooks by concern (the record, the list, the actions), and let the screen combine them.
4. **Check every API response against a model** (`<validation library>`) at the boundary. Mark each field required or optional. A bad optional field must not break the whole screen: hide it. A response that fails the check shows an error, never an empty list.
5. **Use the generated API types and client.** Do not write endpoint types by hand. If the contract is wrong or missing, wrap the gap in the model, write a comment that says why, and add an open question to the feature spec. Do not work around it silently.
6. **Every screen shows every state.** Loading (shaped like the content, so nothing jumps), empty (say why it is empty), error (say what failed, and offer a retry), no permission, and any partial or stale state. The feature spec lists them under Screens and states.
7. **Errors say something useful.** Show the server's message when it sends one, with a fallback text. Never show a raw error object or a code alone. See "Error format" in `docs/standards/api.md`.
8. **Writes are honest.** Disable the action while it runs. Show success only after the server says so. Show failure with the reason. Reload the data afterwards. On a stale edit (`409`), reload, tell the user their change did not apply, and do not overwrite. See "Editing: versions" in `docs/standards/api.md`.
9. **State the user expects to keep lives in the URL.** The tab, the page, and the filters of a list go in the query string, so Back and reload return to the same place.
10. **A failed reload must not mislead.** If a reload fails after a success, keep showing the success. If a reload fails after a save, do not present the old data as current. Treat it as unavailable and say so.
11. **Unsaved changes are visible.** Show how many there are, and block actions that would lose them or depend on them (such as approving) until they are saved or discarded.
12. **Anyone can use it.** Every control has a label. Status changes use a live region. Everything works with the keyboard. Colour is never the only signal.
13. **No secrets in the client.** See "Secrets and logging" in `docs/standards/security.md`.

## Folder layout

```text
src/features/<feature>/
  index.ts          exports the screens only
  components/       screens and parts
  composables/      hooks: data and actions, named by what they own
  models/           response checks and types
  utils/            pure functions
  tests/
```

In a fullstack project, a frontend feature and its backend feature share the name in `docs/architecture/features.md`.

## Screen checklist

Before you ask for review:

- [ ] Every state listed in the spec's Screens and states is built and has a test.
- [ ] Data comes through a hook and a model. No part calls the API.
- [ ] Each write disables while it runs, and shows its result.
- [ ] A stale edit is handled and tested.
- [ ] Unsaved changes are visible and guarded.
- [ ] The screen works on a narrow and a wide layout.
- [ ] Controls have labels, and the keyboard reaches everything.

Which tests are required: see "Frontend tests" in `docs/standards/testing.md`.

## Edge cases

- **The API contract is wrong.** Wrap it in the model, comment why, and open a question for the API owner. Remove the wrap when the contract is fixed.
- **The response shape changes.** The check fails, and the screen shows an error. It does not show an empty list that looks like "no data".
- **The user changes screen or input while a request runs.** Cancel the old request, so a slow answer cannot replace a newer one.
- **Large lists.** Page through the API. Do not load everything and filter in the browser.
- **Two people edit the same record.** The second save gets `409`. Show it, and let the user reload.
