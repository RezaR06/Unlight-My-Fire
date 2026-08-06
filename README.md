# Unlight My Fire

A solar-powered wildfire early-detection sensor node, built around a custom 4-layer mixed-signal PCB. The board carries five environmental sensors, runs unattended in the field on solar and a single LiPo cell, and reports over LoRa to a live dashboard.

Named after the Doors song, inverted — the point is to catch a fire before it starts.

![Top view](images/UnlightMyFire_3D_PCB.png)

## The idea

Most wildfire detection happens after there's already a fire: satellite thermal imaging, camera networks, someone calling it in. This node is meant to sit in the woods and watch for the conditions and early signatures that precede ignition, then report them continuously rather than waiting for a plume big enough to see from orbit.

Two constraints drove most of the design:

1. **It has to run for months without anyone visiting it.** Average current draw is the whole ballgame — a node that needs a battery swap every three weeks is useless.
2. **It has to sense several unrelated physical quantities at once**, which means a mixed-signal board with sensors that have very different power, timing, and noise requirements sharing one small PCB.

## Sensing

Five channels, chosen to cover both the conditions that make ignition likely and the signatures of combustion once it begins.

| Channel | Part | What it's for |
|---|---|---|
| Particulate | SPS30 | Smoke detection — PM1.0 through PM10 mass concentration |
| Lightning | AS3935 | Ignition risk before combustion; storm distance and energy |
| Temperature | SHT31 | Ambient conditions and fire-weather context |
| Humidity | SHT31 | Fuel moisture proxy; low RH plus high temp is the danger window |
| VOC | *(see BOM)* | Volatile organics released by heated vegetation ahead of visible flame |

Sensors sit on a shared I²C bus with the AS3935 and share the MCU with an SPI link. The SPS30 is the power hog of the group by a wide margin and is the reason the board has a switched rail at all — it needs 5 V and far more current than everything else combined, but only during a sample.

Readings feed a confidence score rather than a single-sensor trip threshold, since no one channel is meaningful alone: a humidity reading means little by itself, but a multi-day drying trend plus a temperature spike
