# Frontier Archive — Data Structure README


## NOTE TO USER

If you have more videos that have appeared than listed in the "Broadcast Order.png" (Found in Misc folder), please send recordings (Snipper tool with video is fine!) to QZRChedders#1860 on Discord or raise an Issue on GitHub and I can include it in future versions as many videos seem to appear and disappear at random (And some are locked behind certain progress gates in-game it seems!)


## The tagging tool: `grid_tagger.html`

A self-contained HTML/JS app for tagging videos. Open in Chrome or Edge (it uses the File System Access API, which Firefox doesn't support).

If the file picker fails on `file://` URLs, run `python -m http.server` from this folder and open `http://localhost:8000/grid_tagger.html`.

**Workflow**:
1. Click *Open project folder* and select `ArchiveInvest`. The tool authorizes the folder for the session and reads `glyph_dictionary.csv`.
2. Drag a video onto the load panel. The tool auto-suggests the matching `_patterns.csv` if one exists.
3. Position the 9×9 grid over the cells using *Auto-fit (diamond)* or *Fit by 2 cells* (click center cell, then top-left).
    NOTE: The auto-fit feature is very unreliable and should be ignored., instead use the *Fit by 2 cells* feature, select the MIDDLE of the centre cell, then the MIDDLE of the top-left cell. Doing this accurately will make your life easier!
4. Step through the video and click cells to mark glyph patterns. Click *Save as pattern* (or press `S`) at each glyph frame.
5. Click *Save to folder* — writes `<broadcast>_patterns.csv` and updates `glyph_dictionary.csv` with any new glyphs.

**Useful features**:
- **Auto-Detect** - Under the advanced settings (Found beneath the Glyph Viewer), press "Auto-tag this frame" to automatically select cells it sees as darkened, configure the thresholds to tune it so it is only selecting dark cells
    NOTE: The auto-tag struggles significantly with extreme edge cells and those under the diamond overlay (As part of the cell is occluded), ENSURE you check these every glyph for best results. (8,8) - Bottom Right - is a significant offender for being missed manually and by the auto-tag feature
- **Glyph viewer** (sidebar) — type any glyph ID to see its pattern rendered. *Load as marks* puts it on the canvas for visual comparison against the current frame.
- **Near-match badges** — saved patterns within Hamming distance ≤ 2 of an older glyph show `≈#42 (1 off)` in yellow. Click the badge to snap to the older glyph, OR ignore it (some near-matches are legitimate cycle-stamped variants — see PROJECT_KNOWLEDGE.md).
- **Edit-in-place** — click any pattern row to load its marks for editing. *Save changes* replaces it; *Cancel edit* exits.
- **Auto-sort by frame** — patterns always reorder to ascending frame after every save.

**Browser session limitation**: the folder permission expires when you close the tab. You'll re-pick the folder once per browser session.



This folder contains source videos, transcripts, and tagged glyph patterns for the **Frontier Archive** broadcasts in EVE Frontier. This README explains how the data is organized and how to read each file.

For *findings, hypotheses, and analysis*, see [PROJECT_KNOWLEDGE.md](./PROJECT_KNOWLEDGE.md).

---

## What's a "broadcast"?

A short in-game video with spoken narration AND a sequence of 9×9 grid glyph patterns rendered at the end. The narration is one channel; the glyphs are a separate cipher channel. Each broadcast is named like `E6C2-N` — meaning **Era 6, Cycle 2, broadcast "N"**. We have 21 broadcasts spanning Era 5 (one early outlier) and Era 6 cycles 2 through 5.

---

## Folder layout

```
ArchiveInvest/
├── README.md                   ← this file
├── PROJECT_KNOWLEDGE.md        ← findings, hypotheses, decoding theories
├── grid_tagger.html            ← the in-browser tagging tool (open in Chrome/Edge)
├── Order of broadcasts.png     ← canonical chronological order
├── E6C2-N_patterns.csv         ← stray duplicate, can be deleted
├── E6C3-11 First text.png      ← ad-hoc reference image
├── E6C3-11 Second text.png     ← ad-hoc reference image
├── suspects_action_list.md     ← partially-superseded retag list (see PROJECT_KNOWLEDGE)
├── address_candidates.csv      ← test addresses for Garnet block explorer
├── phrases.csv                 ← cluster definitions (derived)
├── broadcasts.csv              ← per-broadcast annotated sequences (derived)
├── Videos/                     ← raw .mp4 source videos
├── Transcripts/                ← spoken-text transcripts (one .txt per broadcast)
└── PatternCSVs/                ← canonical tagged glyph data
    ├── glyph_dictionary.csv    ← master dictionary of unique glyphs
    ├── E6C2-N_patterns.csv     ← per-broadcast pattern data (one per video)
    ├── E6C2-11_patterns.csv
    └── ...
```

`PatternCSVs/PatternCSVs/` is an empty leftover folder — safe to delete by hand.

---

## The 9×9 glyph format

Each glyph is a 9×9 binary grid. Cells are referenced as `(row, col)` zero-indexed, row-major.

**26 cells form a central diamond that is always blank** — the bright reference shape glyphs are drawn around. Only the 55 cells *outside* the diamond can carry information.

Diamond cells (`X`) — always blank in every glyph:
```
. . . . X . . . .
. . . X X X . . .
. . X X . X X . .
. X X . . . X X .
X X . . . . . X X
. X X . . . X X .
. . X X . X X . .
. . . X X X . . .
. . . . X . . . .
```

A glyph is fully described by which of the 55 non-diamond cells are lit. Glyphs in the dataset have anywhere from 2 to 16 cells lit, most in the 7–13 range.

**Cadence** — broadcasts emit one glyph every 6 frames (~0.193s) at 30fps. The Era 5 broadcast is faster (0.147s/glyph).

---

## The pattern CSV format

`PatternCSVs/E6C2-N_patterns.csv` is the canonical per-broadcast format. One CSV per broadcast. Each row is one glyph occurrence.

Columns:

| column | meaning |
|---|---|
| `pattern` | sequential index within this broadcast (1, 2, 3, …) |
| `frame` | video frame number where this glyph appears |
| `time_s` | timestamp in seconds (= frame / 30) |
| `n_cells` | number of lit cells (2–16) |
| `cells` | human-readable list of `(row col)` pairs, e.g. `(0 5) (1 7) (3 4)` |
| `r0c0`, `r0c1`, … `r8c8` | 81 binary columns, one per cell — `1` if lit, `0` otherwise |

The `cells` text and the `r*c*` binary columns are redundant — the tool writes both for convenience. Either is sufficient to reconstruct the glyph.

**Example row** (`E6C2-N` pattern 1):
```
1, 880, 29.3219, 14, "(0 3) (0 6) (0 7) (1 0) (1 6) (1 8) (2 4) (3 3) (4 3) (6 0) (7 2) (7 7) (8 2) (8 7)", 0,0,0,1,0,0,1,1,0, ...
```

**File naming**: `<BroadcastName>_patterns.csv`. Most match `E6C[2345]-*`. The lone Era 5 broadcast is named `Youtube_1_patterns.csv` (sourced from a YouTube upload of an early-game stream).

---

## The dictionary: `glyph_dictionary.csv`

The master list of every unique glyph fingerprint seen across all broadcasts. Same `r*c*` cell layout as pattern CSVs, plus metadata.

Columns:

| column | meaning |
|---|---|
| `glyph_id` | sequential ID, assigned in order of first appearance |
| `fingerprint` | 81-character binary string (row-major), the canonical hash key |
| `n_cells` | cell count |
| `cells` | human-readable cell list |
| `first_video` | which broadcast this glyph was first seen in |
| `first_frame` | frame number of first appearance |
| `first_time_s` | timestamp of first appearance |
| `r0c0` … `r8c8` | binary cell values |

**About glyph IDs**: they're assigned during dictionary build, walking broadcasts in canonical chronological order (oldest first). If the dictionary is rebuilt with a different scan order, IDs renumber. The **fingerprint is stable**; the ID is just a convenient handle.

The canonical seed order is documented in `PROJECT_KNOWLEDGE.md` under "How to pick up this work" — preserve it when rebuilding to keep IDs stable across sessions.

---

## Transcripts (`Transcripts/*.txt`)

One plain-text file per broadcast with timestamped narration:

```
[ 0.75 - 20.66] Foundation is inert without fuel, begin full cycle crude extraction, ...
[ 20.66 - 25.31] A synthesis. E6C2-16 a
```

Format: `[start_s - end_s] spoken text`. The narration is **separate from the glyphs** — they run in parallel and don't necessarily encode the same content. Some broadcasts have very few spoken words but many glyphs (`E6C4-1H`: one word, 24 glyphs).

Transcripts are useful for:
- Identifying competition targets (e.g., `E6C2-N`'s narration names `E6C2-16` as the synthesis prize).
- Finding recurring phrases that may map to fixed glyph clusters.
- Understanding broadcast intent (announcement, recap, lore).

---

## Derived analysis files

These are generated from the pattern CSVs + dictionary. Re-runnable.

### `phrases.csv` — cluster definitions

One row per "phrase" (a glyph or set of glyphs that always appear together across broadcasts).

| column | meaning |
|---|---|
| `phrase` | letter A, B, C, … assigned by descending size |
| `n_glyphs` | number of glyphs in this cluster |
| `glyph_ids` | comma-separated list of member IDs |
| `n_broadcasts` | how many broadcasts contain it |
| `broadcasts` | which broadcasts |
| `role` | a one-line guess at what track/role this phrase fills |

A cluster has **locked internal order** — whenever it appears, its members are in the same sequence. So a "phrase" of 11 glyphs is effectively an 11-token fixed string the broadcasts are emitting verbatim.

### `broadcasts.csv` — per-broadcast annotated sequences

One row per broadcast.

| column | meaning |
|---|---|
| `broadcast` | broadcast name |
| `track` | Main / Side / Sparse / Bridge / E5 |
| `n_glyphs` | number of patterns |
| `duration_s` | total time span |
| `phrase_sequence` | compressed annotated sequence (e.g., `J×2 E×7 B B×13 <27>`) |
| `raw_glyph_ids` | full glyph ID sequence (e.g., `#1 #2 #3 #4 ...`) |

In `phrase_sequence`, capital letters are clusters (`B` = one B-cluster glyph, `B×13` = 13 consecutive B glyphs in their fixed order). `<27>` means a singleton glyph (not in any cluster) with that ID.

### `address_candidates.csv` — test addresses for the chain hypothesis

Generated from `E6C2-N`'s 36 glyphs. Reads them as 3-glyph groups → 12 candidate Ethereum-format hex strings to test in Garnet block explorers. See PROJECT_KNOWLEDGE.md for the methodology and caveats.



---

## Naming conventions

**Broadcast IDs**: `E<era>C<cycle>-<id>`
- `E6C2-N` = Era 6, Cycle 2, broadcast "N"
- `E6C4-35` = Era 6, Cycle 4, broadcast "35"
- The suffix is alphanumeric; appears to use bases beyond 10 (so `E6C4-1G`, `E6C4-2T` are valid)
- `Youtube_1` is the lone Era 5 broadcast, named after its source

**Cycles** are CCP's term for in-game test cycles. Each one is canonical lore as a "shear event" or "jubilee" — a world reset. Cycle 4 introduced a "Memory" mechanic that explicitly carries forward across resets, which is plausibly *what the Frontier Archive is*.

**Eras** seem to span multiple cycles. We have data from Era 5 (one broadcast) and Era 6 (twenty broadcasts spanning C2–C5).

**Glyph IDs** are session-local handles assigned during dictionary build, in chronological broadcast order. Stable as long as the broadcast scan order is preserved.

---

## How to read what's there in 5 minutes

1. Open `Order of broadcasts.png` to see the chronological list of broadcasts.
2. Open `broadcasts.csv` — you can see at a glance the structure of each broadcast (e.g., `J×2 E×7 B B×13 <27>` reads as preamble + main body + singleton).
3. Open `phrases.csv` — see which glyphs always co-occur and across how many broadcasts.
4. Open a transcript (e.g., `Transcripts/E6C2-N.txt`) to get the narration.
5. Open `grid_tagger.html`, click *Open project folder*, click any *Previous scans* entry, type a glyph ID into the *Glyph viewer* — visualizes individual glyphs in context.
6. Skim `PROJECT_KNOWLEDGE.md` for findings, hypotheses, and what's been ruled out.

---

## Common operations

**Re-build the dictionary fresh from all CSVs**: rebuild scripts walk the canonical broadcast order (see `PROJECT_KNOWLEDGE.md`), assign sequential IDs to first-seen fingerprints, and write `glyph_dictionary.csv`. Past sessions used `/tmp/rebuild*.py` style scripts.

**Find suspect glyphs (near-twins)**: scan the dictionary for pairs with Hamming distance ≤ 2. Some are tagging mistakes; some are legitimate cycle-stamped variants. The previous list in `suspects_action_list.md` is partly superseded — see PROJECT_KNOWLEDGE.md for details.

**Add a new broadcast**:
1. Drop the `.mp4` into `Videos/`.
2. Open `grid_tagger.html`, load the video, position the grid, tag patterns, save.
3. Optionally add a transcript at `Transcripts/<broadcast>.txt`.
4. Re-run the dictionary rebuild.

---

## Data integrity notes

- Dictionary IDs are NOT stable across rebuilds with different scan orders.
- A bash-mount filesystem may lag behind the tagging tool's saves by a few seconds — re-read with the file system tool if `wc -l` and the tool's pattern count disagree.
- The stray top-level `E6C2-N_patterns.csv` is a byte-identical duplicate of the canonical version in `PatternCSVs/`. Safe to delete.
- The `cells` text column and the `r*c*` binary columns must agree — if they don't, the binary columns are authoritative (the tool writes those last).
