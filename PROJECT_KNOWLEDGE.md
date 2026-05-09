# Frontier Archive Puzzle — Project Knowledge

A handoff document capturing everything discovered so far. Drop this into a fresh chat session to continue the work without re-deriving findings.

---

## What this is

A working analysis of the **Frontier Archive** broadcasts in **EVE Frontier** (CCP Games), specifically the cipher of 9×9 grid glyph patterns that appear at the end of each broadcast video. The user is George Peralta. The puzzle is canonical in-game content (not fan-made) and currently has no public solution.

EVE Frontier is a blockchain-based MMO. It originally ran on **Lattice's Redstone Garnet** testnet (Ethereum L2, EVM-compatible, chain ID 17069, OP Stack on Holesky). At the start of Cycle 5 (March 2026) it migrated to **Sui** (32-byte object IDs / 256-bit). Cycles ("jubilees" / "shear events") are canonical in-game resets. Era 6 spans cycles 2–5 in the data we have. Era 5 was a much earlier game state. **Cycle 4 (Eternal Forge) introduced "Memory" — a progression system that explicitly carries forward across resets.** The Frontier Archive is plausibly that Memory system's player-facing surface.


---

## Workspace layout

```
ArchiveInvest/
  grid_tagger.html              ← the tagging tool I built (open in Chrome)
  Order of broadcasts.png       ← canonical broadcast order (oldest at bottom)
  E6C2-N_patterns.csv           ← stray duplicate of canonical (safe to delete)
  E6C3-11 First text.png        ← reference image
  E6C3-11 Second text.png       ← reference image
  PROJECT_KNOWLEDGE.md          ← this file
  suspects_action_list.md       ← retag punch list (some entries now superseded — see below)
  phrases.csv                   ← cluster definitions
  broadcasts.csv                ← per-broadcast annotated phrase sequences
  address_candidates.csv        ← Garnet-explorer test addresses for E6C2-N
  Videos/                       ← source mp4s (E6C2-* through E6C5-*, plus Youtube_1)
  Transcripts/                  ← spoken text per broadcast
  PatternCSVs/                  ← canonical per-broadcast pattern CSVs
    glyph_dictionary.csv        ← the master dictionary (rebuilt fresh each pass)
    PatternCSVs/                ← empty stale subfolder; user can delete
```

`grid_tagger.html` features: project folder linking via File System Access API, dictionary management, two-point grid fit (click center cell + top-left cell), near-match warning (Hamming distance ≤ 2 surfaces an "≈#N (X off)" badge), edit-in-place, auto-sort by frame, Glyph Viewer panel.

---

## The dataset (current state, 21 broadcasts, 586 patterns)

| Era | Cycle | Broadcasts |
|-----|-------|------------|
| E5 | C3 (theoretical) | `Youtube_1` (16 patterns) |
| E6 | C2 | `E6C2-N` (36), `E6C2-11` (32), `E6C2-1K` (30), `E6C2-1N` (29) |
| E6 | C3 | `E6C3-2` (29), `E6C3-9` (36), `E6C3-18` (35), `E6C3-1L` (30) |
| E6 | C4 | `E6C4-V` (24), `E6C4-13` (25), `E6C4-16` (31), `E6C4-17` (19), `E6C4-18` (23), `E6C4-19` (20), `E6C4-1G` (26), `E6C4-1H` (24), `E6C4-2T` (28), `E6C4-30` (29), `E6C4-35` (48) |
| E6 | C5 | `E6C5-N` (16) |

Cycle 4 was Redstone Garnet (EVM); Cycle 5 is Sui; Cycle 2 was earlier on Garnet; Era 5 was much earlier (likely pre-Garnet or earliest on-chain state).

`E6C2-N`, `E6C3-9`, `E6C4-35` are the most complete broadcasts; `E6C5-N` and `Youtube_1` (E5) are the shortest.

`E6C5-13` has a transcript but no pattern CSV yet.

---

## Glyph format

Each glyph is a 9×9 binary grid. **26 cells form a central diamond** that is **always blank** in every glyph — it's the bright reference shape the broadcasts overlay glyphs around. The remaining **55 cells** are the encodable region.

The diamond shape (`X` = always blank):
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

So each glyph encodes at most **55 bits** of information, with cell counts ranging from 2 to 16 (most in 7–13 range). 145 unique glyphs in the current dictionary; ~33% are singletons (appear only once).

