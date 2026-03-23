---
name: fdk
description: Search the master registry of Norwegian public data APIs via Fellesdatakatalog (FDK). Use this skill ALWAYS when the user asks about what Norwegian public APIs exist, which government agencies publish data, how to find a specific Norwegian data API, or when you need to locate an API endpoint for a Norwegian public agency. Also trigger when looking for WFS, WMS, OGC API, or REST API endpoints from Norwegian government agencies. Also trigger on fellesdatakatalog, data.norge.no, FDK, or Norwegian open data catalog.
---

# FDK — Norwegian Public API Discovery

**Endpoint:** `POST https://search.api.fellesdatakatalog.digdir.no/search/dataservices`
**Auth:** None.
**Format:** JSON request and response.
**License:** NLOD — credit Digitaliseringsdirektoratet.
**Rate limit:** No documented rate limit. Be reasonable.

The national registry of **1,060 data services** (APIs) from Norwegian public agencies. Covers REST APIs, WFS, WMS, OGC API Features, GraphQL, and more.

## Search

All requests are POST with JSON body.

### List all services (paginated)

```bash
curl -s -X POST "https://search.api.fellesdatakatalog.digdir.no/search/dataservices" \
  -H "Content-Type: application/json" \
  -d '{"pagination":{"page":1}}'
```

Default page size is 10. Page numbering starts at 1.

### Text search

```bash
curl -s -X POST "https://search.api.fellesdatakatalog.digdir.no/search/dataservices" \
  -H "Content-Type: application/json" \
  -d '{"query":"akvakultur","pagination":{"page":1}}'
```

**Use Norwegian terms** for best results: "flom" not "flood", "kulturminner" not "cultural heritage", "vannkraft" not "hydropower".

### Filter by organization

```bash
curl -s -X POST "https://search.api.fellesdatakatalog.digdir.no/search/dataservices" \
  -H "Content-Type: application/json" \
  -d '{"filters":{"orgPath":{"value":"/STAT/912660680/971349077"}},"pagination":{"page":1}}'
```

### Combine text search + org filter

```bash
curl -s -X POST "https://search.api.fellesdatakatalog.digdir.no/search/dataservices" \
  -H "Content-Type: application/json" \
  -d '{"query":"korallrev","filters":{"orgPath":{"value":"/STAT/912660680/971349077"}},"pagination":{"page":1}}'
```

## Organization Directory

| orgPath | Agency | Services |
|---|---|---|
| `/STAT/972417858/971040238` | Kartverket (Mapping Authority) | 231 |
| `/STAT/912660680/971349077` | Havforskningsinstituttet (Marine Research) | 223 |
| `/STAT/972417882/999601391` | Miljødirektoratet (Environment Agency) | 100 |
| `/STAT/912660680/970188290` | Norges geologiske undersøkelse (Geological Survey) | 60 |
| `/STAT/972417874/988983837` | NIBIO (Bioeconomy Research) | 50 |
| `/STAT/977161630/970205039` | NVE (Water Resources & Energy) | 40 |
| `/STAT/972417807/974761076` | Statistisk sentralbyrå (Statistics Norway) | 38 |
| `/STAT/972417807/971526920` | Brønnøysundregistrene (Business Registry) | 25 |
| `/STAT/912660680/971203420` | Fiskeridirektoratet (Fisheries) | 23 |
| `/STAT/977161630/870917732` | Meteorologisk institutt (Weather) | 21 |
| `/STAT/972417882/974760819` | Riksantikvaren (Cultural Heritage) | 17 |
| `/STAT/972417882/971022264` | Norsk Polarinstitutt (Polar Institute) | 15 |
| `/STAT/912660680/874783242` | Mattilsynet (Food Safety) | 14 |
| `/STAT/932384469` | Entur (Public Transport) | 14 |
| `/STAT/972417858/974760223` | Statens vegvesen (Roads) | 13 |
| `/STAT/972417831/974760983` | DSB (Civil Protection) | 9 |

Use parent paths for broader search: `/STAT` = all state agencies (972), `/KOMMUNE` = municipalities (53).

## Response Structure

```json
{
  "hits": [{
    "id": "{uuid}",
    "uri": "{service_url}",
    "title": {"nb": "{service_title}"},
    "description": {"nb": "{service_description}"},
    "fdkFormatPrefixed": ["{format_tag}"],
    "organization": {
      "id": "{org_number}",
      "name": "{agency_name}",
      "orgPath": "{org_path}"
    },
    "relations": [{"uri": "...", "type": "servesDataset"}]
  }],
  "page": {
    "currentPage": 1,
    "size": 10,
    "totalElements": 1060,
    "totalPages": 106
  },
  "aggregations": {
    "orgPath": [{"key": "/STAT", "count": 972}, ...],
    "format": [{"key": "FILE_TYPE WMS_SRVC", "count": 260}, ...]
  }
}
```

## Format Tags — Which APIs Return Queryable Data?

| Tag | Meaning | Returns structured data? |
|---|---|---|
| `FILE_TYPE JSON` | REST API returning JSON | ✅ Yes |
| `FILE_TYPE GEOJSON` | GeoJSON features | ✅ Yes |
| `FILE_TYPE WFS_SRVC` | OGC Web Feature Service | ✅ Yes (GML or JSON) |
| `FILE_TYPE GML` | WFS returning GML | ✅ Yes (XML parsing needed) |
| `MEDIA_TYPE application/json` | JSON API | ✅ Yes |
| `FILE_TYPE WMS_SRVC` | Web Map Service | ❌ Images only |
| `FILE_TYPE PNG` | Image output | ❌ Images only |
| `FILE_TYPE WMTS_SRVC` | Tile service | ❌ Tiles only |
| `FILE_TYPE WCS_SRVC` | Coverage service | ⚠️ Raster data |

**To find queryable APIs**: check `fdkFormatPrefixed` on each hit for JSON, GEOJSON, WFS_SRVC, or GML.

**Important:** The `format` filter in the request body does not actually narrow results — it returns all 1,060 regardless. You must filter client-side by checking `fdkFormatPrefixed` on each hit.

## Aggregations

Every response includes breakdowns in `aggregations`:
- `orgPath` — which agencies have results (use to discover orgPaths)
- `format` — how many services per format type

Use aggregations to orient before drilling down. Example: search "flom", check `orgPath` aggregation to see which agencies have flood-related services, then filter by that agency.

## Practical Workflow

1. **Text search** with Norwegian keywords: `{"query":"korallrev"}`
2. Check `aggregations.orgPath` to see which agencies have results
3. **Filter by org** to focus: `{"filters":{"orgPath":{"value":"/STAT/912660680/971349077"}}}`
4. Check `fdkFormatPrefixed` on hits — look for JSON/GEOJSON/WFS_SRVC
5. The `uri` field is the service URL — may be a base URL, GetCapabilities link, or Swagger page. Derive the query endpoint from it.

## Notes

- **URIs vary in format.** Some are base URLs, some are GetCapabilities links, some are Swagger UI pages. You may need to adapt the URI to form actual query requests.
- **Use Norwegian search terms.** The catalog is in Norwegian.
- **Duplicates exist.** The same service may appear multiple times with slightly different metadata.
- **Stale entries exist.** Some registered services may have moved or been decommissioned. Always test the URI before relying on it.
