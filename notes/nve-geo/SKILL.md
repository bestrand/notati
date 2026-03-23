---
name: nve-geo
description: Query NVE geospatial hazard data — flood zones (flomsoner), landslide hazard zones (kvikkleire, skredfaresoner), glaciers (breer), wind/solar power maps. Use this skill ALWAYS when the user asks about Norwegian flood zone maps, quick clay zones (kvikkleire), landslide susceptibility areas (aktsomhetsområder), avalanche hazard zones (snøskredfaresoner), glacier data, or NVE spatial hazard data. Also trigger on kart.nve.no, gis3.nve.no, flomsone, skredsone, kvikkleiresone, aktsomhetskart, or NVE GIS data.
---

# NVE-GEO — Geospatial Hazard & Energy Data

NVE's geospatial services are split across two platforms:

| Platform | Base URL | Content |
|---|---|---|
| WFS (GeoNorge) | `https://wfs.geonorge.no/skwms1/wfs.{service}` | Hazard zones, glaciers — GML output |
| ArcGIS | `https://gis3.nve.no/arcgis/rest/services/` | Slope analysis, quick clay, solar/wind — JSON/image output |

**Auth:** None.
**License:** NLOD — credit NVE.
**Rate limit:** No documented rate limit. Be reasonable.

**Note:** The old `kart.nve.no/arcgis` is decommissioned (returns 404). Use `gis3.nve.no` and `wfs.geonorge.no` instead.

## WFS Services on GeoNorge

These are OGC WFS 2.0 services. They return GML by default (no JSON output available).

| WFS Service Name | Feature Types | Content |
|---|---|---|
| `wfs.kvikkleire` | KvikkleireFaresoneAvgr, UtlopOmr, UtlosningOmr, Kartleggingsområde | Quick clay hazard zones |
| `wfs.snoskred` | (snow avalanche hazard zones) | Avalanche zones |
| `wfs.breer` | (glacier extent data) | Glaciers |
| `wfs.aktsomhetsomradeflom` | (flood susceptibility areas) | Flood susceptibility |
| `wfs.aktsomhetsomradejordskred` | (landslide susceptibility) | Landslide susceptibility |
| `wfs.aktsomhetsomradesnoskred` | (avalanche susceptibility) | Avalanche susceptibility |
| `wfs.kvikkleiresoner` | (quick clay zones) | Quick clay zones |
| `wfs.flomsoner` | (mapped flood zones) | Flood zones |
| `wfs.vassdrag` | (waterways/catchments) | River network |

### WFS Query Examples

```bash
# GetCapabilities — discover feature types
curl -s "https://wfs.geonorge.no/skwms1/wfs.kvikkleire?service=WFS&version=2.0.0&request=GetCapabilities"

# Get features (GML output — must specify typeNames)
curl -s "https://wfs.geonorge.no/skwms1/wfs.kvikkleire?service=WFS&version=2.0.0&request=GetFeature&typeNames=app:KvikkleireFaresoneAvgr&count=10"

# Spatial filter — features within a bounding box
curl -s "https://wfs.geonorge.no/skwms1/wfs.kvikkleire?service=WFS&version=2.0.0&request=GetFeature&typeNames=app:KvikkleireFaresoneAvgr&count=10&bbox={minLat},{minLon},{maxLat},{maxLon},urn:ogc:def:crs:EPSG::4258"
```

**Important:** These WFS services return GML/XML only — JSON output is not supported. Parse with an XML parser. The CRS is EPSG:4258 (ETRS89 geographic).

## ArcGIS Services on gis3.nve.no

### Map Services

```bash
# List all services
curl -s "https://gis3.nve.no/arcgis/rest/services?f=json"

# Service info
curl -s "https://gis3.nve.no/arcgis/rest/services/mapservice/AlphaBeta/MapServer?f=json"
```

| Service | Layers | Content |
|---|---|---|
| `mapservice/AlphaBeta` | Utløpspunkt, BetaLine, Skredbane, Beta helning | Slope/runout analysis |
| `mapservice/Solkraft` | Solar energy potential | Solar maps |
| `mapservice/SolkraftView` | Solar energy view | Solar visualization |
| `mapservice/VindkraftView1` | Wind power | Wind energy maps |
| `mapservice/NGUFjellskred` | Large rock avalanches | Rock avalanche data |
| `mapservice/SkredKvikkleireApp_Faktaark` | Quick clay fact sheets | Quick clay detail |

### WMTS Tile Services

| Service | Content |
|---|---|
| `wmts/Flomsoner2` | Flood zone tiles |
| `wmts/Kvikkleire_Jordskred` | Quick clay / landslide tiles |
| `wmts/Bratthet_2024` | Slope steepness |
| `wmts/SvekketIs` | Weakened ice |

### ArcGIS Query Example

```bash
# Query layers (identify features at a point)
curl -s "https://gis3.nve.no/arcgis/rest/services/mapservice/AlphaBeta/MapServer/0/query?where=1%3D1&outFields=*&f=json&resultRecordCount=5&returnGeometry=false"
```

## Combining with Other Skills

- Use `nve-warnings` for real-time flood/landslide/avalanche warnings
- Use `nve` for energy production data (hydropower, wind)
- Use `geonorge` for address/place lookups to get coordinates for spatial queries
