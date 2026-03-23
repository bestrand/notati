---
name: hybasapi
description: Query Norwegian bathymetry survey coverage data via Kartverket's OGC API. Use this skill ALWAYS when the user asks about seabed surveys, bathymetry (batymetri), depth data (dybdedata), multibeam surveys, seabed mapping (sjøbunnkartlegging), or survey coverage in Norwegian waters. Also trigger when user mentions hybasapi, dybdedata, sjømåling, or asks about surveyed areas in fjords or along the Norwegian coast.
---

# Hybasapi — Norwegian Bathymetry Survey API

**Base URL:** `https://hybasapi.atgcp1-prod.kartverket.cloud`
**Auth:** None.
**License:** CC BY 4.0 — credit "© Kartverket".
**Attribution:** "Inneholder data fra Kartverket, lisensiert under Creative Commons Attribution 4.0 (CC BY 4.0)."
**Rate limit:** No documented limit. Be reasonable.
**Format:** OGC API Features — returns GeoJSON.

## Collections

One collection available: `surveys` — geographical polygons delimiting bathymetry survey areas.

```bash
curl -s "https://hybasapi.atgcp1-prod.kartverket.cloud/collections/surveys?f=json"
```

## Query survey coverages

```bash
curl -s "https://hybasapi.atgcp1-prod.kartverket.cloud/collections/surveys/items?f=json&limit=10"
```

**Parameters:**
- `f=json` — GeoJSON output
- `limit` — max features per page (default 10)
- `offset` — pagination offset
- `bbox` — bounding box filter: `minlon,minlat,maxlon,maxlat` (WGS84)
- Property filters: `year=2019`, `data_owner=Kartverket`, `survey_method=multibeam`

### Bbox example

```bash
curl -s "https://hybasapi.atgcp1-prod.kartverket.cloud/collections/surveys/items?f=json&limit=5&bbox={MINLON},{MINLAT},{MAXLON},{MAXLAT}"
```

## Response fields

```json
{
  "type": "Feature",
  "properties": {
    "survey_id": "...",
    "data_owner": "...",
    "year": 2019,
    "date_start": "2019-01-31",
    "date_end": "2019-02-16",
    "depth_sensor": "...",
    "logged_backscatter": true,
    "logged_water_column": false,
    "survey_method": "multibeam",
    "total_area": 13.0,
    "normalised_area": 56.26,
    "quality_specification": "...",
    "resolution": "1",
    "modeled": true,
    "data_quality": 1,
    "target_resolution": "1"
  },
  "geometry": { "type": "Polygon", "coordinates": [...] }
}
```

**Key fields:** `survey_method` (multibeam, singlebeam, lidar), `data_owner` (Kartverket, Kystverket, etc.), `year`, `resolution` (metres), `depth_sensor` (echosounder model), `data_quality` (1=best).

## Queryable properties

```bash
curl -s "https://hybasapi.atgcp1-prod.kartverket.cloud/collections/surveys/queryables?f=json"
```

All filterable: `survey_id`, `data_owner`, `year`, `date_start`, `date_end`, `depth_sensor`, `logged_backscatter`, `logged_water_column`, `survey_method`, `total_area`, `normalised_area`, `quality_specification`, `resolution`, `modeled`, `financier`, `data_quality`, `target_resolution`.

## Common recipes

```bash
# Surveys in a bbox
/collections/surveys/items?f=json&limit=20&bbox={MINLON},{MINLAT},{MAXLON},{MAXLAT}

# Recent high-resolution multibeam surveys
/collections/surveys/items?f=json&limit=20&survey_method=multibeam&year=2023

# Surveys by a specific data owner
/collections/surveys/items?f=json&limit=20&data_owner=Kystverket

# Single survey by ID
/collections/surveys/items/{SURVEY_ID}?f=json
```

## Notes

- Geometries are WGS84 polygons showing the surveyed area boundaries.
- This API covers survey **metadata and coverage** — not raw depth values. For actual depth point data, see Kartverket's download services.
- Hundreds of surveys available in any given coastal area.
