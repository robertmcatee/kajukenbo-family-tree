# Kajukenbo Family Tree — single-page app (SQLite backend)

**Open `index.html` in any browser.** That's the whole app: one page, no server, no install.
It runs a real SQLite database in the browser (sql.js WASM from a CDN, so it needs
internet the first time it loads). If the CDN is unreachable it falls back to the
built-in seed data and tells you so in the status bar.

Published at **https://robertmcatee.net/kajukenbo-family-tree/** (GitHub Pages repo
`robertmcatee/kajukenbo-family-tree`). Transcribed in full from the 2024 Kajukenbo
Family Tree poster (Nov 2024 edition): **10,285 entries, 10,076 verified**, one root,
13 generations, 631 "also trained under" cross-links.

## What it does
- **Family-tree view** of teacher → student lineage. Every node has a **− / + show-hide
  button** that collapses or expands everyone under that person. Header controls:
  *Expand all*, *Hide all branches*, and *Show to depth…*. The tree opens at depth 2.
- **Search** (sidebar): type any part of a name/branch/school — matches highlight,
  their ancestors auto-expand, click a result to jump to it.
- **Details panel**: teacher, students, also-trained-under links, and the full line
  back to the root.
- **Add / edit / delete** people in the sidebar form. Deleting a person moves their
  students up to that person's teacher.
- **Import .db** replaces the current database with a file. **Import CSV / Export CSV**
  use the columns in `persons_template.csv` (`teacher_name` must match an existing
  name, or a name in an earlier row of the same file).

## How edits work (read this before editing the published site)
GitHub Pages is static hosting — the site **cannot save changes back to GitHub**.
What actually happens:

1. Your edits auto-save to **your own browser's local storage on that device only**.
   The status bar says "your saved copy" when you are looking at local edits.
   Nobody else sees them, and they vanish if the browser data is cleared.
2. **Export .db** (header button) downloads your edited database. That file is the
   master copy — keep it safe after any data-entry session.
3. To **publish** edits so everyone sees them: replace `kajukenbo.db` in the
   GitHub repo with the exported file (github.com → repo → `kajukenbo.db` →
   upload a new version), or send the file to Muse and ask for a republish.
   Every build carries a `data_version` stamp; a newer published database
   automatically supersedes older saved copies in visitors' browsers, while a
   saved copy based on the current version is kept.
4. **Reset to published database** (sidebar) throws away your browser's local
   edits and reloads the published data.

## Known limits of the data (as published)
- **677 people (6.6%) sit under "⚠ Unplaced — teacher not yet in tree."** Nearly all
  are branch heads whose own teacher line runs across poster regions and could not
  be proven from the poster tiles alone. Their students and sub-branches are
  complete under them; only the one link upward is missing.
- **~209 entries are unverified** — low-confidence readings (split columns, tile-edge
  fragments), each with the reason in its notes rather than a guess.
- Spellings follow the poster as printed, including its misspellings; variants are
  noted on the affected records.

## Files
- `index.html` — the app. Over HTTP it loads `kajukenbo.db` from beside it; opened
  as a local file it uses an embedded copy of the same database (fully standalone).
- `kajukenbo.db` — the standalone SQLite database (open it in DB Browser for
  SQLite, or query it with the `sqlite3` command line).
- `persons.json` — human-editable source of truth for the whole tree.
- `build_db.py` — rebuilds `kajukenbo.db` from `persons.json` (stamps a fresh
  `data_version`); `embed.py` then rebuilds `index.html` from `index_template.html`.
- `persons_template.csv` — bulk-entry template.
- `source/` — the full-resolution poster scan the transcription was made from.
- `transcription/` — tile grid, per-quadrant agent readings, merge logs, and the
  same-name review list from the transcription pipeline.

## Data model (kajukenbo.db)
- `persons(id, name, title, kind, branch, school, location, teacher_id, sort_order,
  poster_x, poster_y, verified, notes)` — `teacher_id` is the tree parent;
  `poster_x/y` (0–1) record a person's position on the poster;
  `verified` flags entries checked against the poster.
- `lineage(teacher_id, student_id, kind)` — extra teacher relationships that don't
  fit the single-parent tree.
- `persons_fts` — FTS5 full-text index over name/title/branch/school/location/notes.
- `meta` — title/edition/source plus `data_version`, the build stamp described above.

## Rebuild pipeline (for maintainers)
Edit `persons.json` → `python3 build_db.py` → `python3 embed.py` → test
(`~/workspace/kajukenbo-qa/test.js`, jsdom) → copy `index.html` + `kajukenbo.db`
into the repo. The build prints a root census — it must be exactly 1 — and the
tree is audited for full reachability from the root before anything is published.
