# Hosted on
[[cryo]]

# Devices

## CPA1114
Cryostat

# PV list

| PV                      | Description                                                                                                                                                                 | Units |
| ----------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----- |
| `cryo:operatingState`   | Compressor operating state (Ready to start / Starting / Running / Stopping / Error Lockout / Error / Helium Overtemp Cooldown / Power Related Error / Recovered From Error) | —     |
| `cryo:compressorState`  | Compressor motor state (Idle / Running)                                                                                                                                     | —     |
| `cryo:warnings`         | Decoded warnings (compressor's Warning register, text)                                                                                                                      | —     |
| `cryo:alarms`           | Decoded alarms (compressor's Alarm register, text)                                                                                                                          | —     |
| `cryo:waterIn`          | Temperature of cooling water input                                                                                                                                          | K     |
| `cryo:waterOut`         | Temperature of cooling water output                                                                                                                                         | K     |
| `cryo:oil`              | Oil temperature                                                                                                                                                             | K     |
| `cryo:helium`           | Helium temperature                                                                                                                                                          | K     |
| `cryo:lowPressure`      | Pressure Helium Low                                                                                                                                                         | psi   |
| `cryo:lowPressureAvg`   | Pressure Helium Low Avg                                                                                                                                                     | psi   |
| `cryo:highPressure`     | Pressure Helium High                                                                                                                                                        | psi   |
| `cryo:highPressureAvg`  | Pressure Helium High Avg                                                                                                                                                    | psi   |
| `cryo:deltaPressure`    | Delta of Pressure Avgs                                                                                                                                                      | psi   |
| `cryo:motorCurrent`     | Motor Current                                                                                                                                                               | A     |
| `cryo:hours`            | Hours of operation                                                                                                                                                          | hours |
| `cryo:model`            | Compressor model, decoded live from the unit (e.g. `CPA1114`)                                                                                                               | —     |
| `cryo:pressureScale`    | Pressure display unit configured on the compressor (psi / Bar / KPA)                                                                                                        | —     |
| `cryo:temperatureScale` | Temperature display unit configured on the compressor (F / C / K)                                                                                                           | —     |
| `cryo:switch`           | Switches compressor ON and OFF                                                                                                                                              | —     |

### Internal / helper PVs
| PV                  | Description                                    |
| ------------------- | ---------------------------------------------- |
| `cryo:warningsCode` | Raw warning fault code (feeds `cryo:warnings`) |
| `cryo:alarmsCode`   | Raw alarm fault code (feeds `cryo:alarms`)     |
| `cryo:modelRaw`     | Raw model register (feeds `cryo:model`)        |

# Alarms and severity levels

Source: Cryomech PT425–CPA1114 Installation and Operation Manual, Section 7.7.7
("Error and warning set points") and Section 8.2 ("Maintenance schedule").

Severity convention used throughout: **MAJOR** = matches the compressor's own
"Error" conditions (lockout contributors — these stop the compressor).
**MINOR** = matches the compressor's own "Warning" conditions (compressor
keeps running).

## Analog threshold alarms

Each row is one EPICS `ai` record with `HIHI`/`HIGH`/`LOW`/`LOLO` fields.
`HYST` is a single hysteresis value applied to all four limits (EPICS
limitation — the manual's trip/clear gaps are not symmetric, see Notes).

| PV                     | Units | LOLO (MAJOR) | LOW (MINOR) | HIGH (MINOR) | HIHI (MAJOR) | HYST |
| ---------------------- | ----- | ------------ | ----------- | ------------ | ------------ | ---- |
| `cryo:waterIn`         | K     | 277.6        | 280.4       | 302.6        | 316.5        | 9    |
| `cryo:waterOut`        | K     | 277.6        | 280.4       | 316.5        | 324.8        | 8    |
| `cryo:oil`             | K     | 277.6        | 280.4       | 322.0        | 324.8        | 8    |
| `cryo:helium`          | K     | 277.6        | 280.4       | 349.8        | 360.9        | 39   |
| `cryo:lowPressureAvg`  | psi   | 40           | 50          | 240          | 250          | 10   |
| `cryo:highPressureAvg` | psi   | 150          | 170         | 375          | 400          | 20   |
| `cryo:deltaPressure`   | psi   | 50           | 75          | 290          | 300          | 10   |

### Original manual values (°F / psig), for reference

| Condition      | Too Low (trip / clear)        | Running Low   | Running High   | Too High (trip / clear)         |
| -------------- | ----------------------------- | ------------- | -------------- | ------------------------------- |
| Water In Temp  | 40°F (277.6K) / 50°F (283.2K) | 45°F (280.4K) | 85°F (302.6K)  | 110°F (316.5K) / 80°F (299.8K)  |
| Water Out Temp | 40°F (277.6K) / 50°F (283.2K) | 45°F (280.4K) | 110°F (316.5K) | 125°F (324.8K) / 110°F (316.5K) |
| Oil Temp       | 40°F (277.6K) / 50°F (283.2K) | 45°F (280.4K) | 120°F (322.0K) | 125°F (324.8K) / 110°F (316.5K) |
| Helium Temp    | 40°F (277.6K) / 50°F (283.2K) | 45°F (280.4K) | 170°F (349.8K) | 190°F (360.9K) / 120°F (322.0K) |
| Low Pressure   | 40 / 50 psig                  | 50 psig       | 240 psig       | 250 / 240 psig                  |
| High Pressure  | 150 / 170 psig                | 170 psig      | 375 psig       | 400 / 375 psig                  |
| Delta Pressure | 50 / 75 psig                  | 75 psig       | 290 psig       | 300 / 290 psig                  |
## Enumerated state alarms

`cryo:operatingState` (mbbi):

| State | Raw value | Severity |
|---|---|---|
| Ready to start | 0 | NO_ALARM |
| Starting | 2 | NO_ALARM |
| Running | 3 | NO_ALARM |
| Stopping | 5 | NO_ALARM |
| **Error Lockout** | 6 | **MAJOR** |
| **Error** | 7 | **MAJOR** |
| **Helium Overtemp Cooldown** | 8 | **MAJOR** |
| **Power Related Error** | 9 | **MAJOR** |
| Recovered From Error | 15 | NO_ALARM |

## Decoded fault-text alarms

| PV | Severity when active | Notes |
|---|---|---|
| `cryo:warnings` | MINOR | Non-"None" text raises MINOR; clears automatically each scan cycle once the condition resolves. |
| `cryo:alarms` | MAJOR | Same mechanism, MAJOR severity — mirrors the compressor's own Alarm register (the conditions that actually stop it). |

## Maintenance reminder

| PV | Threshold | Severity | Basis |
|---|---|---|---|
| `cryo:hours` | HIHI = 20000 hours | MINOR | CPA1114 is CP1100-series, not CP100 — adsorber replacement interval is every 20,000 hours (CP100-series would be 15,000; Section 8.2). |

Also pushed as a one-time Telegram notification via `TelAlarms.py` when
`cryo:hours` crosses this threshold (edge-triggered, not repeated every poll).

## Notes and known gaps

- **Unit assumption**: limits above are set in Kelvin/psi because that's what
  the compressor is currently configured to report (`cryo:temperatureScale` =
  `K`, `cryo:pressureScale` = `psi`, confirmed live). If the front-panel unit
  setting is ever changed, these hardcoded limits would no longer be correct
  — check `cryo:pressureScale`/`cryo:temperatureScale` if readings look off.
- **Pressure limits are on the `*Avg` PVs**, not the instantaneous
  `cryo:lowPressure`/`cryo:highPressure`. The averaged registers are assumed
  to be what the compressor's own alarm logic filters on; the instantaneous
  ones are display-only and unalarmed to avoid noise-driven chatter.
- **No Static Pressure alarm.** The manual defines one (Too High 300 psig /
  Too Low 100 psig, Running High 280 / Running Low 170), but `CPA.py` never
  decoded a Static Pressure register from the Modbus block this IOC reads,
  so there's no PV to attach it to.
- **No Motor Current alarm.** The manual's "Motor Current Too Low (<5A)"
  check only applies while the compressor is commanded running (idle current
  is normally ~0.2A); a plain `LOLO` would false-alarm at idle. Skipped for
  now — would need gating on `cryo:compressorState`.
- **`HYST` is one value per record** in EPICS `ai` records, but the manual's
  trip/clear gaps differ between the high and low side of each PV (e.g.
  Helium: 39K gap on the high side, 5.6K on the low side). The value chosen
  is based on the high-side (HIHI) gap in each case.
