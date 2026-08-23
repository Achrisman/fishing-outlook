# FISHING OUTLOOK — PROCESS (canonical)

last_updated: 2026-08-23
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

0. **Time-remaining gate** (added 2026-08-23). If the query date is today, compute usable hours left (now → ~1h past sunset). Drop spots whose drive+launch time eats the productive window; compress recommended windows accordingly. An 11am query is a different outlook than a 6am query.
1. **Mode gate.** Boat mode adds go/no-go checks (wind, swell, small-craft advisories — AirCat 355 risk profile per spots.md launch notes) and changes regs (shore rockfish exemptions do not apply from the boat).
2. **Season/regs check.** For every candidate species, verify current CDFW season status via web search. Never assume from memory.
3. **Candidate spots.** Filter spots.md by mode + species + travel constraint if given.
4. **Live conditions pull** per registry/sources.md. ALWAYS pull live data — no seasonal generalizations. Minimum set: tide curve (with per-spot offset), wind, swell (coastal), water temp where available, weather.
5. **Score.** For each spot × species, compare live conditions against the formula block in the species module. Output per spot: window (local time), confidence, one-line reasoning citing which formula fired.
6. **Output format (updated 2026-08-23):** FULL BOARD — one row per registry spot valid for the mode (core-shortlist spots always included and listed first, marked), each with: Species | Best window | Key driver | Conf | Watch-outs. OUTPUT PACKAGE (format v3, 2026-08-23):
(a) TIDE/WIND CHART — matplotlib PNG via present_files: local tide curve (cosine interp between offset extremes), vertical NOW + SUNSET lines, hourly x-ticks, hourly wind numbers on twin axis (flag as est. when interpolated from NWS zone ranges), extreme annotations.
(b) TOP 2 PICKS as short prose intel briefs (a few sentences each — phase sequence, structure, presentation, grades, hard stops). NOT tables.
(c) ALTERNATES as a compact table (Spot | Species | Verdict). Each top pick at a sequenced spot MUST include the phase timeline in decision form per intel/tide_phase_logic.md ("flood until HH:MM — species on structure, presentation → slack — ... → ebb — ..."). Less conversational; these are decisions, not narration. Species scoring uses the granular per-spot derived profiles in the species modules (N, tide split, time bins, months, gear) — not generic seasonal logic.
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
- New species/spot = copy the template block in the relevant file, fill, commit.
