[![hacs_badge](https://img.shields.io/badge/HACS-Custom-orange.svg)](https://github.com/hacs/integration)

# BKK Stop Information (enriched fork)

Home Assistant custom component for [Budapest GO / BKK](https://go.bkk.hu/) stop departures.

This is a fork of [amaximus/bkk_stop](https://github.com/amaximus/bkk_stop) with rail enrichment and leaner attributes for map-friendly Lovelace cards. Pair with **[bator/bkk-stop-card](https://github.com/bator/bkk-stop-card)**.

## What’s new in this fork

- **`tripId`** on each departure (card can fetch the trip polyline on demand)
- **No bulky `shape` prefetch** — keeps sensor state small (~KB instead of tens of KB)
- **MÁV ELVIRA enrichment** (unofficial passenger-info API) for known rail stations:
  - live **platform / vágány** (`platform`, optionally baked into `headsign` as `· vág.N`)
  - delay reason, train number / name
  - booking / dining / bike flags and `services[]`
  - station-level `stationHavaria`
- Extra vehicle fields when available: delay, model, occupancy, GPS `lat`/`lon`, alerts
- API health tracking (`api_ok`, consecutive failures / threshold)

> ELVIRA is unofficial and may change without notice. It is enabled by default for mapped rail stops and can be disabled with `elvira: false`.

## Installation

1. Copy `custom_components/bkk_stop/` into your Home Assistant config
2. Add sensors under `sensor:` (or a package / `!include`)
3. Restart Home Assistant

You need your own BKK API key from [opendata.bkk.hu](https://opendata.bkk.hu/).

## Configuration

| Name | Optional | Default | Description |
| :--- | :--- | :--- | :--- |
| `name` | **N** | — | Sensor name |
| `stopId` | **N** | — | Stop id from [go.bkk.hu](https://go.bkk.hu/) |
| `apiKey` | **N** | — | BKK OpenData API key |
| `maxItems` | Y | `0` | Max departures (`0` = unlimited) |
| `minsAfter` | Y | `20` | Minutes ahead to include |
| `minsBefore` | Y | `0` | Skip soon departures (≤ 0) |
| `wheelchair` | Y | `false` | Wheelchair attribute |
| `bikes` | Y | `false` | Bike attribute |
| `colors` | Y | `false` | Route colours |
| `ignoreNow` | Y | `true` | Ignore vehicles already at the stop |
| `inPredicted` | Y | `false` | Use predicted time for “in minutes” |
| `vehicle` | Y | `true` | Include live vehicle details when present |
| `routes` | Y | `[]` | Allow-list of route labels |
| `headsigns` | Y | `[]` | Allow-list of headsigns |
| `elvira` | Y | `true` | Enable ELVIRA enrichment for mapped rail stops |
| `failureThreshold` | Y | (built-in) | Consecutive API failures before marking unhealthy |

### Example — suburban rail from Kelenföld

```yaml
- platform: bkk_stop
  name: vonatok_pestrol
  stopId: BKK_005501024
  minsAfter: 120
  wheelchair: true
  bikes: true
  colors: true
  inPredicted: true
  vehicle: true
  elvira: true
  apiKey: !secret bkk_apikey
  headsigns:
    - Székesfehérvár
    - Balatonfüred
  routes:
    - G43
    - Z30
    - S30
    - IR
    - IC
```

## Lovelace card

Use the enriched card: [bator/bkk-stop-card](https://github.com/bator/bkk-stop-card)  
(`custom:bkk-stop-card-r2`).

Action for manual refresh: `bkk_stop.refresh` with `entity_id`.

## Upstream

Please consider supporting the original author: [amaximus/bkk_stop](https://github.com/amaximus/bkk_stop).

![bkk_stop Lovelace example](example/bkk_lovelace.jpg)
