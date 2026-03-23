---
name: miljodir
description: Query environmental geodata from the Norwegian Environment Agency (Miljødirektoratet). Use this skill ALWAYS when the user asks about Norwegian protected areas (verneområder, naturvernområder), nature types (naturtyper), NiN mapping, contaminated ground/seabed (forurenset grunn/sjøbunn), outdoor recreation areas (friluftslivsområder), noise maps (støykart), large predators (rovdyr — ulv, bjørn, jerv, gaupe, kongeørn), species data, environmental monitoring, or any Miljødirektoratet spatial data. Also trigger on kart.miljodirektoratet.no, naturbase, Miljøstatus, NiN, or Norwegian environmental maps.
---

# Miljødirektoratet — Environmental Geodata (ArcGIS)

**Base URLs:**

| Server | Folders | Content Focus |
|---|---|---|
| `kart.miljodirektoratet.no/arcgis/rest/services` | 7 folders | Core: NiN, noise, sensitive species, environmental status |
| `kart2.miljodirektoratet.no/arcgis/rest/services` | 22 folders | Extended: outdoor recreation, predators, contamination, portal |
| `kart3.miljodirektoratet.no/arcgis/rest/services` | 4 folders | Nature measures, noise, test |
| `image001.miljodirektoratet.no/arcgis/rest/services` | 7 folders | Raster: green infrastructure, species hotspots, forest, predators |

**Auth:** None for public services.
**Protocol:** ArcGIS REST API — MapServer and FeatureServer.
**License:** NLOD — credit Miljødirektoratet.
**Rate limit:** No documented rate limit. Be reasonable.

## Service Discovery

```bash
# List folders on each server
curl -s "https://kart.miljodirektoratet.no/arcgis/rest/services?f=json"
curl -s "https://kart2.miljodirektoratet.no/arcgis/rest/services?f=json"

# List services in a folder
curl -s "https://kart2.miljodirektoratet.no/arcgis/rest/services/rovbase?f=json"

# Service info (layers, fields)
curl -s "https://kart.miljodirektoratet.no/arcgis/rest/services/miljostatus/kalket_laksevassdrag/MapServer?f=json"
```

## Key Service Folders

### kart.miljodirektoratet.no
| Folder | Content |
|---|---|
| `miljostatus` | Environmental status — limed salmon rivers, industrial emissions |
| `nin_innsyn` | NiN (Nature in Norway) flat structure mapping |
| `nin_landskapstyper` | NiN landscape types |
| `sensitive_artsdata` | Sensitive species data (restricted) |
| `stoy` | Strategic noise maps (rail, road) |

### kart2.miljodirektoratet.no
| Folder | Content |
|---|---|
| `forurenset_sjobunn` | Contaminated seabed |
| `friluftsliv_ferdselsarer` | Outdoor recreation trails coverage |
| `rovbase` | Large predator data (wolf, bear, wolverine, lynx, eagle) |
| `naturovervaking` | Nature monitoring |
| `nin_skogkartlegging` | NiN forest mapping |
| `svalbard` | Svalbard environmental data |

### image001.miljodirektoratet.no
| Folder | Content |
|---|---|
| `gronn_infrastruktur` | Green infrastructure |
| `hotspots_arter` | Species hotspot maps |
| `naturskog` | Natural forest |
| `rovbase` | Predator raster data |

## Query Examples

```bash
# Query a MapServer layer
curl -s "https://kart.miljodirektoratet.no/arcgis/rest/services/miljostatus/kalket_laksevassdrag/MapServer/0/query?where=1%3D1&outFields=*&f=json&resultRecordCount=5&returnGeometry=false"

# Spatial query — features within envelope
curl -s "https://kart.miljodirektoratet.no/arcgis/rest/services/miljostatus/kalket_laksevassdrag/MapServer/0/query?geometry={minLon},{minLat},{maxLon},{maxLat}&geometryType=esriGeometryEnvelope&inSR=4326&spatialRel=esriSpatialRelIntersects&outFields=*&f=json&resultRecordCount=10"
```

## ArcGIS REST Query Parameters

| Parameter | Example | Description |
|---|---|---|
| `where` | `1=1` | SQL where clause |
| `outFields` | `*` or `OBJECTID,NAVN` | Fields to return |
| `f` | `json` | Output format |
| `returnGeometry` | `false` | Omit geometry for faster response |
| `resultRecordCount` | `10` | Max records to return |
| `geometry` | `{lon},{lat}` | Point or envelope for spatial query |
| `geometryType` | `esriGeometryPoint` | Type of geometry filter |
| `inSR` | `4326` | Input spatial reference (WGS84) |
| `spatialRel` | `esriSpatialRelIntersects` | Spatial relationship |
| `outSR` | `4326` | Output spatial reference |

## Notes

- Some services are FeatureServer (editable) and MapServer (read-only) — use MapServer for queries
- Sensitive species data requires special authorization
- Layer IDs start at 0 — check service info for available layers
- Default output CRS varies — always specify `outSR=4326` for WGS84
