---
name: npolar
description: Query Svalbard and Arctic geospatial data from the Norwegian Polar Institute (Norsk Polarinstitutt). Use this skill ALWAYS when the user asks about Svalbard wildlife (walrus, polar bear, reindeer, seabirds), Arctic glaciers (isbreer), sea ice (havis), Svalbard geology, Arctic vegetation, Svalbard nature reserves, or any polar/Arctic spatial data from Norway. Also trigger on geodata.npolar.no, Norsk Polarinstitutt, Svalbard maps, MAREANO polar data, or Arctic research data.
---

# Npolar — Norwegian Polar Institute Geodata (ArcGIS)

**Base URL:** `https://geodata.npolar.no/arcgis/rest/services`
**Auth:** None.
**Protocol:** ArcGIS REST API (MapServer).
**License:** NLOD / CC BY — credit Norsk Polarinstitutt.
**Rate limit:** No documented rate limit. Be reasonable.

## Service Discovery

```bash
# List all folders
curl -s "https://geodata.npolar.no/arcgis/rest/services?f=json"

# List services in Temadata folder
curl -s "https://geodata.npolar.no/arcgis/rest/services/Temadata?f=json"
```

**5 top-level folders:**

| Folder | Content |
|---|---|
| `Temadata` | Thematic data: wildlife, glaciers, geology, nature reserves (26 services) |
| `Basisdata` | Base maps for Svalbard |
| `Basisdata_Intern` | Internal base data |
| `ArcticSDI` | Arctic Spatial Data Infrastructure |
| `Utilities` | Utility services |

## Key Temadata Services

### Wildlife

| Service | Layers | Description |
|---|---|---|
| `F_Marine_Mammals_Svalbard` | 31 layers | Walrus, seals (ringed, bearded, harbour), polar bear |
| `F_Seabirds_Svalbard` | — | Seabird colonies and habitats |
| `F_Reindeer_Population_Areas_Svalbard` | — | Reindeer population areas |
| `F_Reinsdyrhabitat` | — | Reindeer habitat mapping |
| `F_Rypehabitat` | — | Ptarmigan habitat |
| `F_Biogeographic_Zones_Svalbard` | — | Biogeographic zones |
| `F_Vegetation_Map_22cl_Svalbard` | — | Vegetation classification (22 classes) |

### Marine Mammals Layers (31 total)

| Layer ID | Name |
|---|---|
| 0 | Walruses |
| 1 | Walrus - Halout sites |
| 2 | Walrus - Important breeding areas |
| 3 | Walrus - Important foraging areas |
| 4 | Walrus - Distribution areas |
| 5 | Ringed Seal |
| 6 | Ringed Seal - Ice edge zone |
| 7 | Ringed Seal - Important breeding areas |

### Glaciers & Ice

| Service | Content |
|---|---|
| `I_Glacier_Fronts_Svalbard` | Glacier front positions over time |
| `I_Isbreer_Overflatetyper_og_utstrekning_Svalbard` | Glacier surface types and extent |
| `I_Havis_Historiske_isgrenser_Svalbard` | Historical sea ice boundaries |
| `I_Isfrekvens_*` | Sea ice frequency maps |

### Geology

| Service | Content |
|---|---|
| `G_Geologi_Svalbard_Raster` | Geological raster maps |
| `G_Geologi_Svalbard_S250_S750` | Geological maps 1:250k–1:750k |
| `G_Lithostratigraphic_Lexicon_of_Svalbard` | Lithostratigraphic lexicon |
| `G_Geologi_DML` | Digital elevation model |

### Other

| Service | Content |
|---|---|
| `N_Nature_Reserves_Svalbard` | Nature reserves |
| `N_Strandrydding_Svalbard` | Beach cleanup |
| `Society_Svalbard` | Settlements and infrastructure |
| `Transportation_Svalbard` | Transport network |

## Query Examples

```bash
# Get service metadata and layers
curl -s "https://geodata.npolar.no/arcgis/rest/services/Temadata/F_Marine_Mammals_Svalbard/MapServer?f=json"

# Query walrus haul-out sites (layer 1)
curl -s "https://geodata.npolar.no/arcgis/rest/services/Temadata/F_Marine_Mammals_Svalbard/MapServer/1/query?where=1%3D1&outFields=*&f=json&resultRecordCount=10&returnGeometry=true&outSR=4326"

# Spatial query — features near a point
curl -s "https://geodata.npolar.no/arcgis/rest/services/Temadata/F_Marine_Mammals_Svalbard/MapServer/0/query?geometry={lon},{lat}&geometryType=esriGeometryPoint&inSR=4326&spatialRel=esriSpatialRelIntersects&distance=50000&units=esriSRUnit_Meter&outFields=*&f=json&returnGeometry=false"
```

## Notes

- **Coordinate system:** Services may use EPSG:25833 (UTM33N), EPSG:3575, or EPSG:3857. Always specify `outSR=4326` for WGS84 output.
- Some layers may return empty results with `where=1=1` if they require spatial filtering.
- Use `resultRecordCount` to limit results (default may return all features).
- The `Basisdata` folder contains topographic base maps useful as backdrop for thematic overlays.
- For mainland Norway wildlife data, use the `miljodir` skill (Miljødirektoratet rovbase).
