# henryandirma.com

Single-page site for the Henry & Irma White Family Reunion 2026, served via GitHub Pages at henryandirma.com (see `CNAME`).

## Layout

- `index.html` is the whole site: CSS in `<style>`, markup, and one inline `<script type="module">` (starts near line 1047). There is no build step, bundler, package manager, or test suite.
- `CNAME` holds the custom domain. Do not change or delete it.
- Deploy = push to `main`; GitHub Pages publishes it.

## Page sections (in order)

`#reunion` (info), `#rsvp`, `#lodging`, `#maps`, `#events`, `#polls`, `#stories`, `#bulletin`.

## Backend: Firebase Realtime Database

The script imports the Firebase 10.12.0 modular SDK from `gstatic.com` and talks directly to the Realtime Database. Paths used:

| Path | Purpose |
|---|---|
| `rsvps` | RSVP entries shown on the "wall" |
| `banner` | Site banner text (admin-editable) |
| `poll_protein`, `poll_spice`, `poll_writein`, `poll_playlist` | Poll votes/submissions |
| `interest_paterson`, `interest_hamilton`, `interest_cemetery` | Event interest counts |
| `pending_posts` | Bulletin board posts awaiting approval |
| `approved_posts` | Approved bulletin board posts |

Data is read with `onValue` listeners that re-render on change. Records are rebuilt as `{ ...child.val(), firebaseKey: child.key }`.

## Conventions and gotchas

- **Use `firebaseKey`, not `id`, to identify a record** when deleting/approving/rejecting. Delete paths are built as `'<path>/' + firebaseKey`.
- The script is a module, so functions are not global. Anything called from an inline `onclick="..."` must be exported at the bottom with `window.fnName = fnName` (see the block near line 1493).
- Rendering is done by building HTML strings (`chips()`, `renderWall()`, `renderBoard()`, `renderPending()`). User-entered text such as names and posts is concatenated into those strings, so be careful about escaping when touching them.
- Bulletin posts go to `pending_posts` first; an admin approves them into `approved_posts` or rejects (deletes) them.

## Admin mode (security caveat)

- `checkAdmin()` compares the entered password to the `ADMIN_PW` constant in `index.html`. It unlocks delete buttons, the pending-posts panel, and the banner editor.
- This is client-side only: the password and Firebase config are visible to anyone who views source, and `isAdmin` checks do not protect the database. Real protection would need Firebase security rules and/or Firebase Auth. Do not treat admin mode as secure, and do not copy the password into other files or commit messages.

## Color scheme

Warm earth tones. Primary colors: deep brown `#3D2B1F`, sage green `#6B8F6E`, gold `#C9974C`. Do not change the visual style.

## Related artifacts (not in this repo)

A family tree poster and a family directory PDF exist separately as print artifacts. They are not part of this repo.

## Do not

- Do not add external JS libraries or frameworks. The one existing exception is the Firebase 10.12.0 SDK loaded from `gstatic.com`.
- Do not add a build step.
- Keep everything in `index.html`.

## Working on it

- Preview by opening `index.html` in a browser (or any static server). It uses the live production Firebase database, so test actions (RSVPs, votes, deletes) affect real data.
- Commit author for this repo: Roberta Glaser <rglaser2@gmail.com>.
