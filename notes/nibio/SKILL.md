---
name: nibio
description: Query Norwegian agricultural, soil, forestry, and land resource data from NIBIO (Norwegian Institute of Bioeconomy Research). Use this skill ALWAYS when the user asks about soil maps (jordsmonn, jordkvalitet), soil types, agricultural land (jordbruksareal), land resources (arealressurser, AR5), forestry productivity (bonitet), reindeer herding areas (reinbeiteområder, sameby), green infrastructure (grønnstruktur), erosion risk, cultural landscapes (kulturlandskap), grazing (beitebruk), or fruit orchards (frukthager) in Norway. Also trigger when user mentions NIBIO, Kilden, jordsmonnkart, arealressurskart, or asks about soil quality or agricultural data for a Norwegian area.
---

# NIBIO — Norwegian Agricultural & Soil Data

**WFS Base:** `https://wfs.nibio.no/cgi-bin/{service}`
**WMS Base:** `https://wms.nibio.no/cgi-bin/{service}`
**Reindeer WFS:** `https://wfs02.nibio.no/cgi-bin/rein/{service}`
**Auth:** None.
**License:** NLOD — free for any purpose.
**Attribution:** "Inneholder data under norsk lisens for offentlige data (NLOD) tilgjengeliggjort av NIBIO."
**Rate limit:** No documented limit. Be reasonable.
**Format:** WFS returns GML (XML). JSON output generally not supported — parse XML.
**Geometry SRID:** EPSG:4258 (EUREF89, ≈WGS84).

## WFS — Soil maps (jordsmonn)

The main queryable WFS service. 6 layers:

| Layer | Description |
|---|---|
| `ms:dekning1` | Municipality-level soil mapping coverage |
| `ms:dekning2` | Grid-level soil mapping coverage |
| `ms:dekning3` | Soil mapping polygons (detailed) |
| `ms:dekningPunkt` | Municipality centroid coverage indicators |
| `ms:dekningPunkt10prosent` | Municipalities with >10% soil coverage |
| `ms:planlagt` | Planned mapping areas |

### Query soil coverage for an area

```bash
curl -s "https://wfs.nibio.no/cgi-bin/jordsmonn?service=WFS&version=2.0.0&request=GetFeature&typenames=ms:dekning3&count=5&bbox={MINLAT},{MINLON},{MAXLAT},{MAXLON}"
```

**Parameters:**
- `service=WFS&version=2.0.0&request=GetFeature` (required)
- `typenames` — layer name from table
- `count` — max features
- `bbox=minlat,minlon,maxlat,maxlon` — spatial filter
- `STARTINDEX` — pagination offset

### Response fields (dekning3 — soil polygons)

```xml
<ms:dekning3>
  <ms:gid>...</ms:gid>
  <ms:komid>...</ms:komid>
  <ms:fylkenavn>...</ms:fylkenavn>
  <ms:kommunenavn>...</ms:kommunenavn>
  <ms:unit>m²</ms:unit>
  <ms:dekning>100</ms:dekning>
  <ms:ar5>...</ms:ar5>
  <ms:jm>...</ms:jm>
</ms:dekning3>
```

## WFS — Reindeer herding (wfs02)

```bash
curl -s "https://wfs02.nibio.no/cgi-bin/rein/samebyavtale?service=WFS&version=2.0.0&request=GetFeature&typenames=ms:samebyavtale&count=5"
```

**Layer:** `ms:samebyavtale` — cross-border (Norway–Sweden) reindeer herding agreements.

**Response fields:**
- `ms:samebynavn` — Sami village name
- `ms:samebyid` — Sami village code
- `ms:reinbeitedistriktnavn` — Reindeer herding district name
- `ms:reinbeitedistriktid` — District code
- Geometry: polygons of herding areas

## WMS services (image-based maps)

11+ WMS map services for visual map layers. Not queryable for individual features, but useful for map overlays.

| Service path | Theme |
|---|---|
| `jordsmonn` | Soil types and quality |
| `ar5` | Land resource classification (AR5) |
| `skogbruk` | Forestry |
| `bonitet` | Forest site productivity |
| `kulturlandskap` | Cultural landscapes |
| `gronnstruktur` | Green infrastructure |
| `rein` | Reindeer herding areas |
| `erosjon` | Erosion risk |
| `frukthager` | Fruit orchards |
| `beitebruk` | Grazing areas |
| `kilden` | Combined map portal |

### WMS GetMap example

```bash
curl -s "https://wms.nibio.no/cgi-bin/jordsmonn?SERVICE=WMS&VERSION=1.3.0&REQUEST=GetMap&LAYERS=jordkvalitet&CRS=EPSG:4326&BBOX={MINLAT},{MINLON},{MAXLAT},{MAXLON}&WIDTH=800&HEIGHT=600&FORMAT=image/png" -o soil_map.png
```

### WMS GetCapabilities

```bash
curl -s "https://wms.nibio.no/cgi-bin/{service}?REQUEST=GetCapabilities&SERVICE=WMS&VERSION=1.3.0"
```

## Workflow

1. Use `GetCapabilities` on the relevant WMS/WFS service to discover available layers
2. For queryable soil data, use the `jordsmonn` WFS with bbox filtering
3. For reindeer herding boundaries, use the `wfs02` reindeer WFS
4. For visual map images of other themes, use the WMS services with `GetMap`

## Notes

- Soil mapping (`jordsmonn`) covers approximately 50% of Norway's agricultural land. Coverage is concentrated in southern and central Norway.
- AR5 is Norway's national land resource classification system with categories like agricultural land, forest, open land, water, built-up areas.
- WFS on `wfs.nibio.no` supports `jordsmonn` only. Other themes are WMS-only (image layers).
- WFS on `wfs02.nibio.no` supports reindeer herding data.
- For the interactive map portal combining all NIBIO datasets, see [Kilden](https://kilden.nibio.no/).
