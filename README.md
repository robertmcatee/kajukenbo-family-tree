# Kajukenbo Family Tree

An interactive teacher → student lineage tree for Kajukenbo, transcribed in full
from the 2024 Kajukenbo Family Tree poster (Nov 2024 edition): 10,285 entries,
13 generations, with search, show/hide branches, and a detail
panel tracing each person's line back to the Black Belt Society root.

**Live site: https://robertmcatee.net/kajukenbo-family-tree/**

## How it works
One static page (`index.html`) running a real SQLite database in the browser
(sql.js WASM). Served over HTTP it loads `kajukenbo.db` from this repo; opened
as a local file it falls back to an embedded copy, so the page also works
standalone with no server.

## Editing & publishing updates
GitHub Pages cannot save changes, so edits made on the live site are stored in
the editor's own browser only. To publish them: use the app's **Export .db**,
then replace `kajukenbo.db` in this repo with the exported file (or send it to
the maintainer). Each database carries a `data_version` stamp; a newer
published database automatically supersedes stale saved copies in visitors'
browsers. The full maintainer notes, data model, and rebuild pipeline are in
the project README kept with the source files (`persons.json` is the
human-editable source of truth).

## Data notes
Spellings follow the poster as printed. 677 branch heads (6.6%) sit under an
"Unplaced" group because their own teacher link could not be proven from the
poster alone — their students and sub-branches are complete. **All entries are
currently marked UNVERIFIED**: the machine transcription awaits a name-by-name
review against the poster with the owner, and entries will be marked verified
branch by branch as that review proceeds. ~209 readings carry low-confidence
notes with their reasons recorded, rather than guessed.
