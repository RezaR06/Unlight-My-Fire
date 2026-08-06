# Unlight-My-Fire
A battery-and-solar node that samples its environment on a duty cycle and transmits over LoRa when several independent signatures of combustion appear together.

Compute — Raspberry Pi Pico 2 (RP2350, A3/A4 stepping), castellated SMD module.

Sensing — Sensirion SPS30 (particulate), SGP40 (VOC), SHT31 (humidity/temp), and a Panasonic AMG8833 8×8 thermal array on a shared I²C bus.

Lightning — ScioSense AS3935 on SPI with a 500 kHz LC tank, giving predictive ignition-risk warning ahead of combustion and triggering an elevated-sampling storm mode.

Fusion — On-device 0–100 confidence score against a rolling 24-hour baseline (PM2.5 40%, VOC 30%, thermal 20%, RH 10% inverse), gated on correlated anomalies across ≥2 modalities plus time persistence.

Radio — Seeed Wio-SX1262 (Semtech SX1262, +22 dBm, on-board TCXO), 915 MHz to an external whip via u.FL pigtail. Shares SPI with the AS3935.

Power — Single-cell LiPo, 6 V solar, TI bq24074 charger with dynamic power path. MIC2295 boost for the SPS30 5 V rail at 1.2 MHz, chosen to 
clear the AS3935's 482.5–517.5 kHz window.

Rail partitioning — TPS22918 high-side load switches with default-off pull-downs; TS5A23166 analog switch isolates the SPS30 from I²C so it's electrically absent, not just unpowered, closing the back-powering sneak paths a rail-only switch leaves open.

Instrumentation — INA228 20-bit current monitor on the system rail; MAX17048 fuel gauge for SOC.

Board — 4-layer JLCPCB stackup (L1 signal, L2 solid ground, L3 power pours, L4 ground/overflow) in Altium Designer. Isolation by spatial partitioning, not plane splits.

Firmware — C++, brought up in stages: LoRa link, sensor integration, duty-cycle loop, fusion algorithm, packet assembly, power management, and fault hardening.

Backend — Raspberry Pi gateway with LoRa HAT into InfluxDB, Telegraf, and Grafana for dashboards and alerting.
