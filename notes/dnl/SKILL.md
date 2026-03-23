---
name: dnl
description: Query Norwegian maritime navigation data from Den norske los (DNL) at Kartverket. Use this skill ALWAYS when the user asks about Norwegian harbors, anchorages (ankringsplasser), lighthouses (fyrtårn, fyrstasjon), sailing routes, waypoints, pilot boarding areas (losbordsplasser), sea navigation in Norway, coastal navigation, VTS areas, or Den norske los data. Also trigger on dnl.kartverket.no, losordningen, Norwegian maritime infrastructure, or coastal waypoints.
---

# DNL — Den norske los (Maritime Navigation API)

**Base URL:** `https://dnl.kartverket.no/api`
**Auth:** None.
**Protocol:** OGC API Features (REST/JSON).
**License:** NLOD — credit Kartverket.
**Rate limit:** No documented rate limit. Be reasonable.

## Collections

```bash
# List all collections
curl -s "https://dnl.kartverket.no/api/collections?f=json"
```

**16 collections available** (Norwegian + English versions):

| Collection | Items | Description |
|---|---|---|
| `anchorage` | 1,085 | Anchorage areas (ankringsplasser) |
| `lighthouse` | 3 | Lighthouses (fyrtårn/fyrstasjoner) |
| `landfall` | — | Landfall points |
| `place` | — | Named places along the coast |
| `sailing` | — | Sailing routes/descriptions |
| `routes` | — | Navigation routes |
| `waypoints` | — | Route waypoints |
| `losboarding` | — | Pilot boarding positions |
| `vts_lines` | — | VTS area boundaries |
| `vts_points` | — | VTS reporting points |
| `dangerWave` | — | Dangerous wave areas |

English versions: `anchorage_en`, `lighthouse_en`, `landfall_en`, `place_en`, `sailing_en`

## Query Features

```bash
# Get anchorages (paginated)
curl -s "https://dnl.kartverket.no/api/collections/anchorage/items?f=json&limit=10"

# Spatial filter — bounding box
curl -s "https://dnl.kartverket.no/api/collections/anchorage/items?f=json&limit=10&bbox={minLon},{minLat},{maxLon},{maxLat}"

# Single item by ID
curl -s "https://dnl.kartverket.no/api/collections/anchorage/items/{id}?f=json"
```

## Anchorage Properties

```json
{
  "title": "{anchorage_name}",
  "county": "{county}",
  "municipality": "{municipality}",
  "objtype": "Vik i sjø",
  "description": "<p>HTML description with navigation details</p>",
  "seabed": [],
  "mooring": ["bolt"],
  "date_updated": "2024-08-29T08:27:18.902000",
  "ssr": "{ssr_id}"
}
```

## Lighthouse Properties

```json
{
  "title": "{lighthouse_name}",
  "municipality": "{municipality}",
  "objtype": "Fyrstasjon",
  "description": "<p>Description of the lighthouse</p>",
  "date_updated": "2024-12-09T12:13:19.312000",
  "ssr": "{ssr_id}"
}
```

## Pagination

- Use `limit` (page size) and `offset` (skip N items)
- Default limit is 10
- `numberMatched` in response gives total count
- Example: `?limit=50&offset=100` for items 101–150

## Notes

- Descriptions contain HTML markup — strip tags for plain text
- The `ssr` field is the SSR (Sentralt stedsnavnregister) reference number
- Geometry is GeoJSON Points (WGS84 / EPSG:4326)
- English collections (`_en`) contain translated content
- Combine with `geonorge` for place name lookups and address search
- The web application at `dnl.kartverket.no` provides a map interface for browsing
