---
name: entur
description: Query Norwegian public transport data via the Entur APIs. Use this skill ALWAYS when the user asks about public transport in Norway, bus/train/tram/metro/ferry schedules, departures, journey planning, travel routes between Norwegian places, real-time departures (sanntid), stop places (holdeplasser), or mentions Entur, Ruter, Skyss, AtB, Kolumbus, Vy, SJ Nord, Flytoget, or any Norwegian transit operator.
---

# Entur — Norwegian Public Transport API

**Journey Planner:** `https://api.entur.io/journey-planner/v3/graphql` (POST, GraphQL)
**Geocoder:** `https://api.entur.io/geocoder/v1/` (GET, JSON)
**Auth:** None, but **must set** `ET-Client-Name: your-company-yourapp` header on every request.
**License:** NLOD — free for any purpose.
**Rate limit:** Strict throttling on unidentified consumers. No published numeric limit — always include `ET-Client-Name` or requests may be blocked.

## 1. Geocoder — Find stops

```bash
curl -s "https://api.entur.io/geocoder/v1/autocomplete?text=<stop+name>&size=5&layers=venue" \
  -H "ET-Client-Name: skill-test"
```

**Parameters:** `text`, `size` (default 10), `lang` (`no`/`en`), `layers` (`venue`=stops, `address`, `street`, `locality`), `focus.point.lat`/`focus.point.lon` (bias towards location)

**Response:** GeoJSON FeatureCollection. Per feature: `properties.id` (NSR ID, e.g. `NSR:StopPlace:<id>`), `properties.name`, `properties.label`, `properties.category` (e.g. `railStation`, `onstreetBus`), `geometry.coordinates` [lon, lat]

**Reverse:** `GET /geocoder/v1/reverse?point.lat=<lat>&point.lon=<lon>&size=5&layers=venue`

## 2. Journey Planner (GraphQL)

### Departures from a stop
```bash
curl -s -X POST "https://api.entur.io/journey-planner/v3/graphql" \
  -H "Content-Type: application/json" -H "ET-Client-Name: skill-test" \
  -d '{"query":"{ stopPlace(id: \"NSR:StopPlace:<id>\") { name estimatedCalls(numberOfDepartures: 10) { expectedDepartureTime realtime destinationDisplay { frontText } serviceJourney { line { publicCode name transportMode } } quay { publicCode name } } } }"}'  
```

### Trip from A to B
```graphql
{
  trip(
    from: { place: "NSR:StopPlace:<from_id>" }
    to: { place: "NSR:StopPlace:<to_id>" }
    numTripPatterns: 3
  ) {
    tripPatterns {
      duration
      walkDistance
      legs {
        mode
        fromPlace { name }
        toPlace { name }
        expectedStartTime
        expectedEndTime
        line { publicCode name transportMode }
      }
    }
  }
}
```

**Trip parameters:** `from`/`to` (use `{ place: "NSR:..." }` or `{ coordinates: { latitude, longitude } }`), `dateTime` (ISO, default now), `arriveBy` (boolean), `numTripPatterns` (default 5), `walkSpeed` (m/s, default 1.3)

**Always use the Geocoder to find correct NSR IDs** — don't hardcode or guess.

## Workflow

1. **Geocode** origin + destination: `GET /geocoder/v1/autocomplete?text=<place>&layers=venue&size=3`
2. **Search trips** using NSR IDs (or coordinates)
3. **Present:** departure time, duration, line numbers, transfers

**Transport modes:** `bus`, `tram`, `rail`, `metro`, `water` (ferry), `air`, `coach`
**Dates:** ISO 8601 with timezone (`2026-03-20T14:30:00+01:00`)
