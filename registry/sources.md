# DATA PULL PLAYBOOK

last_updated: 2026-09-10
confidence: FishNotify pattern TESTED 2026-08-23 (Pacifica, live same-day data); NOAA API established across many sessions

## Rules
- ALWAYS live data for any conditions question. No seasonal guessing.
- Never derive gear/bait/species/tide claims from summary CSVs (Bay_Area_Fishing_Insights_Rules.csv, *_Analysis.csv). Raw *_filtered.csv only, dedup on Fishbrain_ID.
- Date every source. Facebook: verify original upload date (reposts hide age).

## Tides (Bay interior — primary) [UPDATED 2026-08-23, dry-run tested]
PRIMARY: cached monthly table in repo — data/tides_9414290_YYYY-MM.md. Read it, apply per-spot offsets from spots.md. Zero live calls.
MONTHLY REFRESH: web_search "usharbors san francisco tides" → web_fetch result → full month table in ONE fetch → commit new data file.
LIVE FALLBACK (cache missing/stale): same search→fetch. tides4fishing.com secondary.
CONSTRAINT (tested 2026-08-23): the NOAA CO-OPS API URL cannot be fetched directly — the fetch tool rejects URLs not surfaced by a search. Any direct-API pattern must be search-first.

## Coastal composite — FishNotify (TESTED)
One fetch returns: 0-100 score, water temp, wind AM/PM, swell ht/period, wave power, pressure, moon, best-fishing window, 7-day table, local NOAA tide station.
- Pattern: web_search "fishnotify <location> fishing forecast" → web_fetch the result URL.
- CONSTRAINT (re-tested 2026-08-23): constructed URLs are rejected, AND page URLs from earlier fetches/turns do NOT reliably persist as fetchable. Rule: fresh web_search → web_fetch pair per FishNotify spot, per session, no exceptions.
- Coverage: coastal only. Named pages confirmed: Pacifica, Half Moon Bay, Ocean Beach SF, Bodega Bay, Santa Cruz.
- Treat the 0-100 score as ONE input, not a verdict — it doesn't know species or structure. Use the raw variables against our formulas.

## Wind / marine
- NWS marine forecast (SF Bay / coastal waters zones) via web search/fetch.
- NDBC buoys for observed: note TIBC1 (Tiburon) offline as of last check — re-verify.
- Windy/Surfline: dynamic pages may return cached data via fetch; Alex screenshots are more reliable for real-time surf. Say so when uncertain.

## Water temp
- FishNotify (coastal), NDBC buoys, USGS gauges for delta/rivers.

## River (Feather/Sac fall-run module, pending)
- CDEC flow CFS; hatchery passage counts. Schema variant per FRESHWATER_SCHEMA_v1_1.md.

## Visibility / turbidity (added 2026-08-23)
LIVE: USGS NWIS real-time turbidity (FNU, 15-min), 8 SF Bay stations. Confirmed live: Alcatraz 374938122251801. Pattern: web_search "USGS turbidity <station/area>" -> fetch. Caveats: channel sensors UNDERSTATE shallows (USGS: shallows are the most turbid water, wave-driven resuspension); Central Bay stations are a proxy for San Pablo.
ANNUAL PATTERN (REPORTED, USGS-mechanism-backed): turbidity tracks wind season — murky ~late May-summer, clearing fall. Hourly wind forecast doubles as same-day clarity predictor. Rough angler read: <10 FNU decent viz, 10-30 marginal, >30 mud. Calibrate against on-water estimates in session logs.

## Salmon run-timing telemetry — NOAA CalFishTrack (added 2026-09-10)
PROVEN-grade run position: acoustic-tagged adult Chinook (2026: 75 fish, Gulf of Farallones release Jul 27-Aug 27, mean ~714mm) on receiver lines Golden Gate -> Carquinez -> Delta. Unique-fish counts per receiver = direct observation of where the run is.
- Pattern: web_search "CalFishTrack ocean salmon 2026" -> fetch (oceanview.pfeg.noaa.gov/CalFishTrack/pageOceanSalmon_YYYY.html; new page per year). Data typically current as of prior midnight — check page timestamp.
- STANDING STEP: check before ANY salmon session (bay, Benicia, Delta). See OUTLOOK_PROCESS step 4.
- Decision rules:
  1. High release count, low Carquinez detections -> cohort in ocean/bay corridor -> bay troll (Raccoon Strait / California City) + Benicia are the play.
  2. Carquinez detections climbing -> Benicia/Dillon pulse active/imminent; cross-check First St daily counts.
  3. Upper-Delta/Sac receivers lighting up -> Steamboat Slough / Walnut Grove trigger; shift effort upstream.
  4. Upstream saturated, Carquinez flat -> main body passed; wind down bay/Benicia effort.
- NOT evidence of: feeding/bite, or which bay corridor (Raccoon Strait vs main channel — unresolved).
- Caveats: ~75-fish sample, single nearshore release site, receiver ranges vary. Percentages indicative.
- Baseline 2026-09-10: 29/27 unique fish at Carquinez lines (~38% of cohort), 0 at all upstream Delta receivers.

## HMB boat-conditions gate (added 2026-10-07, for Discovery 400 coastal runs)
Pull BOTH, same session: (1) NWS PZZ545 (Pt Reyes-Pigeon Pt coastal, 10nm) via search->fetch (cache-buster ?v=YYYYMMDD; VERIFY issuance date — this feed served week-stale caches twice) for wind kt + combined seas + wave detail; (2) FishNotify Half Moon Bay named page for swell ht/period, wave power kJ, water temp, score. NDBC 46012 (HMB buoy) for observed when forecast and reality need reconciling. Pillar Point bar: NWS SF Bar forecast when crossing matters — ebb against swell is the kill condition.
Discovery 400 nearshore go/no-go STARTING BANDS (GUESS — calibrate against sessions, same protocol as kJ bands):
- GO: wind <=10 kt AND combined seas <=5 ft with dominant period >=8s
- CAUTION (harbor-mouth radius, buddy awareness): 10-15 kt OR 5-7 ft OR period <8s on 4ft+
- NO-GO: >15 kt, >7 ft, or short-period windswell >=5 ft; any small-craft advisory on PZZ545
Season note: NW windswell events define fall; mornings before the gradient fills are the window.

## Tomales Bay conditions gate (added 2026-10-07)
Tides: inside-bay NOAA subordinate stations (Marshall / Inverness / Blakes Landing) via US Harbors or tides4fishing — do NOT apply SF Bay offsets; mid-bay halibut formula keys to LOCAL flood. Wind: NWS PZZ540 (Pt Arena-Pt Reyes coastal) for the mouth + Bodega Bay FishNotify named page (confirmed) for swell/kJ/water temp; inside-bay wind is land-sheltered NW — afternoon fetch builds down the bay axis, mornings are the window. Observed: NDBC 46013 (Bodega). BAR RULE (FG-01): the Tomales bar between Sand Pt and Tomales Pt is a hazard zone — ebb against NW swell breaks; Discovery 400 stays INSIDE the bay, launch Nick's Cove/Miller Park side, no bar crossings. Score mid-bay (Hog Island/Pelican Pt flat-to-channel drift) per species/halibut.md.

## Regs
- CDFW via web search, every session, per species. Shore exemptions ≠ boat regs.
