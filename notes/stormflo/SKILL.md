---
name: stormflo
description: Query storm surge and sea level rise consequence data from Kartverket. Use this skill ALWAYS when the user asks about stormflo, havnivåstigning, sea level rise in Norway, coastal flooding risk, storm surge consequences per municipality, oversvømmelse fra havet, flooded buildings/roads/areas in Norwegian coastal municipalities, or climate adaptation for coastal areas. Also trigger on stormflo-konsekvens, havnivå, or Kartverket sea level data.
---

# Stormflo — Storm Surge & Sea Level Rise Consequences API

**Base URL:** `https://stormflo-konsekvens.kartverket.no/public/api/v1`
**Auth:** None.
**License:** NLOD — credit Kartverket.
**Rate limit:** No documented rate limit. Be reasonable.
**Data:** Statistics on buildings, roads, and areas at risk from storm surge and sea level rise per municipality.
**OpenAPI spec:** `https://stormflo-konsekvens.kartverket.no/openapi.yaml`

## Endpoints

```bash
# Per municipality (use 4-digit kommunenummer)
# Per municipality (use 4-digit kommunenummer)
curl -s "https://stormflo-konsekvens.kartverket.no/public/api/v1/{kommunenummer}.json"

# All municipalities at once (~1.4 MB)
curl -s "https://stormflo-konsekvens.kartverket.no/public/api/v1/alle_kommuner.json"

# National totals
curl -s "https://stormflo-konsekvens.kartverket.no/public/api/v1/national.json"
```

## Per-Municipality Response

Returns array of scenarios (typically 12 combinations of return period × year):

```json
{
  "kommunenummer": "{kommunenummer}",
  "code": "1000ymax",
  "year": "2025",
  "offentlige_bygg": 86,
  "privat_naering": 186,
  "private_bygninger": 2482,
  "bygning_total": 2909,
  "samfunnskritiske_bygg": 0,
  "annenbygning": 155,
  "veilengde_privat": 4077,
  "veilengde_offentlig": 7628,
  "veilengde_total": 11705,
  "bebyggelse": 242660,
  "natur": 1076219,
  "offentlige_anlegg": 0,
  "primarnaering": 38165,
  "areal_total": 1357044
}
```

## Scenario Codes

| Code | Description |
|---|---|
| `mhw` | Mean high water (middel høyvann) |
| `20ymax` | 20-year return period storm surge |
| `200ymax` | 200-year return period storm surge |
| `1000ymax` | 1000-year return period storm surge |
| `highendwl` | High-end sea level rise scenario |

## Year Values

| Year | Meaning |
|---|---|
| `2025` | Current sea level |
| `2100` | Projected sea level 2100 |
| `2150` | Projected sea level 2150 |

## National Response Structure

```json
{
  "highendwl": {
    "2100": { "bygning_total": 272945, "veilengde_total": 4533683, ... },
    "2150": { ... },
    "2025": { ... }
  },
  "mhw": { ... },
  "20ymax": { ... },
  "200ymax": { ... },
  "1000ymax": { ... }
}
```

## Response Fields

| Field | Description |
|---|---|
| `offentlige_bygg` | Public buildings affected |
| `privat_naering` | Private commercial buildings |
| `private_bygninger` | Private residential buildings |
| `bygning_total` | Total buildings affected |
| `samfunnskritiske_bygg` | Critical infrastructure buildings |
| `veilengde_privat` | Private road length (meters) |
| `veilengde_offentlig` | Public road length (meters) |
| `veilengde_total` | Total road length (meters) |
| `bebyggelse` | Built-up area (m²) |
| `natur` | Nature area (m²) |
| `primarnaering` | Primary industry area (m²) |
| `areal_total` | Total affected area (m²) |

## Notes

- Only coastal municipalities have data. Inland municipalities return 404.
- Road lengths are in meters. Area values are in square meters.
- Use `alle_kommuner.json` for comparative analysis across municipalities.
- Data based on DSB guidelines for storm surge planning (2024).
