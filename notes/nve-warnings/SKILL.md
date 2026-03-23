---
name: nve-warnings
description: Query flood, landslide, and avalanche warnings from NVE (Norges vassdrags- og energidirektorat). Use this skill ALWAYS when the user asks about flood warnings (flomvarsel), landslide warnings (jordskredvarsel), avalanche warnings (snøskredvarsel), hazard levels, warning colors (grønt/gult/oransje/rødt), varsom.no data, or current natural hazard status in Norway. Also trigger on flomfare, skredfare, jord- og flomskredvarsling, or NVE varsling.
---

# NVE Warnings — Flood, Landslide & Avalanche APIs

**Domain:** `api01.nve.no`
**Auth:** None.
**License:** NLOD — credit NVE. **NVE requires that all warning data is presented — do not selectively filter out warnings.**
**Rate limit:** No documented rate limit. Be reasonable.

## Warning Levels

| Level | Color | Meaning |
|---|---|---|
| 1 | Green | No special alert |
| 2 | Yellow | Moderate danger |
| 3 | Orange | Significant danger |
| 4 | Red | Extreme danger |

## 1. Flood Warnings

```bash
# County overview (all counties, highest activity level)
curl -s "https://api01.nve.no/hydrology/forecast/flood/v1.0.6/api/CountyOverview/1"

# Warnings for a county (dayOffset: 0=today, 1=tomorrow, 2=day after)
curl -s "https://api01.nve.no/hydrology/forecast/flood/v1.0.6/api/Warning/County/{countyNr}/{dayOffset}"
# Warnings for a specific county today
curl -s "https://api01.nve.no/hydrology/forecast/flood/v1.0.6/api/Warning/County/{countyNr}/0"

# All current flood warnings
curl -s "https://api01.nve.no/hydrology/forecast/flood/v1.0.6/api/Warning/County/1/0"
```

**County numbers:** Use 2020+ county numbers. Use `1` for national overview. Get valid county numbers from the CountyOverview endpoint.

**Response fields:** `ActivityLevel` (1–4), `WarningText`, `MainText`, `MunicipalityList[]` with individual municipality warnings.

## 2. Landslide Warnings

```bash
# County overview
curl -s "https://api01.nve.no/hydrology/forecast/landslide/v1.0.6/api/CountyOverview/1"

# Warnings per county
curl -s "https://api01.nve.no/hydrology/forecast/landslide/v1.0.6/api/Warning/County/{countyNr}/{dayOffset}"
```

Same structure as flood warnings.

## 3. Avalanche Warnings

```bash
# Avalanche uses a different API version
curl -s "https://api01.nve.no/hydrology/forecast/avalanche/v6.3.0/api/"
```

**Note:** Avalanche warnings are primarily published via varsom.no. The API structure differs from flood/landslide.

## Usage Notes

- **dayOffset:** 0=today, 1=tomorrow, 2=day after tomorrow
- **Always show all warnings** — NVE requires complete presentation of warning data
- If api01.nve.no is blocked, direct users to https://varsom.no for the same information
- Combine with the `nve` skill for energy data and the `nve-geo` skill for hazard zone maps
