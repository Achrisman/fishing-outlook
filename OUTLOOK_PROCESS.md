# FISHING OUTLOOK — PROCESS (canonical)

last_updated: 2026-10-14
owner: Alex | repo: github.com/Achrisman/fishing-outlook
This file is static. All mutable intelligence lives in the module files below. A copy of this file sits in Claude project knowledge; if they ever differ, the repo version wins.

## Session bootstrap (every outlook query)

1. Check current date/time (`user_time_v0`). Never assume — threads get picked up days later.
2. Pull fresh modules from the repo (raw URLs, curl from bash):
   - https://raw.githubusercontent.com/Achrisman/fishing-outlook/main/registry/spots.md
   - https://raw.githubusercontent.com/Achrisman/fishing-outlook/main/intel/tide_phase_logic.md (REQUIRED whenever a flats/channel spot is in scope)
   - https://raw.githubusercontent.com/Achrisman/fishing-outlook/main/registry/sources.md
   - https://raw.githubusercontent.com/Achrisman/fishing-outlook/main/species/<target>.md (only targets in scope)
   - https://raw.githubusercontent.com/Achrisman/fishing-outlook/main/intel/behavior_notes.md
3. If repo unreachable, fall back to the last-known copies in project knowledge and SAY SO — flag staleness.

## Outlook query procedure

INPUT: date (default today), mode (shore | boat | both), optional target species or spot shortlist.
HORIZON (updated 2026-10-07, per Alex): the scheduled daily run covers today in full detail (phase timelines, top picks) PLUS every remaining day of the current week through Sunday (minimum D+1..D+3 when run on a weekend), rendered as a WEEK-AHEAD block — ONE line per day: best window (local time) | the play (spot+species+approach, a few words) | grade, flagging any day whose forecast could not be verified fresh. Short descriptions only; the detail lives in today's briefs. Ad hoc queries use whatever horizon is asked.

0. **Time-remaining gate** (added 2026-08-23). If the query date is today, compute usable hours left (now → ~1h past sunset). Drop spots whose drive+launch time eats the productive window; compress recommended windows accordingly. An 11am query is a different outlook than a 6am query.
1. **Mode gate.** Boat mode adds go/no-go checks (wind, swell, small-craft advisories — AirCat 355 risk profile per spots.md launch notes) and changes regs (shore rockfish exemptions do not apply from the boat).
2. **Season/regs check.** For every candidate species, verify current CDFW season status via web search. Never assume from memory.
3. **Candidate spots.** Filter spots.md by mode + species + travel constraint if given.
4. **Live conditions pull** per registry/sources.md. ALWAYS pull live data — no seasonal generalizations. Minimum set: tide curve (with per-spot offset), wind, swell (coastal), water temp where available, weather. Salmon in scope -> also pull CalFishTrack telemetry per registry/sources.md (run position gates bay-corridor vs Benicia vs Delta).
5. **Score.** For each spot × species, compare live conditions against the formula block in the species module. Output per spot: window (local time), confidence, one-line reasoning citing which formula fired.
6. **Output format (updated 2026-10-14):** FULL BOARD scoring unchanged — one row per registry spot valid for the mode (core-shortlist spots always included and listed first, marked), each with: Species | Best window | Key driver | Conf | Watch-outs. OUTPUT PACKAGE (format v4, 2026-10-14 — PROGRESSIVE DISCLOSURE, per Alex: brief first, detail second; v3 components retained but re-layered):
LAYER 1 — THE CALL: <=5 lines, delivered FIRST and leading the push notification. Now-action + time, today's #1 window, the week's standout day, any hard flag (regs/safety/weather). Phone-glanceable; no evidence citations, no N-counts, no narration.
LAYER 2 — TIDE/WIND CHART — matplotlib PNG (unchanged from v3): local tide curve (cosine interp between offset extremes), vertical NOW + SUNSET lines, day labels, wind values where verified (flag estimates/provisional), extreme annotations, moon notes.
LAYER 3 — DETAIL BLOCKS, each standing alone and answering exactly one question, in this order:
(a) TODAY HOUR-BY-HOUR — phase timeline in decision form per intel/tide_phase_logic.md ("flood until HH:MM — species on structure, presentation → slack — ... → ebb — ...") for the top picks; sequenced spots MUST use this form.
(b) WEEK AT A GLANCE — one line per day: best window | play | grade, confidence-decay flags explicit.
(c) ALTERNATES — compact table (Spot | Species | Verdict).
(d) GATES / REGS / EVIDENCE — grades, N-counts, go/no-go numbers, regs citations. All evidence lives HERE, never woven into Layer 1.
Species scoring still uses the granular per-spot derived profiles in the species modules (N, tide split, time bins, months, gear) — not generic seasonal logic. Brief, decision-dense, no padding — the layering changes where detail goes, not how much rigor is behind it.
7. **Log.** If the session produces new intel or a formula correction, commit it to the repo (see Write protocol) and add a changelog line.

## Write protocol (Claude autonomous edits)

- Auth: fine-grained PAT, Contents read/write, this repo only. Provided by Alex in-session; not stored in repo.
- Every write = module edit + one line in logs/changelog.md (date, file, reason).
- Formula changes require data basis (raw CSV counts or dated first-person source). Summary CSVs (Bay_Area_Fishing_Insights_Rules.csv etc.) are NEVER a valid basis.
- New community intel follows existing workflow: prose extraction → Alex confirms → rows/notes committed.
- Nothing gets committed that Alex hasn't seen in-chat first.

## Module conventions

- Every file carries front-matter: last_updated, data_basis, confidence.
- Shore/boat is a `mode:` field on entries, never a separate folder.
