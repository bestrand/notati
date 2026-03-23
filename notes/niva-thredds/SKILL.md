---
name: niva-thredds
description: Query Norwegian water quality monitoring data from NIVA (Norwegian Institute for Water Research) via THREDDS. Use this skill ALWAYS when the user asks about water quality (vannkvalitet), river chemistry (elvekjemi), sea water temperature, salinity, chlorophyll, turbidity, nutrient concentrations (nitrogen, phosphorus), dissolved organic carbon (DOC), FerryBox measurements, water monitoring stations, or environmental water data in Norway. Also trigger when user mentions NIVA, thredds.niva.no, FerryBox, vannkjemi, or asks about water conditions in Norwegian rivers, fjords, or coastal areas.
---

# NIVA — Norwegian Water Quality Data (THREDDS)

**Base URL:** `https://thredds.niva.no/thredds`
**Auth:** None.
**License:** CC BY 4.0.
**Attribution:** "Data from the Norwegian Institute for Water Research (NIVA), licensed under CC BY 4.0."
**Rate limit:** No documented limit. Avoid bulk downloads — use time range filters.
**Format:** CSV via NetCDF Subset Service (NCSS). Also supports OPeNDAP for programmatic access.

## How to query — use NCSS (NetCDF Subset Service)

NCSS returns clean CSV. This is the recommended access method.

```
https://thredds.niva.no/thredds/ncss/point/{DATASET_PATH}?var={VARIABLES}&time_start={ISO8601Z}&time_end={ISO8601Z}&accept=csv
```

## Available datasets

### Loggers (continuous sensor stations)

| Dataset | Path | Variables |
|---|---|---|
| Glomma-Baterod | `datasets/loggers/glomma/baterod.nc` | temp_water_avg, phvalue_avg, condvalue_avg, turbidity_avg, cdomdigitalfinal |
| SIOS (Svalbard) | `datasets/loggers/sios.nc` | temp, salinity, turbcalib, chlacalib, condvalue, fdomcalib |
| MULTISOURCE Inlet | `datasets/loggers/msource-inlet.nc` | — |
| MULTISOURCE Outlet | `datasets/loggers/msource-outlet.nc` | — |
| AKVABY | `datasets/loggers/akvaby.nc` | — |

### FerryBoxes (ship-mounted sensors along routes)

| Dataset | Path | Variables |
|---|---|---|
| Color Fantasy (daily) | `datasets/nrt/color_fantasy.nc` | temperature, salinity, oxygen_sat, chlorophyll, turbidity, fdom |
| Color Fantasy NorSoop | `datasets/norsoop/color_fantasy/merged_acdd_color_fantasy.nc` | Same as above (research quality) |
| MS Norbjørn (daily) | `datasets/nrt/norbjoern.nc` | — |

### River chemistry samples (laboratory analyses, 1990–2024)

| Dataset | Path |
|---|---|
| Drammenselva (cleaned) | `datasets/samples/cleaned_riverchem_40352.nc` |
| Glomma, Sarpsfossen (cleaned) | `datasets/samples/cleaned_riverchem_40356.nc` |
| Numedalslågen (cleaned) | `datasets/samples/cleaned_riverchem_40355.nc` |

River chemistry variables: DOC, Color, NH4-N, NO3-N, TRP, POC, SPM, SiO2, TOC, TOTN, TDP, PP, TOTP, TSM, UV_Abs_254nm, UV_Abs_410nm, DIN, DON.

Raw versions also available (replace `cleaned_` with `raw_` and append date suffix).

### Models

| Dataset | Path |
|---|---|
| Sea urchin biomass | `datasets/refarktis/S_droebachiensis_final_biomass_sqm_wgs84/...nc` |

## Query examples

### Logger data (hourly timeseries)

```bash
curl -s "https://thredds.niva.no/thredds/ncss/point/{LOGGER_PATH}?var={VARIABLE}&time_start={YYYY-MM-DD}T00:00:00Z&time_end={YYYY-MM-DD}T00:00:00Z&accept=csv"
```

