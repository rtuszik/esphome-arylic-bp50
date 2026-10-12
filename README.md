# esphome-arylic-bp50

ESPHome package for controlling an [Arylic BP50](https://www.arylic.com/products/bp50-bluetooth-preamplifier) from Home Assistant over Bluetooth LE, using the protocol from Arylic's GoControl app. See [PROTOCOL.md](PROTOCOL.md) for the protocol.

Tested with:

- BP50, firmware `52-8456a6b7-8`
- ESP32-C3 (`esp32-c3-devkitm-1`), ESP-IDF
- ESPHome 2026.9.1

Other ESP32 variants with BLE should work. The B50 may work too, but is untested.

## Usage

```yaml
substitutions:
    bp50_mac: "AA:BB:CC:DD:EE:FF"

packages:
    arylic_bp50: github://rtuszik/esphome-arylic-bp50/arylic-bp50.yaml@main
```

See [example.yaml](example.yaml) for a complete device config.

| Substitution | Default | Description |
|---|---|---|
| `bp50_mac` | | Bluetooth address of the BP50 (required) |
| `bp50_poll_interval` | `60s` | Interval for re-reading volume, input, tone and LED state |

To find the address, flash with a placeholder MAC and look for `Candidate:` lines in the log. They are printed for advertising devices with "50" or "rylic" in their name.

## Entities

| Entity | Type | Notes |
|---|---|---|
| Volume | number | 0–100 |
| Input | select | BT, LINE-IN, PHONO, OPT, HDMI, USBDAC, USBPLAY |
| Bass, Treble | number | −10 to 10 |
| LEDs | switch | |
| Play Pause, Next, Previous | button | Bluetooth source, untested |
| Connected | binary_sensor | Off when the BP50 is in standby |
| BLE link | switch | Connects or disconnects the ESP |
| Raw command | text | Sends a command as-is, e.g. `MXV` |
| Last response | text_sensor | Last reply received |
| Available inputs, Firmware | text_sensor | |

## Limitations

- The BP50 accepts one BLE connection at a time. Turn off `BLE link` to use GoControl.
- The BP50 drops the BLE connection in standby and can't be powered on over BLE. Power on needs IR. `Connected` can be used as the power state.
- There is no mute command.

## License

[MIT](LICENSE). Not affiliated with Arylic.