The cadence is **0.193s/glyph** (one new glyph every 6 frames at 30fps) for E6 broadcasts. The Era 5 broadcast runs faster at **0.147s/glyph** (~4–5 frames apart, possibly a different render framerate or earlier protocol).

---

## Cluster structure (phrases that recur across broadcasts)

Glyphs that appear in identical sets of broadcasts form **fixed clusters with locked internal order**. Currently 12 clusters of 2+ glyphs. Cluster letters re-shuffle each time the dictionary is rebuilt (size-ranked); these are the current letters.

| Phrase | Glyph IDs | Broadcasts containing it |
|--------|-----------|---|
| **A** (20 glyphs) | 82, 83, 86–103 | `E6C3-18`, `E6C4-V` (Sparse track) |
| **B** (14) | 9, 14–26 | All 6 main-track E6 broadcasts |
| **C** (12) | 54, 55, 57–66 | `E6C2-1K` and `E6C4-35` only |
| **D** (11) | 37, 41–50 | E6C2-1K + 10 E6C4-* + E6C5-N (12 total — most-replayed) |
| **E** (10) | 3–8, 10–13 | 5 main-track E6 broadcasts |
| **F** (3) | 121, 122, 123 | 7 E6C4 main-track broadcasts |
| **G** (2) | 38, 39 | 10 side-track + E6C5-N broadcasts |
| **H** (2) | 1, 2 | 5 main-track + `E6C4-35` (bridge) |
| **I** (2) | 4, 5 | 6 main-track broadcasts |
| **J** (2) | 1, 3 | 5 main-track broadcasts |
| **K** (2) | 117, 118 | 5 cycle-4 broadcasts |
| **L** (2) | 34, 35 | 3 main-track broadcasts |

(Note: cluster IDs shift slightly between dictionary rebuilds — what matters is the SHAPE of the clusters, not the specific numbers.)

---

## The three transmission tracks

Cross-broadcast Jaccard similarity reveals **three near-disjoint message streams plus a bridge**:

```
Main track (5–6 broadcasts):  E6C2-N, E6C2-11, E6C2-1N, E6C3-2, E6C3-9   (+ E6C3-1L loosely)
Side track (12 broadcasts):   E6C2-1K, E6C5-N, all 10 main E6C4-* (-V)
Sparse track (2 broadcasts):  E6C3-18, E6C4-V                            (these recap each other)
Bridge (1 broadcast):         E6C4-35                                    (contains content from all tracks)
Era 5 (1 broadcast):          Youtube_1                                  (overlaps Side track + E6C4-35 unique payload)
```

Cross-track overlap is **zero** between Main, Side, and Sparse — they're truly different message streams. Only `E6C4-35` carries content from multiple tracks.

### Track 2 (Side) master sequence

Reads cleanly from `E6C4-1H`:
```
#123 → G(#124, #125, #126) → #127 → #128 → #129 → #130 → F(#112, #113) → H(#114, #115)
     → #39 → #116 → E(#41–49) → #50
```
(Older cluster letters from earlier analyses — see broadcasts.csv for the current naming.)

