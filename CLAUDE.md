# Goldstein-Family-Tree

Interactive family tree of the descendants of Shmuel Yankel Goldstein (Kavalerisky) & Elka Raisel Golden.
Repo: github.com/adambsilverman/Goldstein-Family-Tree → https://adambsilverman.github.io/Goldstein-Family-Tree/
Lives at `~/Documents/GitHub Sync/Goldstein-Family-Tree` (see `../CLAUDE.md` for syncing).

## Files
- `index.html`: the whole app (HTML + CSS + JS in one file). Edit it directly.
- `qrcode.js`: QR code generator (MIT, Kazuhiko Arase), same file as SilverApps/assets/qrcode.js.
- `private/`: **never committed** (.gitignore). `goldstein-seed.js` is the starting data from
  `Goldstein_Family_Tree.docx`, loaded only when the page is opened from this Mac and the browser has no
  tree yet. `goldstein-tree.json` is the same data as a backup file (☰ → Open a backup).

## Update files (changing the live tree without overwriting relatives' edits)
Menu → "Apply an update file…" merges a `{type:"gft-update", title, ops:[…]}` file: `set` (person field, applied only if it
still holds `from`), `addPerson`, `addUnion`, `setPartners`, `moveChild` (only if the child is still in `from`), `delUnion`
(only if childless), `meta`. Skipped items are listed; the whole update is one undo step. Build one by diffing the data that
was uploaded against the corrected data. Keep update files in `private/` (they contain family data).
- `private/keynote-update-2026-11-18.json`: details from `~/Documents/Family/FAMILY TREE/Family Tree 4:17:08.key`
  (2008–09 chart: years, middle names, surnames) plus Adam's answers to the conflicts (Nov 18 2026).

## Where the family's data lives
The repo is public, so it holds **no family data**. Visitors without the family link see a welcome page.
- On one device: the browser's localStorage.
- Shared: a Firebase Realtime Database (REST + EventSource, no SDK) at `<db>/trees/<secret key>`.
  The family link is `…/Goldstein-Family-Tree/#join=<db host>/<key>`. Rules allow any `$key` of 16+ characters,
  so the key in the link is the password. Records: `people/<id>`, `unions/<id>` (partners[], children[]),
  `log/<id>`, `meta`, `root`. Each edit PUTs only the records it changed (last write wins per record),
  edits made offline queue in localStorage (`gft.pending`) and are sent on reconnect.
- Photos: `person.photo` is a 320 px square crop (data URL) kept in the tree. The full-size original
  (max 1600 px) is stored apart so the tree stays light: shared at `<db>/trees/<key>_photos/<id>` = {v, data},
  locally in IndexedDB `gft-photos`; `person.photoV` is its version. Backups (.json) hold only the crops.
- Attachments: `person.files = {id: {name, title, type, size, t, by}}` in the tree; the file itself (max 7 MB, big
  photos shrunk to 2400 px) at `<db>/trees/<key>_files/<id>` = {name, type, data}, cached in IndexedDB. Uploads that
  can't go out yet wait in IndexedDB (`upq:<id>`), not localStorage. Backups (.json) hold only the list, not the files.
- Firebase turns arrays into objects and drops empty ones: always go through normPerson/normUnion.

## Rules
- Keep it working well on phones and for older relatives (big tap targets, the "Bigger text" option).
- Never put real family data (names, dates, photos) into a committed file.
- `parentsUncertain: true` marks the 29 children whose parent married more than once, where the
  original document didn't say which marriage; "Fill in the gaps" lets the family confirm them.
