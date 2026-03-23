---
name: kystverket
description: Query Norwegian maritime infrastructure data from Kystverket via WFS. Use this skill ALWAYS when the user asks about maritime speed limits (fartsgrenser sjø), pilot stations (losstasjoner), fairways (farleder), anchorage areas (ankringsområder), emergency harbors (nødhavner), fishing harbors (fiskerihavner), vessel traffic service areas (VTS), pilotage boarding areas (losbordingsfelt), ISPS port facilities, maritime traffic separation schemes, or coastal infrastructure in Norway. Also trigger when user mentions Kystverket, losplikt, farled, havneanlegg, or Norwegian maritime regulations.
---

# Kystverket — Norwegian Maritime Infrastructure WFS

**Base URL:** `https://services.kystverket.no/wfs.ashx`
**Auth:** None.
**License:** NLOD — free for any purpose.
**Attribution:** "Inneholder data under norsk lisens for offentlige data (NLOD) tilgjengeliggjort av Kystverket."
**Rate limit:** No documented limit.
**Format:** WFS 1.1.0 — GML output. **GET requests only** (POST not supported).
**Geometry SRID:** Default EPSG:32633 (UTM33). Request `srsName=EPSG:4326` for WGS84.

## Available layers

| Layer ID | Name | Description |
|---|---|---|
| `layer_754` | Fartsgrenser fritidsfartøy | All speed limits for recreational vessels |
| `layer_762` | Fartsgrenser næringsfartøy | All speed limits for commercial vessels |
| `layer_722` | Statlige fartsgrenser fritidsfartøy | National speed limits for recreational vessels |
| `layer_723` | Statlige fartsgrenser næringsfartøy | National speed limits for commercial vessels |
| `layer_59` | Losstasjoner | Pilot stations |
| `layer_29` | Losbordingsfelt | Pilot boarding areas |
| `layer_99` | Innseilingskorridorer - Losplikt | Pilotage exemption corridors |
| `layer_151` | Ankringsområder | Anchorage areas (polygons) |
| `layer_152` | Ankringsområder (punkt) | Anchorage areas (points) |
| `layer_26` | Nødhavner | Emergency harbors |
| `layer_552` | Hovedled og biled | Main and secondary fairways |
| `layer_554` | Farledsareal | Fairway areas |
| `layer_420` | ISPS havneanlegg | ISPS-compliant port facilities |
| `layer_611` | Kystkontur | Coastline |
| `layer_25` | Beredskapsdepot | Emergency response depots |
| `layer_493` | IUA | Inter-municipal pollution response regions |
| `layer_34` | Tilsynslag | Inspection districts |
| `layer_696` | VTS tjenesteområde | Vessel Traffic Service areas |
| `layer_702` | Anbefalte ruter | IMO recommended routes |
| `layer_706` | TSS områder | Traffic Separation Schemes |
| `layer_1077` | Fiskerihavner | Fishing harbors |
| `layer_495` | Slukkinger | Extinguished lighthouses (updated nightly) |
| `layer_426` | Hendelser NOR VTS | Events registered by NOR VTS |
| `layer_digi_95` | Landstrøm | Shore power connections |

## Query features

```bash
curl -s "https://services.kystverket.no/wfs.ashx?service=WFS&version=1.1.0&request=GetFeature&typeName={LAYER_ID}&maxFeatures=5&srsName=EPSG:4326"
```

**Parameters:**
- `service=WFS&version=1.1.0&request=GetFeature` (required)
- `typeName` — layer ID from table above
- `maxFeatures` — limit results
- `srsName=EPSG:4326` — return WGS84 coordinates (recommended)
- `bbox=minlat,minlon,maxlat,maxlon,EPSG:4326` — spatial filter

**Note:** JSON output is not supported. Responses are GML/XML.

### Example response structure (pilot stations)

```xml
<ms:layer_59>
  <ms:msGeometry>
    <gml:Point srsName="EPSG:4326">
      <gml:pos>{LAT} {LON}</gml:pos>
    </gml:Point>
  </ms:msGeometry>
  <ms:navn>...</ms:navn>
  <ms:funksjon>Losstasjon</ms:funksjon>
  <ms:losavdeling>...</ms:losavdeling>
</ms:layer_59>
```

### Speed limits in a bbox

```bash
curl -s "https://services.kystverket.no/wfs.ashx?service=WFS&version=1.1.0&request=GetFeature&typeName=layer_754&maxFeatures=10&srsName=EPSG:4326&bbox={MINLAT},{MINLON},{MAXLAT},{MAXLON},EPSG:4326"
```

## Capabilities & layer discovery

```bash
curl -s "https://services.kystverket.no/wfs.ashx?service=WFS&request=GetCapabilities"
```

Lists all available layers with bounding boxes and abstracts.

## Common recipes

```bash
# All pilot stations in Norway
?service=WFS&version=1.1.0&request=GetFeature&typeName=layer_59&srsName=EPSG:4326

# Emergency harbors
?service=WFS&version=1.1.0&request=GetFeature&typeName=layer_26&srsName=EPSG:4326

# Speed limits for recreational vessels in a bbox
?service=WFS&version=1.1.0&request=GetFeature&typeName=layer_754&maxFeatures=50&srsName=EPSG:4326&bbox={MINLAT},{MINLON},{MAXLAT},{MAXLON},EPSG:4326

# ISPS port facilities
?service=WFS&version=1.1.0&request=GetFeature&typeName=layer_420&srsName=EPSG:4326

# Fishing harbors
?service=WFS&version=1.1.0&request=GetFeature&typeName=layer_1077&srsName=EPSG:4326
```

## Notes

- Speed limit layers `layer_754` and `layer_762` are the comprehensive ones — they combine national regulations and local ordinances.
- `layer_722`/`layer_723` contain only national (statlige) speed limits under the Harbour Act.
- The `Slukkinger` layer (extinguished lights) updates every night at 02:00.
- All coordinate queries use lat/lon order in WFS 1.1.0 bbox (not lon/lat).
