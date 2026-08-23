# DATA PULL PLAYBOOK

last_updated: 2026-08-23
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

## Regs
- CDFW via web search, every session, per species. Shore exemptions ≠ boat regs.
