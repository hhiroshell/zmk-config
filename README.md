# zmk-config

Personal ZMK user config repo. Firmware is built by GitHub Actions on every
push (see `.github/workflows/build.yml`); no local Zephyr SDK install needed.

## `single_key` — single-key wireless PoC

Minimal single-switch wireless keyboard PoC on a Seeed XIAO nRF52840.

### Hardware

- MCU: Seeed XIAO nRF52840 (ZMK board: `xiao_ble/nrf52840/zmk`)
- Switch: 1x momentary, normally-open
- Wiring (no soldering, no breadboard — SMD IC test hook grabbers clamped
  directly onto the XIAO's castellated pads):
  - `D0` (`P0.02`) → one switch terminal
  - `GND` → the other switch terminal
- No external pull-up resistor; the SoC's internal pull-up is used
  (`GPIO_PULL_UP` in `boards/shields/single_key/single_key.overlay`)
- Power: USB-C, or a 3.7V LiPo via the on-board JST connector

### Build

Pushing to this repo triggers GitHub Actions, which builds
`single_key-xiao_ble-nrf52840-zmk.uf2` (see the `build.yaml` matrix). Download
it from the workflow run's "Artifacts" section.

### Flashing

1. Double-press the XIAO's reset button to enter the UF2 bootloader
   (the board enumerates as a USB mass storage drive).
2. Drag and drop the downloaded `.uf2` file onto that drive.

### Keymap

One key, bound to `&kp A` (`boards/shields/single_key/single_key.keymap`).
Edit and re-push to change it.
