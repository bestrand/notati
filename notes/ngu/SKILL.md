---
name: ngu
description: Query geological data from NGU (Norges geologiske undersøkelse — Geological Survey of Norway). Use this skill ALWAYS when the user asks about Norwegian geology, radon risk (radonaktsomhet), loose deposits (løsmasser), marine sediments, seabed conditions, marine boundary (marin grense), groundwater boreholes (grunnvannsborehull), anchoring conditions on seabed, gas seepage (gassoppkommer), quick clay areas, or any geological survey data in Norway. Also trigger on geo.ngu.no, NGU, kvartærgeologi, berggrunn, or Norwegian geological maps.
---

# NGU — Geological Survey of Norway (OGC API Features)

**Base URL:** `https://geo.ngu.no/api/features`
**Auth:** None.
**License:** NLOD — credit Norges geologiske undersøkelse (NGU).
**Rate limit:** No documented rate limit. Be reasonable.
**Protocol:** OGC API Features — GeoJSON responses.

## API Catalog

```bash
# List all available datasets
curl -s "https://geo.ngu.no/api/features/"
```

**19 datasets available:**

| Dataset ID | Title | Description |
|---|---|---|
| `radonaktsomhet` | Radon aktsomhet | Radon risk classification for all of Norway |
| `maringrense` | Marin grense | Marine boundary — highest historical sea level after last ice age |
| `muligmarinleire` | Mulighet for marin leire | Probability of marine clay (relevant for quick clay) |
| `losmassedetaljert` | Løsmasser detaljert | Detailed surface deposit maps |
| `losmasselokalt` | Løsmasser lokalt | Local surface deposit maps |
| `losmasseregionalt` | Løsmasser regionalt | Regional surface deposit maps |
| `ankringsforhold` | Ankringsforhold | Seabed anchoring conditions |
| `bunnfellingsomrader` | Bunnfellingsområder | Seabed deposition areas |
| `bunnsedimentdannelse` | Bunnsedimentdannelse | Seabed sediment formation type |
| `bunnsedimentkornstorrlse` | Bunnsedimentkornstørrelse | Seabed sediment grain size |
| `gassoppkommer` | Gassoppkommer | Gas seepage locations on seabed |
| `gravbarhet` | Gravbarhet | Seabed excavatability |
| `grunngass` | Grunn gass | Shallow gas in seabed |
| `marinlandskap` | Marine landskap | Marine landscape classification |
| `sedimentmiljo` | Marine sedimentasjonsmiljø | Marine sedimentation environment |
| `skjellsandomrader` | Skjellsandområder | Shell sand areas |
| `aktsomhetsvakhetssonerfjell` | Aktsomhet svakhetssoner i fjell | Bedrock weakness/fault zones |
| `grunnvannborehull` | Grunnvannsborehull | Groundwater boreholes |
| `grunnundersokelser_utvidet` | NADAG — utvidet full modell | Geotechnical ground investigation database (26 sub-collections) |

## Querying

Each dataset has one or more collections. List them first, then query items.

```bash
# List collections in a dataset
curl -s "https://geo.ngu.no/api/features/radonaktsomhet/collections?f=json"

# Query features with bounding box (minLon,minLat,maxLon,maxLat)
curl -s "https://geo.ngu.no/api/features/radonaktsomhet/collections/radonaktsomhet/items?f=json&limit=10&bbox={minLon},{minLat},{maxLon},{maxLat}"
```

**Pagination:** Use `limit` and `offset`. `numberMatched` gives total count.

## Radon Risk

The most commonly useful dataset. Covers all of Norway.

```bash
curl -s "https://geo.ngu.no/api/features/radonaktsomhet/collections/radonaktsomhet/items?f=json&limit=5&bbox={minLon},{minLat},{maxLon},{maxLat}"
```

**Response:**
```json
{
  "properties": {
    "objtype": "RadonAktsomhet",
    "aktsomhetGrad": "0",
    "aktsomhetGradNavn": "Usikker aktsomhet"
  }
}
```

| aktsomhetGrad | Meaning |
|---|---|
| `0` | Usikker aktsomhet (uncertain) |
| `1` | Lav aktsomhet (low risk) |
| `2` | Moderat aktsomhet (moderate risk) |
| `3` | Høy aktsomhet (high risk) |

## Marine Boundary

The highest historical post-glacial sea level. Areas below this line may have marine clay deposits (relevant for quick clay assessment).

```bash
curl -s "https://geo.ngu.no/api/features/maringrense/collections/maringrense/items?f=json&limit=5&bbox={minLon},{minLat},{maxLon},{maxLat}"
```

## NADAG — Geotechnical Database

The most complex dataset with 26 sub-collections covering boreholes, soundings, deformation measurements, and lab results.

```bash
# List NADAG collections
curl -s "https://geo.ngu.no/api/features/grunnundersokelser_utvidet/collections?f=json"
```

Key sub-collections: `geotekniskborehull`, `dynamisksondering`, `deformasjonmaling`, `laboratorieundersokelsedata`.

## Collection Names

Most datasets follow the pattern where the main data collection has the same name as the dataset. Supporting collections include:
- `fiktivdelelinje` — fictitious boundary lines
- `dataavgrensning` — data coverage area
- `kartbladkant` — map sheet edges
- `dekning` / `dekningsområde` — coverage area

**Always query the collection matching the dataset name for actual data**, not the supporting geometry collections.

## Notes

- Some datasets have sparse coverage — use wide bounding boxes if narrow queries return empty
- Marine/seabed datasets only cover mapped areas (not the entire coastline)
- CRS is EPSG:4326 (WGS84) for bbox queries
- Some queries may be slow on large datasets — use bbox to limit spatial extent
- The NADAG database may have limited data in the OGC API; the full database was previously at nadag.nve.no (now decommissioned)
