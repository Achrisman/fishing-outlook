# DATA PULL PLAYBOOK

last_updated: 2026-11-25
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
- STALE-CACHE WORKAROUND (verified 2026-10-15): forecast.weather.gov shmrn pages AND tgftp zone .txt files served 4-20-day-stale issuances even with cache-busters (all three zones, same morning). The NDBC mirror of the full Monterey CWF — ndbc.noaa.gov/data/Forecasts/FZUS56.KMTR.html (search-indexed, accepts ?v= cache-buster) — returned the same-morning 2:42a issuance covering every PZZ zone in one fetch. Check issuance date first; if stale, go straight to the NDBC mirror.
- WORKAROUND UPDATE (verified 2026-11-17): the NDBC mirror ITSELF served a Mar-2025 cache (bare URL; cache-busted fetch hit an unattended permission block), and the tgftp full-CWF file (fzus56.kmtr.cwf.mtr.txt) served Apr-2025. What worked: per-zone tgftp files tgftp.nws.noaa.gov/data/forecasts/marine/coastal/pz/pzzNNN.txt — bare fetch may be a day stale, but WITH ?v=YYYYMMDD cache-buster they returned fresh issuances (PZZ540 same-morning 3:12a; PZZ530/545 prior-afternoon). Neither path is reliable alone: try zone .txt + cache-buster first, NDBC mirror second, and ALWAYS verify the issuance line before using any of them.
- WORKAROUND UPDATE (verified 2026-11-20, unattended run): the tgftp per-zone .txt + cache-buster path is PROVENANCE-BLOCKED in unattended cloud runs (the query-string URL isn't search-indexed, so the fetch-approval prompt goes unanswered — same mechanism as the CalFishTrack blocks), and the NDBC mirror again served its Mar-2025 cache. What worked: web_search the zone ID ("PZZ530 San Francisco Bay marine forecast") → fetch the search-indexed forecast.weather.gov/shmrn.php?mz=pzzNNN page. Freshness varied by zone that morning: PZZ545 same-evening issuance (fresh), PZZ530 prior-morning (1 day, usable), PZZ540 3 days stale (fell back to Bodega FishNotify + PZZ545 proxy, flagged). Unattended order of attack: shmrn-via-search FIRST; tgftp cache-busted .txt only when attended; ALWAYS verify the issuance line.
- WORKAROUND UPDATE (verified 2026-11-22, unattended run): shmrn-via-search served PZZ530 3 days stale and PZZ540 11 days stale (PZZ545 was prior-afternoon, usable). What worked for PZZ530: marine.weather.gov/MapClick.php?zoneid=pzzNNN (search-indexed) links a "Text-Only" zone product — MapClick.php?zoneid=PZZNNN&TextType=1 — which served the same-morning 2:28a issuance. For PZZ540 nothing fresh was reachable (shmrn, shmrn+syn, MapClick all stale) → Bodega FishNotify + PZZ545 proxy fallback, flagged. Unattended order now: shmrn-via-search, then MapClick TextType=1 via the MapClick page, then NDBC mirror; ALWAYS verify the issuance line.
- WORKAROUND UPDATE (verified 2026-11-24, unattended run): shmrn-via-search served PZZ530 fresh (prior-evening 8:38p issuance) but PZZ545 3 days stale; the BARE tgftp zone .txt (no cache-buster) fetched unattended without a provenance block and was the freshest PZZ545 available (11/22 8:41p — 2 days, usable as provisional). PZZ540 again had nothing fresh anywhere (shmrn 11/11, MapClick 11/1, MapClick TextType=1 10/10, tgftp 11/21, NDBC mirror still its Mar-2025 cache) → Bodega FishNotify + PZZ545 proxy fallback, third run straight. Unattended order of attack now: shmrn-via-search first, bare tgftp .txt second (check issuance; often fresher than shmrn for 545), MapClick TextType=1 third; treat PZZ540 as structurally unreliable and plan on the Bodega-FishNotify fallback.
- WORKAROUND UPDATE (verified 2026-11-25, unattended run): bare tgftp zone .txt (no cache-buster, fetched via subagent after a zone-ID web search) was the FRESHEST source for ALL THREE zones in one run — PZZ530 2:20p 11/24, PZZ545 9:03p 11/24, PZZ540 2:20p 11/24 — including the first fresh PZZ540 since 11-17 (shmrn served it 14 days stale, 11/11). shmrn-via-search was staler for every zone (530 1 day, 545 ~19h behind the .txt). No provenance blocks. Unattended order of attack now: bare tgftp .txt FIRST (search the zone ID so the domain is indexed, then fetch the .txt), shmrn-via-search second, MapClick TextType=1 third; Bodega-FishNotify fallback for PZZ540 only if all three fail. ALWAYS verify the issuance line.
- WORKAROUND UPDATE (verified 2026-11-26, unattended run): bare tgftp .txt was 2 days stale for BOTH PZZ530 and PZZ540 (11/24 2:20p), and shmrn-via-search was staler still (530: 11/23 8:38p; 540: 11/11). What worked for PZZ530: the MapClick MAIN page — marine.weather.gov/MapClick.php?zoneid=pzz530 (search-indexed) — served the same-morning 2:42a issuance, 10 periods through Mon night. CAUTION: its TextType=1 link was OLDER (11/22), and MapClick.php?zoneid=pzz540 returned PZZ530/point content mislabeled — verify the zone name in the header before attributing. PZZ540 again had nothing fresh anywhere → Bodega-FishNotify + PZZ545 proxy fallback (4th run). PZZ545 freshest via shmrn (11/25 2:25p; tgftp 11/24 9:03p). Unattended order of attack now: try bare tgftp .txt AND the MapClick main page, use whichever issuance is newer (shmrn third); ALWAYS verify the issuance line and zone header.
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
PLATE ANCHOR (verified 2026-11-25, transom builder's plate photo from Alex): True Kit Discovery 400, CE Design Category C (EN ISO 6185, 2013/53/EU) — certified to significant seas ≤2 m (~6.6 ft) and wind ≤ Force 6 (~27 kt); max 15 kW / 20 hp; 6 persons; max total load 700 kg (1540 lbs); tube 0.2 bar / floor 0.45 bar; serial NZ-NZK14009E525. Read: the sea-state bands below sit INSIDE the plate rating (NO-GO >7 ft ≈ the Cat-C ceiling), so they are rating-aligned, not over-cautious; the 15-kt wind NO-GO is deliberately stricter than the F6 plate limit (solo-ops margin for a 45-kg hull — the one band with documented headroom to calibrate upward via logged sessions). A design category certifies non-breaking water with competent crew; it does NOT extend to bars/surf transition zones (FG-01 exclusion stands independent of the plate).
Discovery 400 nearshore go/no-go STARTING BANDS (GUESS — calibrate against sessions, same protocol as kJ bands):
- GO: wind <=10 kt AND combined seas <=5 ft with dominant period >=8s
- CAUTION (harbor-mouth radius, buddy awareness): 10-15 kt OR 5-7 ft OR period <8s on 4ft+
- NO-GO: >15 kt, >7 ft, or short-period windswell >=5 ft; any small-craft advisory on PZZ545
Season note: NW windswell events define fall; mornings before the gradient fills are the window.

## Tomales Bay conditions gate (added 2026-10-07)
Tides: inside-bay NOAA subordinate stations (Marshall / Inverness / Blakes Landing) via US Harbors or tides4fishing — do NOT apply SF Bay offsets; mid-bay halibut formula keys to LOCAL flood. Wind: NWS PZZ540 (Pt Arena-Pt Reyes coastal) for the mouth + Bodega Bay FishNotify named page (confirmed) for swell/kJ/water temp; inside-bay wind is land-sheltered NW — afternoon fetch builds down the bay axis, mornings are the window. Observed: NDBC 46013 (Bodega). BAR RULE (FG-01): the Tomales bar between Sand Pt and Tomales Pt is a hazard zone — ebb against NW swell breaks; Discovery 400 stays INSIDE the bay, launch Nick's Cove/Miller Park side, no bar crossings. Score mid-bay (Hog Island/Pelican Pt flat-to-channel drift) per species/halibut.md.

## Regs
- CDFW via web search, every session, per species. Shore exemptions ≠ boat regs.
