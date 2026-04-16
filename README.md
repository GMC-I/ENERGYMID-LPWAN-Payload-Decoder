# LoRaWAN Payload Decoder – Energy Meter

This repository contains a **LoRaWAN uplink payload decoder** for energy meter series [ENERGYMID LPWAN](https://www.gossenmetrawatt.de/produkte/messen-steuern-regeln/energiemanagement/mid-zertifizierte-energiezaehler/energymid-em2281-em2389/energymid-lpwan/) by [GOSSEN METRAWATT](https://gossenmetrawatt.com/).
The decoder converts raw binary payloads into **structured JSON data** including timestamp, energy values, and device error status.

[![ENERGYMID LPWAN](https://dev.gmc-instruments.de/energymid-lpwan-payload-decoder/images/u2289-v039-energymid.png)](https://www.gossenmetrawatt.de/produkte/messen-steuern-regeln/energiemanagement/mid-zertifizierte-energiezaehler/energymid-em2281-em2389/energymid-lpwan/)

The decoder is designed for use in **LoRaWAN network servers** such as:

- The Things Stack (v3)
- ChirpStack
- Actility's ThingPark

## Download

Stable versions are available under [Releases](../../releases).

**[→ Latest Release](../../releases/latest)**

## General Information

- **Uplink port:** `2`
- **Payload structure:** Type‑based (TLV‑like format)
- **Byte order:** Little‑endian for energy values
- **Energy unit:** kWh (converted from Wh)

Payloads may contain multiple data blocks in one message.

## Supported Data Types

Each data block starts with a **Type ID** followed by its data.

### 🕒 Timestamp (`0x24`)

| Bytes | Description |
| ----: | ----------- |
| 1 | Type ID (`0x24`) |
| 7 | Timestamp data |

**Format:**

- Second
- Minute
- Hour
- Day
- Month
- Year (uint16)

Decoded as both individual fields and ISO‑like string.

### ⚡ Import Active Energy (`0x25`)

| Bytes | Description |
| ----: | ----------- |
| 1 | Type ID (`0x25`) |
| 8 | IEEE 754 Double (Wh) |

- Converted to **kWh**
- Output precision: 3 decimal places

### ⚡ Export Active Energy (`0x26`)

| Bytes | Description |
| ----: | ----------- |
| 1 | Type ID (`0x26`) |
| 8 | IEEE 754 Double (Wh) |

- Converted to **kWh**
- Output precision: 3 decimal places

### 🚨 Error Status (`0x54`)

| Bytes | Description |
| ----: | ----------- |
| 1 | Type ID (`0x54`) |
| 1 | Error bitmask |

**Decoded flags:**

- Phase voltage too low
- Phase voltage too high
- Phase current exceeded
- Frequency synchronization error
- Device reset performed
- Internal communication problem
- DC offset too high

## Example from The Things Stack

### Raw Payload (HEX)

`2400000A0F04EA072533333333330CAC40269A999999993D95405409`

### JSON data

```json
{
  "error_status": {
    "dc_offset_too_high": false,
    "frequency_synchronization_error": true,
    "internal_communication_problem": true,
    "phase_current_exceeded": false,
    "phase_voltage_too_high": false,
    "phase_voltage_too_low": true,
    "reset_performed": false
  },
  "export_active_energy": 1.359,
  "import_active_energy": 3.59,
  "timestamp": {
    "day": 15,
    "hour": 9,
    "minute": 59,
    "month": 4,
    "second": 51,
    "timestamp_iso": "2026-04-15 09:59:51",
    "year": 2026
  }
}
```
