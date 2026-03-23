---
name: artskart
description: Query Norwegian species occurrence data from Artsdatabanken's Artskart API. Use this skill ALWAYS when the user asks about species observations in Norway, wildlife sightings, species distribution, biodiversity data, species occurrences (artsfunn), threatened species (truede arter, rødliste), species search, or wants to find where a plant or animal has been observed in Norway. Also trigger when user mentions Artskart, Artsdatabanken, artsobservasjoner, naturmangfold, or asks about specific species locations in Norwegian nature.
---

# Artskart — Norwegian Species Occurrence API

**Base URL:** `https://artskart.artsdatabanken.no/publicapi/api`
**Auth:** None.
**License:** CC BY 4.0 — individual datasets may vary.
**Attribution:** "Data fra Artsdatabanken / Artskart (artsdatabanken.no). Kreditering av individuelle datakilder kan kreves."
**Rate limit:** No documented limit. Be reasonable — this is a shared public service.
**Format:** JSON responses.
**Total records:** 60+ million species observations across Norway.

## Find species (taxon lookup)

```bash
curl -s "https://artskart.artsdatabanken.no/publicapi/api/taxon/ScientificName/{GENUS}?maxCount=10"
```

Returns a JSON object mapping `ScientificNameId` → `ScientificName`:
```json
{
  "12345": "Genus species1",
  "12346": "Genus species2"
}
```

**Note:** The search matches broadly. Results may include partial matches across all taxa. Look through the returned names to find the correct `ScientificNameId`.

## Query observations

```bash
curl -s "https://artskart.artsdatabanken.no/publicapi/api/observations/list?ScientificNameIds={ID}&Top=5"
```

**Key parameters:**
- `ScientificNameIds` — taxon ID (from taxon lookup). **This is the main filter** — without it, all 60M+ records are returned.
- `Top` — max results per page (default 20)
- `PageNumber` — pagination (0-indexed)

### Response structure

```json
{
  "TotalCount": 8701,
  "PageIndex": 0,
  "PageSize": 20,
  "TotalPages": 436,
  "Observations": [
    {
      "Id": "urn:catalog:...",
      "ScientificName": "...",
      "Name": "...",
      "Author": "...",
      "kingdom": "...",
      "phylum": "...",
      "klass": "...",
      "order": "...",
      "family": "...",
      "genus": "...",
      "species": "...",
      "Status": "LC",
      "Institution": "...",
      "Collector": "...",
      "CollectedDate": "...",
      "County": "...",
      "CountyId": 0,
      "Municipality": "...",
      "MunicipalityId": 0,
      "Locality": "...",
      "Latitude": "...",
      "Longitude": "...",
      "Precision": "...",
      "BasisOfRecord": "observasjon",
      "ObsUrl": "https://artskart.artsdatabanken.no/#featureInfo/..."
    }
  ]
}
```

**Key fields:**
- `ScientificName`, `Name` (Norwegian common name)
- `Status` — Red List category: `LC` (least concern), `NT` (near threatened), `VU` (vulnerable), `EN` (endangered), `CR` (critically endangered), `NE` (not evaluated)
- `County`, `Municipality` — administrative location
- `Latitude`, `Longitude` — coordinates (note: returned as strings with comma decimal separator)
- `Precision` — coordinate accuracy in metres
- `CollectedDate` — observation date
- `BasisOfRecord` — `observasjon`, `objekt` (specimen), etc.

## Landscape types WFS (Artsdatabanken)

NiN (Nature in Norway) landscape classification available via WFS:

```bash
curl -s "https://wms.artsdatabanken.no/?map=/maps/mapfiles/la.map&service=WFS&version=2.0.0&request=GetFeature&typenames={LAYER}&count=5"
```

**Layers:** `ms:LA-I` (inland), `ms:LA-K` (coastal), `ms:LA-M` (marine), `ms:LA-I-A`, `ms:LA-I-D`, `ms:LA-I-S`, `ms:LA-K-F`, `ms:LA-K-S`, `ms:LA-K-A`.

**Note:** Geometries are in EPSG:32633 (UTM33). Features are large landscape classification polygons.

## Workflow

1. Search for species using the taxon endpoint with a genus or species name
2. Find the correct `ScientificNameId` in the results
3. Query observations using `ScientificNameIds={ID}`
4. Paginate with `PageNumber` to get more results

## Notes

- **Coordinate format:** Latitude and Longitude are returned as strings with Norwegian comma-as-decimal (e.g., `"61,32"`). Replace comma with dot before converting to numbers.
- The `ScientificNameIds` parameter is essential for filtering. Without it the API returns the entire 60M+ observation database in default order.
- `TotalCount` in the response tells you how many observations match.
- Some observations are from museum specimens dating back to the 1800s. Use `CollectedDate` to filter for recent sightings.
- Individual observation detail pages are available at the `ObsUrl` link.
- Red List status comes from the Norwegian Red List (Norsk rødliste for arter).
