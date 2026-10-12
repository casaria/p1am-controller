# P1AM Spa Controller

### Industrial-grade hot tub control on a P1AM-ETH, built with PlatformIO

---

## Overview

This is a from-scratch spa/hot-tub controller built on a **P1AM-ETH** board — SAMD21 / MKR Zero-compatible hardware from FACTS Engineering, using their `facts-engineering/P1AM` library, with W5500 Ethernet on chip-select pin 5. It's a **PlatformIO** project rather than an Arduino IDE sketch, kept intentionally separate from the [4-zone HVAC control](../hvac-zone-control/) project even though both live in the same home control ecosystem.

Where most hot-tub controllers are closed boxes with a tiny LCD and a handful of buttons, this one is built on an industrial I/O platform — the same category of hardware used for small-scale factory automation — which buys room to grow: more dosing channels, more sensors, more integrations, without hitting a ceiling.

## I/O and controls

| Function | Type |
|---|---|
| Heater | Relay |
| Pump 1 / Pump 2 | Relay |
| Circulation pump | Relay |
| Blower | Relay |
| Ozone generator | Relay |
| Chemical dosing (×4) | Relay, each with its own runtime timer |
| Flow switch | Digital input |

Each of the four chemical dosing pumps tracks its own runtime independently — the timers exist specifically to cap how long any single dosing line can run unattended, which matters a lot more for chemical dosing than it does for, say, a water pump.

## HMI and networking

The user-facing side is a **Nextion touchscreen**, driven over `Serial1`. In parallel, the controller's state is mirrored to and from an **MQTT broker at `ccc.casaria.net`**, under the `P1AM/` topic prefix, using `PubSubClient`. That gives the spa a second "front panel" — anything the Nextion can show or set, the rest of the home automation stack can see or set too.

## Third-party integrations (in progress)

The controller is being extended with outbound HTTP calls to two public APIs:

- **ip-api.com** — IP geolocation, used to derive the controller's latitude/longitude without manual configuration
- **api.timezonedb.com** — timezone and GMT offset lookup, keyed off those coordinates

The existing stubs for both calls are unfinished and were written against **ArduinoJson 5.x** syntax (`DynamicJsonBuffer`, `JsonObject&`), which is now out of date relative to the modern ArduinoJson API. Getting a working HTTP GET round-trip through to both services — and updating the JSON handling in the process — is the current focus.

## Status

Core relay and dosing control is working. HMI and MQTT mirroring are in place. The geolocation/timezone integration is the active work item.