Every other side-track broadcast windows into this 24-glyph sequence at different starting positions. `E6C2-1K` is the original full-length transmission with a longer tail (cluster C: #54–66). `E6C5-N` is the shortest, joining at position 9.

### E6C4-35 — the anthology / bridge broadcast

48 patterns, the longest single broadcast. Sequence reads as:
1. Side-track tail (#129 → #130 → F → H → cluster D → #50)
2. Cluster C (the E6C2-1K tail content — only otherwise seen in E6C2-1K)
3. **11 unique glyphs (#136–146)** — these were thought to be "fresh new payload" until the Era 5 broadcast was added, which contains 10 of these 11 verbatim (see Era 5 finding below)
4. Main-track preamble (cluster L = #66, #67) and start of main-track (cluster H = #1, #2)

It transmits content from every track. The user's reading: it's a deliberate anthology / "best of the Memory archive."

---

## Transcript context (from user-provided audio transcripts)

Each broadcast has spoken narration AND glyphs at the end. **The glyphs are NOT a 1:1 encoding of the spoken text.** Some broadcasts have very few words but many glyphs (`E6C4-19`: 2 words, 19 glyphs; `E6C4-1H`: 1 word, 24 glyphs). The glyphs are a **separate channel** running alongside the narration.

Selected transcript clues:

- **`E6C2-N`**: *"Foundation is inert without fuel, begin full cycle crude extraction, let purpose burn through hesitation, quantities gathered now, shape endurance tools, the one who exceeds, shall be marked. A synthesis. E6C2-16 a..."* (cuts off) — **this is the simplest and most informative broadcast.** Hints at a competition for "synthesis E6C2-16" with the highest extractor being "marked".
- **`E6C2-11`**: *"...synthesis E6C2-16, deliver to highest extractor, singular performance shapes future design, awaited eternally."* — confirms E6C2-16 is the synthesis target.
- **`E6C2-1K`**: *"One synthesis awaits, synthesis E6C2-1F has been embedded with an containment system location, obscured, signal, oblique, visual indicators deployed, alive first, retrieve, decode, retention, confers, ascension, no duplicates exist, no guidance will repeat."* — points to **E6C2-1F** (a different synthesis target).
- **`E6C4-35`**: *"increasing, jambilee event estimation, five intervals, away"* — "jambilee" likely transcription of **jubilee** (CCP's term for cycles).
- **`E6C5-N`** and **`E6C5-13`**: mention "**calamity marrow**" — possibly an in-game item.

Recurring phrases that may map to clusters:
- "keep monitors" / "cycle persists" — appears in `E6C4-13`, `E6C4-16`, `E6C4-V` (Sparse + Side intersection)
- "deviation persists" — in `E6C4-13`, `E6C4-16`
- "calamity marrow" — `E6C5-13`, `E6C5-N`
- "synthesis" — only cycle-2 broadcasts

---

## Frequency analysis findings

568 total occurrences across 145 unique glyphs. Distribution is **strongly NOT English**:

- The top 11 glyphs are tied at 12 occurrences each (~2.1%) — the cluster D payload broadcast 12 times. English's most common letter is 3× the 10th-most. Distribution is essentially flat.
- **Index of Coincidence = 0.0104**. English IC ≈ 0.0667. Random over 145 symbols ≈ 0.0068. Corpus is just 1.5× random — far from natural language.
- **χ² test against English letter frequencies = 79.1** (critical at p=0.05 is 37.65). Distributions are statistically incompatible.
- 33% of glyphs are singletons. English text has zero singleton letters.
- Adjacent-glyph repeat rate is 1.09% (English ~3%).

Conclusion: **not a monoalphabetic substitution of English.** The flat distribution is consistent with retransmission of fixed payloads (cluster D × 12 broadcasts), not natural language frequency.

---

## The blockchain hypothesis (current best theory)

EVE Frontier was on **Redstone Garnet** (EVM, 160-bit addresses) for cycles 2–4 and migrated to **Sui** (256-bit) for cycle 5. Era 5 was earlier still. Despite the chain migrations, **the same glyph fingerprints appear in Era 5, Era 6 Cycles 2/4/5** — so glyphs cannot be raw chain addresses (which would change across migrations).

### Refined hypothesis (after the Era 5 broadcast was added)

The encoding is **hybrid: content-addressed core + per-cycle stamps**.

- **Most cluster glyphs are content-addressed** — stable references to preserved memory chunks. They appear byte-identical across multiple cycles and chains.
- **Some glyphs have "near-twin" variants** that differ by 1–2 cells and only appear in specific cycles. These are likely **cycle-stamped pointers** to current-chain entities.

Evidence for this:

1. **Cluster D's 11 glyphs** appear identically in 12 broadcasts spanning cycles 2, 4, and 5 (across at least one chain migration). Pure content-addressed.
2. **The Era 5 broadcast** (`Youtube_1`) shares 15 of 16 glyphs verbatim with E6 broadcasts. The one different glyph at frame 95 is a 2-cell shift on `#140` — `(5,2) → (5,3)`. This is the era stamp, NOT a tagging error (user confirmed visually).
3. **Cells (5,2) and (2,6) are C4-exclusive** in the entire 570-pattern corpus. Strong cycle-correlation signal.
4. **Several near-twin pairs** exist where one glyph appears across multiple cycles and the other appears only in C4:
   - `#39 (C2,C4,C5) ↔ #130 (C4-only)` differing at `(0,3)` and `(2,6)` [the C4-exclusive cell]
   - `#40 (C2,C4,C5) ↔ #131 (C4-only)` at `(1,8)`
   - `#52 (C2,C4,C5) ↔ #133 (C4-only)` at `(8,8)`
   - `#83 (C3) ↔ #113 (C4-only)` at `(0,1)`
   - `#84 (C3) ↔ #114 (C4-only)` at `(8,6)`
5. **Per-cycle cell-activation rates show monotonic trends**:
   - Climbing through cycles: `(8,8) 22→44%`, `(4,2) 17→38%`, `(8,7) 18→38%`, `(3,5) 11→31%`, `(1,7) 11→31%`
   - Declining: `(6,1) 24→0%`, `(8,6) 18→0%`, `(7,2) 34→12%`, `(8,2) 26→6%`

### What this means for decoding

- Stable cluster glyphs (D, B, E, F, H, I, J, L, A, C, G) are likely **content-addressed entity references** — would resolve via the MUD World to whatever the current-chain address is.
- The 5 "C4-only twin" glyphs (#113, #114, #130, #131, #133) are likely **chain-specific pointers**. These are the most informative addresses to test in the Garnet block explorer.
- The candidate addresses extracted earlier from `E6C2-N` (in `address_candidates.csv`) treat all 36 glyphs as raw bits → 12 Ethereum addresses via 3-glyph groups. These may still be useful but only for the C4-stamped glyphs would they resolve cleanly.

### Suspect glyphs — re-interpretation

The "suspect" list I generated earlier (in `suspects_action_list.md`) flagged glyphs within Hamming distance ≤ 2 of older glyphs as potential mistags. **After this analysis, several of those "suspects" are now confirmed as cycle-stamped variants, not tagging errors.** Specifically:
- `#125` and `#126` in cycle 4 broadcasts are real (originally flagged as mistags of `#131`/`#132`)
- `#113`, `#114`, `#130`, `#131`, `#133` are C4-stamped variants of universal content
- The `(8,8)` corner suspects across cycles may be cycle-stamps too

The Era 5 `f95` glyph and `#140` are **both legitimate**, not duplicates. The 1-cell shift IS the era stamp.

---

## Era 6 Cycle 2 — the active competition

Per the user, `E6C2-N` is the simplest broadcast and hints at a *real in-game competition*. Transcript: *"the one who exceeds, shall be marked"* — first-to-find-and-deliver wins. Target: **`E6C2-16`** (a synthesis). The glyphs at the end of E6C2-N likely encode either:
- The location/coordinates of E6C2-16
- A code/key needed to claim or unlock it
- A 12-address packet of relevant on-chain references

`E6C2-1K` similarly points at **`E6C2-1F`** as a different synthesis target.

---

## E6C2-N candidate addresses (top 4 to test)

From `address_candidates.csv`, with 36 glyphs read as 3-glyph groups → 12 candidate Ethereum addresses. Best candidates:

```
1. E6C2-N closing block (G×3 L×2 <36>):
   0x0000238000024100404010883194a94452010141
   ← directly precedes the cut-off "E6C2-16 a..." in transcript

2. E6C2-N singletons (#2, #30, #36):
   0x40502014040082102208400020a90a4000008b00
   ← 3 glyphs unique to this broadcast → exactly 165 bits

3. E6C2-N opening (J <2> J = first 3 glyphs):
   0x1694842008144480a040280801040004808ad0c0

4. Cluster D as universal signature:
   0x000001109110000150140030004a6018d1810610
```

**Critical caveat:** These were extracted before the Era 5 broadcast revealed the chain-stamp encoding. They might not resolve directly. The most informative addresses to actually test in Garnet are the C4-stamped twins listed above.

---

## What's known about the broadcast names

- `E6CX` = Era 6, Cycle X (date stamp). Era 6 covers cycles 2–5.
- The suffix (e.g., `-N`, `-11`, `-1K`, `-35`) appears to be a per-broadcast identifier within that cycle. Naming may use base-N digits (letters extending past 9).
- `E6C2-16` is referenced as a synthesis target in cycle-2 transcripts but does not exist as a broadcast we have. Likely a hidden/secret broadcast or in-game location.
- `E6C2-1F` is the analog target referenced by `E6C2-1K`'s transcript.
- `Youtube_1` is the Era 5 broadcast — user said it announced "a new cycle in Era 5" so it's effectively `E5C3-X` (some new broadcast in cycle 3 of era 5).

---

## Tools available in this folder

`grid_tagger.html` — open in Chrome or Edge. The tool now has:
- Project folder linking (one click per session)
- Glyph dictionary integration with near-match warnings
- **Glyph Viewer panel** — pull up any known glyph by ID to examine its pattern
- Two-point fit for grid alignment
- Edit-in-place + auto-sort by frame
- Save-to-folder writes both per-video CSVs and `glyph_dictionary.csv`

If the page errors loading the directory picker, run `python -m http.server` in the workspace folder and open `http://localhost:8000/grid_tagger.html`.

---

## Open questions / next experiments

1. **Test C4-stamped candidate addresses in Garnet block explorer.** The 5 twin pairs (`#113`, `#114`, `#130`, `#131`, `#133`) are the highest-signal candidates. If even one resolves to a known C4 entity, the chain-stamp hypothesis is confirmed.

2. **Era 5 transcript.** User indicated this exists but hasn't been added to `Transcripts/`. If the broadcast announces "E5C3" by name, we can correlate the glyph at `f95` (with cell `(5,3)`) against the literal cycle string — possible Rosetta Stone.

3. **`E6C5-13` pattern CSV.** Transcript exists but no patterns yet. Tagging this would give us a 2nd cycle-5 broadcast for cycle-stamp validation.

4. **Cycle-2 chain pointers.** If C4 has cells `(5,2)` and `(2,6)` exclusive, what cells would be C2-, C3-, C5-exclusive? Need more data per-cycle to identify.

5. **In-game examples.** User offered to share wallet addresses, transaction hashes, and entity references from the game. These would let us verify address format, check for structural padding patterns matching the diamond, and possibly find a direct match for a candidate glyph value.

6. **Discord community.** Official EVE Frontier Discord (`discord.com/invite/evefrontier`) is the most likely venue for community decoding work. User noted players aren't sharing publicly.

---

## How to pick up this work in a new chat

1. Open this file as the first read.
2. Confirm folder is mounted (`C:\Users\crazy\Desktop\Test\ArchiveInvest\`).
3. Re-seed the dictionary fresh with the canonical broadcast order:
   ```
   E6C2-N, E6C2-11, E6C2-1K, E6C2-1N,
   E6C3-2, E6C3-9, E6C3-18, E6C3-1L,
   E6C5-N,
   E6C4-V, E6C4-13, E6C4-16, E6C4-17, E6C4-18,
   E6C4-19, E6C4-1G, E6C4-1H, E6C4-2T, E6C4-30, E6C4-35,
   Youtube_1
   ```
   (Older broadcasts first; preserves stable IDs.)
4. Verify dictionary is at ~145 unique glyphs, ~586 occurrences.
5. The user's correction notes:
   - `#125`/`#126` are NOT mistags — they're real cluster G content.
   - `Youtube_1`'s `f95` glyph is NOT a mistag of `#140` — it's a legitimate era-stamped variant.
   - Multiple "suspects" in `suspects_action_list.md` are now suspected cycle-stamps; do NOT recommend retagging them.
6. **Ask the user**: any new broadcasts? Any in-game examples (wallets, hashes, entity refs)? Any Era 5 transcript? Any progress on E6C2-16?

---

## Methodology notes

- **Dictionary IDs shift between rebuilds.** They depend on broadcast scan order. The seed order in step 3 above is canonical. If you rebuild in a different order, IDs renumber. The cluster shapes and Hamming relationships are stable; specific ID numbers are not.
- **Bash filesystem mount can lag** behind the Read/Edit/Write tool view by a few seconds when the user has just saved from the tagger. If `wc -l` and Read disagree, trust Read; copy to /tmp and work there.
- **Frequency analysis script:** `/tmp/freq.py` style — sources glyphs from `PatternCSVs/*.csv`, hashes them via the diamond-aware fingerprint function.
- **Cell positions are (row, col) zero-indexed**, row-major. The diamond cells (always blank) are reproducible from the dictionary by counting which cells are zero in all 145 glyphs.

---

End of handoff. Total session work: 35 task entries, dictionary built+rebuilt 4 times, 21 broadcasts tagged, ~600 patterns, two hypothesis revisions (English-cipher → blockchain → content-addressed-with-cycle-stamps).
