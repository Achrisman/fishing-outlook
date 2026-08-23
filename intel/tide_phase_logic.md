# TIDE-PHASE SEQUENCING LOGIC (flats/channel spots)

last_updated: 2026-08-23
data_basis: Temple Smok framework (CSBF, Jul 2026); Chilly Chill McNears transcript (filmed ~Aug 1 2026, logged 8/23); derived profiles (31,323 deduped records); Marin pier session analyses (Mar 2026)
confidence: per-cell, marked with an evidence grade:
- PROVEN — backed by our own data (raw CSV counts or logged sessions)
- REPORTED — credible first-person source and/or partial data agreement; not yet tested by us
- GUESS — untested read/hypothesis; fish it as an experiment (one hour, not four)
Promotion: log every session as phase x structure x result (blanks included) + clarity estimate. At ~8 trials per claim: producing -> promote toward PROVEN; blanking -> delete the line.
purpose: turn a day's tide curve into a time-sequenced decision read: "phase X until HH:MM → species on structure with presentation, then phase Y..."

## Core mechanic (REPORTED — Temple Smok, corroborated by McNears ebb-59% derivation)
Rising water pushes bait and stripers UP onto flats and rock edges adjacent to flats. Falling water pulls them OFF the flats into channels, points, and current wraps. Halibut are ambush feeders that tolerate soft water — they own the slack windows on depth-transition shelves, clarity permitting. Slack is dead for current-feeders (stripers, sharks/rays) — never plan a striper window across slack.

## Generic phase table (San Pablo Bay flats/channel spots)
Phases keyed to LOCAL extremes (apply spot offset first). L=low, H=high.

| Phase | Clock | Species → Structure | Presentation | Grade |
|---|---|---|---|---|
| Early flood | L+0 → L+2h | Low % — stripers staging channel edges | Bait soak (anchovy/grass shrimp); hold fire on lures | REPORTED |
| Mid-late flood | L+2h → H-1h | STRIPERS on flats / rock edges adjacent to flats; work mud-line seams (current convergence, not bathymetry) | White paddle tail category, cast up-current, bottom-contact swing. Murky: chartreuse/underspin flash (Chilly Chill 1oz Cool Baits) | REPORTED |
| High slack | H-1h → H+1h | HALIBUT on depth-transition shelf (8–15ft McNears; 10–40ft general), clarity-gated. Sturgeon at pier ends | Halibut: slow paddle tail / live smelt drift. Sturgeon: bait on bottom | REPORTED (halibut tide-neutral PROVEN at Paradise) |
| Ebb | H+1h → L-1h | STRIPERS in channels, off points, current wraps/eddy seams | Same paddle tail worked in the moving water at channel edges; jerkbait sweep at pier tips | PROVEN at McNears (ebb 59%, N=29) |
| Low slack | L-1h → L+1h | Perch/bait fish only; re-rig, move, eat | — | REPORTED |

Modifiers (apply on top):
- CLARITY GATE: San Pablo Bay murky ~late May–summer (wind-driven, both sides); clears in fall. Murk kills the halibut slack window and downgrades all sight presentations → flash/vibration/scent. (REPORTED — Chilly Chill; test as standing variable)
- SEASON GATE: bay striper density builds Aug, thick Sept–Nov (REPORTED — Chilly Chill + our Sept–Oct CSV signal; visibility confound unresolved)
- TIME×PHASE: when phase logic and time-of-day signal conflict, time wins at Loch Lomond (PROVEN) and Stinson (PROVEN); phase wins at McNears until data says otherwise (GUESS)

## Spot instantiation — McNears / Rat Rock (flagship, verify here first)
Offset: ~HW+20/LW+30 vs 9414290 (approx — needs its own offset verification pass).
Micro-structure map (Chilly Chill + Navionics):
- A. Pier-start cast toward near point — flood lane (REPORTED)
- B. Rocky point/jetty adjacent to pier — his producer; works both phases as the edge structure (REPORTED)
- C. Far point, end of McNears Beach — current wrap + eddy; EBB structure; his Sept–Nov call (GUESS — priority verification target)
- D. Right-side shoreline by quarry inlet — persistently murky, skip (REPORTED)
- E. Pier end — sturgeon, side switches with tide direction (GUESS — reputation only)
- Rat Rock flats (shore SW of pier) — mid-flood striper flat per core mechanic (GUESS — Alex's 3/xx blank session was on a bathymetry seam, not a convergence seam)
Derived overlay (N=29 stripers): ebb 59% | midday-PM-dusk | Nov/Jan/Apr months | grass shrimp top bait (KIT GAP).

## Verification protocol (how lines move GUESS -> REPORTED -> PROVEN)
Every session at a sequenced spot logs: date, phase(s) fished (by local tide clock), structure letter (A–E etc.), presentation, result INCLUDING BLANKS, clarity estimate (inches). One row per phase×structure combo fished. Blanks at a claimed-hot cell are as valuable as fish. Review claims at ~8 trials: promote or delete. Log destination: Alex_Personal_Catch_Log.csv (sessions) + this file's grade labels (updated on review).

## Outlook rendering requirement
When a sequenced spot makes an outlook, render the day as a phase timeline in decision form, e.g.:
"Flood until 12:40p — stripers on the Rat Rock flats / point B edge, white paddle tail up-current. 12:40–2:10p high slack — halibut on the 8–15ft shelf off the pier IF clarity >1ft, else bait soak. Ebb from 2:10p — channel stripers at point C wrap, work the eddy seam; grass shrimp on the soak rod."