Response format:
```
time,station,latitude[unit="degrees_north"],longitude[unit="degrees_east"],{VARIABLE}[unit="..."]
{TIMESTAMP},{STATION_NAME},{LAT},{LON},{VALUE}
```

### Multiple variables at once

```bash
curl -s "https://thredds.niva.no/thredds/ncss/point/{LOGGER_PATH}?var={VAR1},{VAR2},{VAR3}&time_start={START}T00:00:00Z&time_end={END}T00:00:00Z&accept=csv"
```

### River nutrient concentrations (monthly lab samples)

```bash
curl -s "https://thredds.niva.no/thredds/ncss/point/{RIVERCHEM_PATH}?var=TOTN,TOTP,DOC&time_start={YYYY}-01-01T00:00:00Z&time_end={YYYY}-12-31T00:00:00Z&accept=csv"
```

### FerryBox sea water data

```bash
curl -s "https://thredds.niva.no/thredds/ncss/point/{FERRYBOX_PATH}?var=temperature,salinity,chlorophyll&time_start={START}T00:00:00Z&time_end={END}T00:00:00Z&accept=csv"
```

## Dataset discovery

```bash
# Root catalog (XML) — lists subcatalogs
curl -s "https://thredds.niva.no/thredds/catalog.xml"

# Subcatalog — loggers, ferryboxes, models, samples, archive
curl -s "https://thredds.niva.no/thredds/catalog/subcatalogs/{SUBCATALOG}.xml"

# Dataset metadata (OPeNDAP DAS — shows all variables with units)
curl -s "https://thredds.niva.no/thredds/dodsC/{DATASET_PATH}.das"

# Dataset structure (OPeNDAP DDS — shows dimensions and types)
curl -s "https://thredds.niva.no/thredds/dodsC/{DATASET_PATH}.dds"

# NCSS dataset description (XML — lists available variables and time range)
curl -s "https://thredds.niva.no/thredds/ncss/point/{DATASET_PATH}/dataset.xml"
```

## Variable reference

### Logger variables (units)
- `temp_water_avg` — Water temperature (°C)
- `phvalue_avg` — pH
- `condvalue_avg` — Conductivity (µS/cm)
- `turbidity_avg` — Turbidity
- `cdomdigitalfinal` — Colored dissolved organic matter (µg/l)

### FerryBox variables (units)
- `temperature` — Sea water temperature (°C)
- `salinity` — Salinity (PSU)
- `oxygen_sat` — Oxygen saturation (%)
- `chlorophyll` — Chlorophyll fluorescence
- `turbidity` — Turbidity
- `fdom` — Fluorescent dissolved organic matter

### River chemistry variables (units)
- `TOTN` — Total nitrogen (µg/l)
- `TOTP` — Total phosphorus (µg/l)
- `DOC` — Dissolved organic carbon (mg/l C)
- `TOC` — Total organic carbon (mg/l C)
- `NO3-N` — Nitrate nitrogen (µg/l)
- `NH4-N` — Ammonium nitrogen (µg/l)
- `SPM` — Suspended particulate matter (mg/l)
- `SiO2` — Silicate (mg/l)

## Workflow

1. Browse the catalog XML to find available datasets
2. Use OPeNDAP `.dds` to check dataset structure and variable names
3. Query via NCSS with `var=`, `time_start=`, `time_end=`, `accept=csv`

## Notes

- NCSS is the best access method — returns clean CSV with headers including units.
- OPeNDAP ASCII subsetting may time out on large datasets. Use NCSS with time range filters instead.
- Logger data is hourly. River chemistry samples are approximately monthly.
- FerryBox data follows ship routes — coordinates change with each measurement (trajectory data).
- Time parameters use ISO 8601 format with `Z` suffix (UTC).
- `NaN` values indicate missing data.
