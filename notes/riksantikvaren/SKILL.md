---
name: riksantikvaren
description: Query cultural heritage data from Riksantikvaren (Directorate for Cultural Heritage). Use this skill ALWAYS when the user asks about Norwegian cultural monuments (kulturminner), heritage sites (kulturmiljøer), Askeladden database, SEFRAK buildings, protected buildings (fredede bygninger), archaeological sites, stave churches (stavkirker), cultural heritage protection zones (sikringssoner), fire protection of heritage, or any cultural heritage GIS data in Norway. Also trigger on api.ra.no, Riksantikvaren, kulturminnesøk, or Norwegian cultural heritage data.
---

# Riksantikvaren — Cultural Heritage OGC API

**Base URL:** `https://api.ra.no`
**Auth:** None.
**Protocol:** OGC API Features (REST/JSON).
**License:** NLOD — credit Riksantikvaren.
**Rate limit:** No documented rate limit. Be reasonable.
**Total records:** ~624,841 kulturminner + sikringssoner.

## API Structure

The root catalog lists available datasets:

```bash
# List all datasets
curl -s "https://api.ra.no/?f=json"
```

**11 datasets available:**

| Dataset ID | Title | Description |
|---|---|---|
| `kulturminner` | Kulturminner | All cultural monuments from Askeladden |
| `kulturmiljoer` | Kulturmiljøer | Protected cultural environments, World Heritage |
| `brukerminner` | Brukerminner | User-submitted heritage records |
| `KulturminnerFredaBygninger` | Freda bygninger | Protected buildings |
| `KulturminnerSEFRAKbygninger` | SEFRAK-bygninger | Pre-1900 buildings register |
| `KulturminnerKulturmiljoer` | Kulturmiljøer | Cultural environments |
| `LokaliteterEnkeltminnerOgSikringssoner` | Lokaliteter, Enkeltminner og Sikringssoner | Sites, individual monuments, buffer zones |
| `KulturminnerVerneverdigTetteTrehusmiljo` | Verneverdig tette trehusmiljøer | Historic wooden town areas |
| `brannvern` | Brannvern | Fire protection of heritage |
| `KulturminnerBrannsmitteomrader` | Brannsmitteområder | Fire spread risk areas |

## Querying Features

Each dataset has its own collections endpoint:

```bash
# List collections for kulturminner
curl -s "https://api.ra.no/kulturminner/collections?f=json"

# Get features (paginated, max 10 default)
curl -s "https://api.ra.no/kulturminner/collections/kulturminner/items?f=json&limit=10"

# Get a specific feature by ID
curl -s "https://api.ra.no/kulturminner/collections/kulturminner/items/{featureId}?f=json"
```

### Collections per Dataset

The `kulturminner` dataset has 2 collections:
- `kulturminner` — 624,841 cultural monuments
- `sikringssoner` — protection/buffer zones

## Feature Properties

Key properties for kulturminner items:

| Property | Description |
|---|---|
| `navn` | Name of the monument |
| `opphav` | Origin/dating |
| `informasjon` | Description |
| `enkeltminneart` | Monument type |
| `enkeltminnekategori` | Category |
| `kommune` | Municipality |
| `fylke` | County |
| `gårdsnavn` | Farm name |
| `lokalitetsart` | Locality type |
| `lokalitetid` | Locality ID in Askeladden |
| `kulturminnesøk` | Link to kulturminnesøk.no |
| `linkAskeladden` | Link to Askeladden |
| `linkKulturminnesøk` | Link to public search |

## Filtering

```bash
# Spatial filter — bounding box (minLon,minLat,maxLon,maxLat)
curl -s "https://api.ra.no/kulturminner/collections/kulturminner/items?f=json&limit=10&bbox={minLon},{minLat},{maxLon},{maxLat}"

# Property filter
curl -s "https://api.ra.no/kulturminner/collections/kulturminner/items?f=json&limit=10&kommune={kommunenavn}"
```

## Notes

- Responses are GeoJSON with `Point` or `Polygon` geometries
- **Not all APIs are official** — Riksantikvaren notes some may change without notice. Official ones are listed on Geonorge.
- For large spatial queries, use bbox filtering to limit response size
- The `limit` parameter controls page size (default 10, max varies)
- Use `offset` for pagination through large result sets
- Contact: datatjenester@ra.no for questions
