# zmk-config

Personal ZMK user config repo. Firmware is built by GitHub Actions on every
push (see `.github/workflows/build.yml`); no local Zephyr SDK install needed.

## `single_key` — single-key wireless PoC

Minimal single-switch wireless keyboard PoC on a Seeed XIAO nRF52840.

### Hardware

- MCU: Seeed XIAO nRF52840 (ZMK board: `seeeduino_xiao_ble`, ZMK's stable
  `v0.3` branch — note this differs from the `xiao_ble/nrf52840/zmk` name
  used on ZMK's unstable `main` branch)
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
`single_key-seeeduino_xiao_ble-zmk.uf2` (see the `build.yaml` matrix).
Download it from the workflow run's "Artifacts" section.

### Flashing

1. Double-press the XIAO's reset button to enter the UF2 bootloader
   (the board enumerates as a USB mass storage drive).
2. Drag and drop the downloaded `.uf2` file onto that drive.

### Keymap

One key, bound to `&kp A` (`boards/shields/single_key/single_key.keymap`).
Edit and re-push to change it.

## `optokbd` — split wireless keyboard

Firmware for [optokbd](https://github.com/hhiroshell/optokbd) (hardware
v0.2.0): a split keyboard with a Seeed XIAO nRF52840 (`seeeduino_xiao_ble`)
in each half. Each half has 25 keys: 3 rows x 7 columns plus 4 thumb keys.
The left half is the central (connects to the host); the right half is a
peripheral that connects to the left half.

### Hardware notes

- Matrix: 4 rows x 7 columns per half, diodes col2row. On both halves `C0` is
  the outermost (pinky side) column.
- The XIAO pins for each row/column are in
  `boards/shields/optokbd/optokbd_left.overlay` and `optokbd_right.overlay`.
  They assume the JST SH 11-pin cable between the matrix board and the
  controller board connects matrix pin k to controller pin 12 - k. **If the
  cable turns out to connect pin k to pin k, the pins must be redone.**

### Build and flash

GitHub Actions builds `optokbd_left-seeeduino_xiao_ble-zmk.uf2` and
`optokbd_right-seeeduino_xiao_ble-zmk.uf2`. Flash each one to its half the
same way as above (double-press reset, drag and drop the `.uf2`).

If the halves stop connecting to each other, flash
`settings_reset-seeeduino_xiao_ble-zmk.uf2` to both halves, then flash the
regular firmware again.

### Keymap

`boards/shields/optokbd/optokbd.keymap`, ported from the QMK keymap of bias.
bias has 6 columns per half, so its keys sit on the outer 6 columns. The
innermost column of the left half has mouse buttons (middle, left and right
click from the top); that of the right half is unassigned (`&none`).

- `LOWER` / `RAISE` thumb keys: momentary layers that also tap `LANG2` /
  `LANG1` when released without pressing any other key.
- `LOWER` + `RAISE` position (either order) enters the `FUNC` layer.
- `FUNC` layer, left `Ctrl/Esc` position: toggles the `ADMIN` layer. Besides
  bias's bootloader key (top-left), `ADMIN` has a bootloader key for the right
  half (top-right), BLE profile selection 0-4 (left top row) and clearing the
  current profile's pairing (left bottom row, `Z` position).
