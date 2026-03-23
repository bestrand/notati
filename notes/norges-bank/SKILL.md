---
name: norges-bank
description: Query Norges Bank (Central Bank of Norway) data — exchange rates, policy rate (styringsrenten), NOWA, financial conditions index, government securities, money market rates, and regional network surveys. Use this skill ALWAYS when the user asks about NOK exchange rates (valutakurser), USD/NOK, EUR/NOK, the Norwegian policy rate (styringsrente, foliorente), NOWA overnight rate, Norwegian government bonds (statsobligasjoner), or any Norges Bank financial/monetary data. Also trigger on data.norges-bank.no, SDMX, or Norwegian central bank statistics.
---

# Norges Bank — Financial Data API (SDMX-JSON)

**Base URL:** `https://data.norges-bank.no/api/data`
**Auth:** None.
**Format:** SDMX-JSON (standard statistical data format).
**License:** Free to use with attribution to Norges Bank.
**Rate limit:** No documented rate limit. Be reasonable.

## URL Pattern

```
https://data.norges-bank.no/api/data/{dataflow}/{key}?format=sdmx-json&{params}
```

## Exchange Rates (EXR)

41 currencies available. Key format: `{freq}.{currency}.NOK.SP`

```bash
# Latest USD/NOK rate
curl -s "https://data.norges-bank.no/api/data/EXR/B.USD.NOK.SP?format=sdmx-json&lastNObservations=1"

# Latest EUR/NOK rate
curl -s "https://data.norges-bank.no/api/data/EXR/B.EUR.NOK.SP?format=sdmx-json&lastNObservations=1"

# Multiple currencies at once
curl -s "https://data.norges-bank.no/api/data/EXR/B.USD+EUR+GBP+SEK+DKK.NOK.SP?format=sdmx-json&lastNObservations=1"

# Historical range
curl -s "https://data.norges-bank.no/api/data/EXR/B.USD.NOK.SP?format=sdmx-json&startPeriod=2024-01-01&endPeriod=2024-12-31"

# Monthly averages
curl -s "https://data.norges-bank.no/api/data/EXR/M.USD.NOK.SP?format=sdmx-json&lastNObservations=12"
```

**Frequency codes:** `B`=business day, `M`=monthly, `A`=annual.

**Common currencies:** USD, EUR, GBP, SEK, DKK, CHF, JPY, CAD, AUD, CNY, PLN, ISK, KRW, INR, SGD, THB, TWD, BRL, TRY, ZAR.

## Parsing SDMX-JSON Responses

The response structure nests data under `data.dataSets[0].series`. Each series key like `"0:0:0:0"` maps to dimension values in `data.structure.dimensions.series`. Observations are in `observations` keyed by time index.

```python
import json

data = json.loads(response_text)
series = data["data"]["dataSets"][0]["series"]
time_periods = data["data"]["structure"]["dimensions"]["observation"][0]["values"]

for series_key, series_data in series.items():
    for time_idx, obs in series_data["observations"].items():
        period = time_periods[int(time_idx)]["id"]
        value = obs[0]
        print(f"{period}: {value}")
```

## Key Policy Rate (Styringsrenten)

```bash
curl -s "https://data.norges-bank.no/api/data/KPRA/B.KPRA?format=sdmx-json&lastNObservations=5"
```

## NOWA (Norwegian Overnight Weighted Average)

```bash
curl -s "https://data.norges-bank.no/api/data/NOWA/B.NOWA.RATE+VOLUME+TRADECOUNT?format=sdmx-json&lastNObservations=5"
```

## All Available Dataflows

```bash
curl -s "https://data.norges-bank.no/api/data?format=sdmx-json"
```

**20 dataflows include:** EXR (exchange rates), KPRA (policy rate), NOWA, FCI (financial conditions index), GOVT_SECURITIES, MONEY_MARKET, REGIONAL_NETWORK, and more.

## Common Parameters

| Parameter | Example | Description |
|---|---|---|
| `format` | `sdmx-json` | Always use this |
| `lastNObservations` | `1` | Latest N data points |
| `startPeriod` | `2024-01-01` | Start date (YYYY-MM-DD) |
| `endPeriod` | `2024-12-31` | End date |
| `detail` | `dataonly` | Omit metadata for smaller responses |
