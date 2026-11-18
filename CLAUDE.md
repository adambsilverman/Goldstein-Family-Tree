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

## Where the family's data lives
The repo is public, so it holds **no family data**. Visitors without the family link see a welcome page.
- On one device: the browser's localStorage.
- Shared: a Firebase Realtime Database (REST + EventSource, no SDK) at `<db>/trees/<secret key>`.
  The family link is `…/Goldstein-Family-Tree/#join=<db host>/<key>`. Rules allow any `$key` of 16+ characters,
  so the key in the link is the password. Records: `people/<id>`, `unions/<id>` (partners[], children[]),
  `log/<id>`, `meta`, `root`. Each edit PUTs only the records it changed (last write wins per record),
  edits made offline queue in localStorage (`gft.pending`) and are sent on reconnect.
- Firebase turns arrays into objects and drops empty ones: always go through normPerson/normUnion.

## Rules
- Keep it working well on phones and for older relatives (big tap targets, the "Bigger text" option).
- Never put real family data (names, dates, photos) into a committed file.
- `parentsUncertain: true` marks the 29 children whose parent married more than once, where the
  original document didn't say which marriage; "Fill in the gaps" lets the family confirm them.
