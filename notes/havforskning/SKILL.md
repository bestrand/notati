---
name: havforskning
description: Query marine research data from Havforskningsinstituttet (Institute of Marine Research) — MAREANO seabed mapping, coral reefs, marine pollutants, species distribution, ocean litter, seabed habitats, and marine nature types. Use this skill ALWAYS when the user asks about Norwegian seabed data, MAREANO, coral reefs (korallrev), marine sediment contamination (PCB, PAH, DDT, heavy metals), ocean floor habitats, NiN marine nature types, fish species distribution maps, sponge gardens (svampsamfunn), sea pen fields (sjøfjærbunn), trawl marks, marine litter, or any Havforskningsinstituttet spatial data. Also trigger on kart.hi.no, MAREANO, Marine Grunnkart, or Norwegian marine biology data.
---

# Havforskning — Marine Research Geodata

**Base URL:** `https://kart.hi.no/mareano/ogc/features/v1`
**Auth:** None.
**License:** NLOD — credit Havforskningsinstituttet.
**Rate limit:** No documented rate limit. Be reasonable.
**Protocol:** OGC API Features (GeoJSON) + WFS on some endpoints.
**Total:** 103 queryable collections.

## List Collections

```bash
curl -s "https://kart.hi.no/mareano/ogc/features/v1/collections?f=json"
```

## Query Features

```bash
# Get items from a collection
curl -s "https://kart.hi.no/mareano/ogc/features/v1/collections/{collection_id}/items?f=json&limit=10"

# Spatial filter with bounding box (minLon,minLat,maxLon,maxLat)
curl -s "https://kart.hi.no/mareano/ogc/features/v1/collections/mareano_biologi:korallrev_observert_rev/items?f=json&limit=10&bbox={minLon},{minLat},{maxLon},{maxLat}"
```

**Pagination:** Use `limit` (page size, default 10) and `offset` (skip N items). `numberMatched` in response gives total count.

## Key Collections

### Biology (mareano_biologi)

| Collection | Items | Description |
|---|---|---|
| `mareano_biologi:korallrev_observert_rev` | 1,774 | Observed coral reefs — location, method, depth |
| `mareano_biologi:korallrev_modellerte_omraader` | — | Modelled coral reef areas |
| `mareano_biologi:korallrev_vernede_omraader` | — | Protected coral reef areas |
| `mareano_biologi:grabb_arter` | 412 | Grab sample species counts |
| `mareano_biologi:grabb_biomasse` | — | Grab sample biomass |
| `mareano_biologi:grabb_individer` | — | Grab sample individuals |
| `mareano_biologi:bomtraal_arter` | — | Beam trawl species |
| `mareano_biologi:arter_video` | — | Video-observed species |
| `mareano_biologi:svamper_video` | — | Sponges from video |
| `mareano_biologi:saarbare_marine_bunndyr_observert` | — | Vulnerable marine benthic animals |
| `mareano_biologi:hornkorall_*` | — | Horn coral species (5 collections: isidella, paragorgia, paramuricea, primnoa, radicipes) |
| `mareano_biologi:polygon_blotbunnskorallskog` | — | Soft-bottom coral forest polygons |
| `mareano_biologi:polygon_sjofjaerbunn` | — | Sea pen field polygons |
| `mareano_biologi:polygon_glassvampbestander` | — | Glass sponge stand polygons |
| `mareano_biologi:vme_*` | — | Vulnerable marine ecosystems (8 collections) |
| `mareano_biologi:topp_10_arter` | — | Top 10 species per station |

### Chemistry / Contamination (mareano_kjemi)

