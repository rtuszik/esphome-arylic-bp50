# Arylic BP50 Bluetooth LE protocol

Notes from the GoControl Android app (v2.2.3) and testing on a BP50 with firmware `52-8456a6b7-8`.

## GATT

| Role | UUID | Properties |
|---|---|---|
| Service | `52414b4f-4954-11eb-8410-f7496d45b300` | |
| Commands to the BP50 | `52414b4f-4954-11eb-8410-f7496d45b301` | Write without response |
| Replies from the BP50 | `52414b4f-4954-11eb-8410-f7496d45b302` | Notify |

The first 12 hex digits are ASCII for `RAKOIT`, Arylic's parent company. The MTU negotiates to 517. No pairing or handshake is needed.

## Framing

Plain ASCII, one command per write:

- `CMD;` queries a value
- `CMD:value;` sets it
- Replies come back as `CMD:value;` notifications

Each reply arrived in its own notification during testing, but splitting on `;` is safer. Changes made on the unit itself, such as turning the volume knob, are sent as notifications without being asked.

The BP50 drops the connection in standby and there is no way to wake it over Bluetooth.

## Verified on the BP50

| Command | Example reply | Notes |
|---|---|---|
| `VER;` | `VER:52-8456a6b7-8` | Firmware, commit, API level |
| `LST;` | `LST:PHONO,OPT,HDMI,USBPLAY,USBDAC,BT,LINE-IN` | Available inputs |
| `VOL;` `VOL:n;` | `VOL:55` | 0–100 |
| `SRC;` `SRC:name;` | `SRC:PHONO` | Any name from `LST` |
| `BAS;` `BAS:n;` | `BAS:0` | −10 to 10 |
| `TRE;` `TRE:n;` | `TRE:0` | −10 to 10 |
| `LED;` `LED:0;` `LED:1;` | `LED:1` | Front panel LEDs |

## Used by the package, not separately verified

| Command | Meaning |
|---|---|
| `POP;` | Play/pause (Bluetooth source) |
| `NXT;` `PRE;` | Next / previous track |

## Present in GoControl, untested

Names are from the app; meanings in brackets are guesses where the app doesn't make them clear.

| Command | Likely meaning |
|---|---|
| `MID` | Mid tone |
| `BAL` | Balance |
| `MXV` | Maximum volume |
| `VST` | Volume step |
| `VBI` | Virtual bass (intensity) |
| `EQS`, `PEQ`, `CEQ:LST/SAV/DEL/FLT` | EQ presets and custom EQ |
| `BTM`, `BMO`, `BTS` | Bluetooth mode / state (RX/TX) |
| `DMO` | Device mode |
| `TLM` | (unknown toggle) |
| `NAM` | Device name |
| `DSK:TOT/LST/CUR/SEL` | USB playback: track count, list, current, select |
| `KEY:BT_PAIR` | Start Bluetooth pairing |
| `KEY:BT_RESET` | Clear Bluetooth pairings |
| `SYS:REBOOT` | Reboot |
| `SYS:RESET` | **Factory reset** |
| `UPG:READY`, `UPG:CONFIRM` | **Firmware upgrade.** Don't send these. |

Arylic's [UART API](https://developer.arylic.com/uartapi/) documents the same command family for its Wi-Fi modules and is a useful reference for value formats. Not every command there exists on the BP50.

## Not available

- Mute. GoControl reports it as not implemented for this device family.
- Power on. The link is down in standby. `SYS:STANDBY` from the UART API is untested.
