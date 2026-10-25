# ROM Sweep

A single-file tool for cleaning up retro ROM libraries on SD cards and folders. It runs locally in the browser on macOS and Windows, with no install, no server, and no internet needed (fonts load from Google Fonts when online and fall back to system fonts offline).

## What it does

- **Scan** a `Roms` folder or SD card. Each system folder is flagged as *has ROMs*, *empty*, or *images/extras only*. Junk like `.DS_Store`, `._` files and `Thumbs.db` is ignored.
- **Export list**: a CSV of every system folder and file.
- **Delete** empty and images-only folders. Folders that contain ROMs are protected.
- **Copy to a master folder** with editable destination names (`SFC` → `snes`, `MD` → `megadrive`, …). Skips identical files, never overwrites a file with a different size, and never modifies the source.
- **Clean up ROM names**: strips list numbering (`001 Name`, `0123 - Name`, `#12 Name`, `12. Name`), with optional underscore and tag cleanup.
  - Detects each folder's numbering style, so titles like *1942*, *10-Pin Bowling* or *007 - The World Is Not Enough* aren't damaged; ambiguous files are flagged and left unchecked.
  - Choose which folders, and whether renames apply to the source, the master copy, or both.
  - Renames matching saves and box art with the ROM, including saves in a separate folder.
  - Skips disc sets (`.cue/.bin`, `.m3u`, `.gdi`) and multi-file game folders.
  - Every in-place rename downloads a JSON log; **Restore from log** puts the old names back.
- **Find duplicates**
  - *Exact copies*: same file content (SHA-1, compared only between files of equal size; files over 256 MB get a partial check, labeled as such).
  - *Same game*: names match after stripping numbering and region/version tags; disc numbers are kept so multi-disc games don't match each other.
  - Auto-picks a keeper per group (master copy, then verified `[!]` dump, then preferred region, then unzipped, then the copy with a save); every pick can be changed.
  - Optionally compares against the master folder (read-only).
  - Extras are moved to `_duplicates/` (structure kept) or deleted; saves are always moved, never deleted.

## Requirements

Uses the File System Access API, which only Chromium browsers support:

| Browser | Works |
| --- | --- |
| Chrome, Edge (macOS, Windows) | Yes |
| Brave | Yes, after enabling `brave://flags` → *File System Access API* and restarting |
| Safari, Orion, Firefox | No (no folder access in WebKit or Gecko) |

## Usage

1. Open `rom-sweep.html` in a supported browser (drag it into a window).
2. **Choose folder** → pick a `Roms` folder or SD card root. A `roms/` subfolder is used automatically.
3. Allow edit access when the browser asks. Nothing changes until you press an action button, and destructive actions always confirm first.

## Safety notes

- Test on a copy of a card first, or use **Master copy** scope, which never touches the source.
- Frontends tie saves and box art to the ROM's exact filename. Renaming on a card renames those too, but saves stored somewhere the tool can't see will need a separate pass.
- Some frontends cache game lists (e.g. `GB_cache6.db` on Onion); names may look stale until the cache rebuilds.
