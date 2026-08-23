# DATA PULL PLAYBOOK

last_updated: 2026-08-23
confidence: FishNotify pattern TESTED 2026-08-23 (Pacifica, live same-day data); NOAA API established across many sessions

## Rules
- ALWAYS live data for any conditions question. No seasonal guessing.
- Never derive gear/bait/species/tide claims from summary CSVs (Bay_Area_Fishing_Insights_Rules.csv, *_Analysis.csv). Raw *_filtered.csv only, dedup on Fishbrain_ID.
- Date every source. Facebook: verify original upload date (reposts hide age).

## Tides (Bay interior — primary)
NOAA CO-OPS API, station 9414290:
`https://api.tidesandcurrents.noaa.gov/api/prod/datagetter?product=predictions&datum=MLLW&time_zone=lst_ldt&interval=hilo&units=english&station=9414290&format=json&begin_date=YYYYMMDD&end_date=YYYYMMDD`
Apply per-spot offsets from spots.md. Fallback: tides4fishing.com.
Note: bash network here cannot reach NOAA — pull via web_fetch, not curl.

## Coastal composite — FishNotify (TESTED)
One fetch returns: 0-100 score, water temp, wind AM/PM, swell ht/period, wave power, pressure, moon, best-fishing window, 7-day table, local NOAA tide station.
- Pattern: web_search "fishnotify <location> fishing forecast" → web_fetch the result URL.
- CONSTRAINT (tested): constructed URLs are rejected by the fetch tool — must search first or use a URL already in-conversation.
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

## Regs
- CDFW via web search, every session, per species. Shore exemptions ≠ boat regs.