| Collection | Items | Description |
|---|---|---|
| `mareano_kjemi:pcb7` | 182 | PCB-7 levels in sediments |
| `mareano_kjemi:pah16` | — | PAH-16 polycyclic aromatic hydrocarbons |
| `mareano_kjemi:pah` | — | Sum PAH |
| `mareano_kjemi:ddt` | — | Sum DDT |
| `mareano_kjemi:pbde` | — | Brominated flame retardants |
| `mareano_kjemi:pfas` | — | PFAS (7 compounds) |
| `mareano_kjemi:hcb` | — | Hexachlorobenzene |
| `mareano_kjemi:thc` | — | Total hydrocarbons |
| `mareano_kjemi:antracen` | 391 | Anthracene levels |
| `mareano_kjemi:benzoapyren` | — | Benzo[a]pyrene |
| `mareano_kjemi:dekloran` | — | Dechlorane |
| `mareano_kjemi:fenantren` | — | Phenanthrene |
| `mareano_kjemi:naftalen` | — | Naphthalene |
| `mareano_kjemi:pyren` | — | Pyrene |
| `mareano_kjemi:perylen` | — | Perylene |
| `mareano_kjemi:nonylfenol4` | — | 4-Nonylphenol |

### Stations and Surveys (mareano_stasjoner)

| Collection | Items | Description |
|---|---|---|
| `mareano_stasjoner:videostasjoner_aarlig` | 3,754 | Video survey stations by year |
| `mareano_stasjoner:bunnstasjoner_aarlig` | — | Bottom stations by year |
| `mareano_stasjoner:kjemistasjoner_aarlig` | — | Chemistry stations by year |
| `mareano_stasjoner:soppel_X_Total_pr1km2` | — | Total litter per km² |
| `mareano_stasjoner:soppel_A_Plastic_pr1km2` | — | Plastic litter per km² |
| `mareano_stasjoner:traalspor_100m` | — | Trawl marks per 100m |

### Marine Grunnkart i Kystsonen (magik)

| Collection | Items | Description |
|---|---|---|
| `magik:nin_grunntyper_polygon` | 3,312 | NiN nature type polygons |
| `magik:nin_grunntyper` | — | NiN nature types (points) |
| `magik:nin_hovedtyper` | — | NiN main types |
| `magik:menneske_soppel` | 710 | Observed litter |
| `magik:menneske_traalspor` | 710 | Trawl marks |
| `magik:menneske_tapt-fiskeredskap` | — | Lost fishing gear |
| `magik:Bunnstromretning_og_hastighet_gjennomsnitt` | — | Bottom current speed and direction |

## Example: Coral Reef Query

```bash
curl -s "https://kart.hi.no/mareano/ogc/features/v1/collections/mareano_biologi:korallrev_observert_rev/items?f=json&limit=5&bbox={minLon},{minLat},{maxLon},{maxLat}"
```

Response:
```json
{
  "numberMatched": "{count}",
  "features": [{
    "properties": {
      "OBJECTID": "{id}",
      "OBJTYPE": "Korallrev",
      "OBS_METODE": "{observation_method}",
      "OBS_STED": "{location_description}",
      "BMNATYP": "Korallforekomster",
      "BM_KILDENA": "{source}",
      "DYBDE_MIN": "{depth_min}",
      "DYBDE_MAX": "{depth_max}"
    },
    "geometry": {"type": "Point", "coordinates": [{lon}, {lat}]}
  }]
}
```

## Species Distribution (WFS)

Fish and marine mammal distribution maps via separate WFS endpoints:

```bash
# Salmon distribution
curl -s "https://kart.hi.no/data/utbredelseskart/Laks/ows?service=WFS&version=2.0.0&request=GetFeature&typeNames=Laks&count=5&outputFormat=application/json"

# Mackerel distribution
curl -s "https://kart.hi.no/data/utbredelseskart/Makrell/ows?service=WFS&version=2.0.0&request=GetFeature&typeNames=Makrell&count=5&outputFormat=application/json"

# Marine mammals
curl -s "https://kart.hi.no/data/utbredelseskart/utbredelse_sjopattedyr/ogc/features/v1/collections?f=json"
```

## Notes

- Geometry is WGS84 (EPSG:4326)
- Chemistry collections include measurement units in fields like `meh_antrac` ("µg/kg tørrvekt")
- Use `bbox` for spatial filtering — most collections cover large ocean areas
- Some WFS species endpoints need exact typename matching (case-sensitive)
- The `magik:` prefix is for Marine Grunnkart i Kystsonen data
