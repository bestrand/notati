---
name: vannstand
description: Query Norwegian sea level, tide, and water level data from Kartverket. Use this skill ALWAYS when the user asks about tides (tidevann, flo og fjøre), sea level (vannstand, havnivå), water level measurements, tide predictions, high/low tide times, storm surge reference levels, tidal constituents, or any coastal water level data for Norway. Also trigger when user mentions vannstand, sehavnivå, sjøkartnull, Kartverket tideapi, tidevannstabell, or asks about water levels at Norwegian ports/harbors.
---

# Vannstand — Norwegian Sea Level & Tide API

**Base URL:** `https://vannstand.kartverket.no/tideapi.php`
**Auth:** None — open for everybody, free of charge.
**License:** CC BY 4.0 — credit "© Kartverket".
**Attribution:** "Inneholder data fra Kartverket, lisensiert under Creative Commons Attribution 4.0 (CC BY 4.0)."
**Rate limit:** ~20 requests/second shared across all users. Cache static data locally.
**Format:** XML responses (not JSON). Parse with XML tools.

## Key concepts

- **Observations (obs):** Actual measured water levels from tide gauges
- **Predictions (pre):** Modelled tide predictions based on harmonic analysis
- **High/low tide (tab):** Predicted times and heights of high and low water
- **Reference levels:** `cd` = Chart Datum (sjøkartnull), `msl` = Mean Sea Level, `nn2000` = NN2000 vertical datum

## List all permanent tide stations

25+ stations along the Norwegian coast. Get codes, names, and coordinates:

```bash
curl -s "https://vannstand.kartverket.no/tideapi.php?tide_request=stationlist"
```

Returns XML with `<location name="..." code="..." latitude="..." longitude="..." type="PERM"/>` for each station. Use the `code` value in station-specific queries, or use `lat`/`lon` for position-based queries.

## Water level at a position (main query)

```bash
curl -s "https://vannstand.kartverket.no/tideapi.php?tide_request=locationdata&lat={LAT}&lon={LON}&fromtime={YYYY-MM-DDTHH:MM}&totime={YYYY-MM-DDTHH:MM}&datatype=all&refcode=cd&lang=en"
```

**Parameters:**
- `tide_request=locationdata` (required)
- `lat`, `lon` — WGS84 coordinates (API finds nearest station)
- `fromtime`, `totime` — ISO 8601 local time: `YYYY-MM-DDTHH:MM`
- `datatype` — `all` (obs+predictions), `obs` (observations only), `pre` (predictions only), `tab` (high/low tide table)
- `refcode` — `cd` (Chart Datum), `msl` (Mean Sea Level), `nn2000`
- `lang` — `en`, `nb`, `nn`
- `file` — omit for XML, `txt` for text file, `pdf` for PDF

**Response (XML):**
```xml
<tide>
  <locationdata>
    <location name="..." code="..." latitude="..." longitude="..."/>
    <reflevelcode>CD</reflevelcode>
    <data type="observation" unit="cm">
      <waterlevel value="..." time="..." flag="obs"/>
    </data>
    <data type="prediction" unit="cm">
      <waterlevel value="..." time="..." flag="pre"/>
    </data>
  </locationdata>
</tide>
```

## High and low tide times

Use `datatype=tab` to get only the turning points:

```bash
curl -s "https://vannstand.kartverket.no/tideapi.php?tide_request=locationdata&lat={LAT}&lon={LON}&fromtime={YYYY-MM-DDTHH:MM}&totime={YYYY-MM-DDTHH:MM}&datatype=tab&refcode=cd&lang=en"
```

Returns entries with `flag="high"` or `flag="low"` — typically 4 per day (2 highs, 2 lows).

## Standard reference levels for a station

```bash
curl -s "https://vannstand.kartverket.no/tideapi.php?tide_request=standardlevels&stationcode={CODE}&lang=en"
```

## Observation time span for a station

```bash
curl -s "https://vannstand.kartverket.no/tideapi.php?tide_request=obstime&stationcode={CODE}&lang=en"
```

Returns first and last observation timestamps — useful to know data availability.

## Other metadata endpoints

```bash
# Available languages
curl -s "https://vannstand.kartverket.no/tideapi.php?tide_request=languages"

# File formats
curl -s "https://vannstand.kartverket.no/tideapi.php?tide_request=fileformats"

# Tidal constituents for a station
curl -s "https://vannstand.kartverket.no/tideapi.php?tide_request=constituents&stationcode={CODE}&lang=en"
```

## Workflow

1. Call `tide_request=stationlist` to find station codes and coordinates
2. Use `lat`/`lon` or `stationcode` to query `locationdata` for the area of interest
3. Use `datatype=tab` for tide tables, `datatype=obs` for measured levels, `datatype=pre` for predictions

## Notes

- Values are in **centimetres** relative to the chosen reference level.
- Observations typically have ~10 min resolution; predictions are hourly.
- The `flag` attribute indicates: `obs` = observation, `pre` = prediction, `high`/`low` = tide turning point.
- For storm surge analysis, compare `obs` values against `pre` values — the difference is the surge component.
- Observation data availability varies by station; check with `obstime` query. Some stations have data from the 1980s.
