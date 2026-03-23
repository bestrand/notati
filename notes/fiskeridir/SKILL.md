---
name: fiskeridir
description: Query Norwegian fisheries and aquaculture data from Fiskeridirektoratet. Use this skill ALWAYS when the user asks about Norwegian fish farming (oppdrett), aquaculture sites, salmon farming, fish farming licenses, aquaculture capacity (MTB), fishing vessels (fiskefartøy), vessel registry, fish buyers (kjøpere), NYTEK certificates, aquaculture environmental reports (miljøundersøkelser), aquaculture research projects, or Fiskeridirektoratet data. Also trigger on fiskeridir, api.fiskeridir.no, lakseoppdrett, fartøyregisteret, or Norwegian seafood production data.
---

# Fiskeridirektoratet — Fisheries & Aquaculture APIs

**Base URL:** `https://api.fiskeridir.no`
**Auth:** None.
**License:** NLOD — credit Fiskeridirektoratet.
**Rate limit:** No documented rate limit. Be reasonable.

Six public JSON APIs. All use page-based pagination starting at page 1 (except aquaculture sites which start at 0).

## 1. Aquaculture Sites (pub-aqua)

```bash
# List sites (page starts at 0)
curl -s "https://api.fiskeridir.no/pub-aqua/api/v1/sites?page=0&size=10"

# Filter by municipality
curl -s "https://api.fiskeridir.no/pub-aqua/api/v1/sites?page=0&size=10&municipalityCode={municipalityCode}"

# Single site
curl -s "https://api.fiskeridir.no/pub-aqua/api/v1/sites/5"

# Licenses (plain array)
curl -s "https://api.fiskeridir.no/pub-aqua/api/v1/licenses?page=0&size=10"
```

**Key fields:** `siteId`, `siteNr`, `name`, `placementType` (Offshore/Onshore), `waterType` (Salt/Fresh), `latitude`, `longitude`, `capacity` (tonnes), `placement.municipalityCode`.

## 2. Vessel Registry (vessel-api)

Norwegian fishing vessel register — names, owners, dimensions, engine power.

```bash
# List vessels (page starts at 1, default size 30)
curl -s "https://api.fiskeridir.no/vessel-api/api/v1/vessels?page=1&size=5"

# Search by vessel name
curl -s "https://api.fiskeridir.no/vessel-api/api/v1/vessels?name={vessel_name}&page=1&size=10"
```

**Key fields:** `id`, `name`, `registrationMark`, `municipalityCode`, `width`, `length`, `enginePower` (HP), `buildYear`, `registrationDate`, `owners[]` with `name`, `entityType`, `postalCode`, `city`.

**Response:** Plain array (not wrapped in pagination object).

## 3. Fish Buyer Locations (buyer-api)

Registered fish buyer/landing locations.

```bash
# List locations (page starts at 1, default 100)
curl -s "https://api.fiskeridir.no/buyer-api/v1/locations?page=1&size=10"
```

**Key fields:** `locationId`, `locationType`, `mainLegalEntityId`, `address.municipalityNumber`, `created`, `updated`.

**Response:** Plain array.

## 4. NYTEK Certificates (nytek-public)

Technical standard certificates for aquaculture installations.

```bash
# List certificates (paginated with content wrapper)
curl -s "https://api.fiskeridir.no/nytek-public/api/v3/certificates?page=1&size=5"

# Certificates for a specific site number
curl -s "https://api.fiskeridir.no/nytek-public/api/v3/certificates/{siteNr}"
```

**Key fields:** `status.status` (ISSUED/EXPIRED), `status.validFrom`, `status.validUntil`, `installationCertificate.issueDate`, `installationCertificate.expiryDate`.

**Response:** `content[]` wrapper with pagination.

## 5. Environmental Reports (envreportreg-public)

16,198 environmental survey reports from aquaculture sites.

```bash
# Search reports (page starts at 0)
curl -s "https://api.fiskeridir.no/envreportreg-public/api/v2/report/search?page=0&size=5"

# Single report
curl -s "https://api.fiskeridir.no/envreportreg-public/api/v2/report/{reportId}"
```

**Key fields:** `reportId`, `reportCreated`, `organisationNumber`, `siteNumber`, `siteName`, `envExaminationType` (e.g. "annen", "b-undersokelse", "c-undersokelse"), `siteCondition`.

**Response:** `content[]` wrapper with `totalElements`.

## 6. Aquaculture Research Projects (aqua-research-project-api-public)

Research projects related to aquaculture.

```bash
# List projects (paginated with content wrapper)
curl -s "https://api.fiskeridir.no/aqua-research-project-api-public/api/v1/research-projects?page=1&size=5"

# Single project by number
curl -s "https://api.fiskeridir.no/aqua-research-project-api-public/api/v1/research-project/{projectNumber}"
```

**Key fields:** `projectNumber`, `name`, `description`, `status`.

**Response:** `content[]` wrapper with pagination.

## Pagination Patterns

The APIs use two patterns — check each:

| API | Page start | Response format |
|---|---|---|
| pub-aqua sites | 0 | Spring wrapper (`totalElements`) |
| pub-aqua licenses | 0 | Plain array |
| vessel-api | 1 | Plain array |
| buyer-api | 1 | Plain array |
| nytek-public | 1 | `content[]` wrapper |
| envreportreg-public | 0 | `content[]` wrapper with `totalElements` |
| aqua-research | 1 | `content[]` wrapper |

## Known Non-Working Endpoints

- `pub-aqua/api/v1/fish-health` — returns 404
- `pub-aqua/api/v1/escapes` — returns 404

For fish health and lice data, check barentswatch.no (may require auth).
