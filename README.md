# Charybdis ZMK Keymap

A Colemak-DH based keymap for the Bastard Keyboards Charybdis with integrated trackball, optimized for a 5-thumb cluster layout.

## Features

- **Colemak-DH** alpha layout
- **Home Row Mods** (GUI, ALT, CTL, SFT on home row), urob-style "timeless" timings
- **9 Layers** optimized for the 5-thumb cluster
- **Numpad Layer** on left hand (Miryoku-style)
- **Gaming Layer** with inverted-T arrows on the left half
- **Trackball Integration** for pointer movement and scrolling
- **Bluetooth Support** with 5 device profiles + passkey pairing

## Firmware Stack

| Component | Value |
| --------- | ----- |
| ZMK | `zmkfirmware/zmk` @ `main` |
| Board | `nice_nano//zmk` (Zephyr 4.1 HWMv2 variant) |
| Trackball driver | [`badjeff/zmk-pmw3610-driver`](https://github.com/badjeff/zmk-pmw3610-driver) @ `zmk-0.4` |
| Pointer API | `CONFIG_ZMK_POINTING` + input processors |
| DT compatible | `pixart,pmw3610-alt` |

Layer numbering is shared between the keymap and the trackball input processors
via `config/boards/shields/charybdis/charybdis_layers.h` — if you renumber a
layer, update that header too or scroll/move modes will bind to the wrong layer.

---

## Layout Overview

### Layer 0: BASE (Colemak-DH)
```
┌───────┬───────┬───────┬───────┬───────┐   ┌───────┬───────┬───────┬───────┬───────┐
│   Q   │   W   │   F   │   P   │   B   │   │   J   │   L   │   U   │   Y   │   '   │
├───────┼───────┼───────┼───────┼───────┤   ├───────┼───────┼───────┼───────┼───────┤
│ A/GUI │ R/ALT │ S/CTL │ T/SFT │   G   │   │   M   │ N/SFT │ E/CTL │ I/ALT │ O/GUI │
├───────┼───────┼───────┼───────┼───────┤   ├───────┼───────┼───────┼───────┼───────┤
│ Z/KBD │X/RALT │   C   │   D   │   V   │   │   K   │   H   │   ,   │./RALT │   /   │
└───────┴───────┴───────┼───────┼───────┤   ├───────┼───────┼───────┴───────┴───────┘
                        │ESC/MED│SPC/NAV│  TAB  │ENT/SYM│BSP/NUM│ 🔴 Trackball
                        └───────┴───────┴───────┴───────┴───────┘
```

**Home Row Mods (GACS order):**
- Left hand: GUI → ALT → CTL → SFT
- Right hand: SFT → CTL → ALT → GUI
- urob-style "timeless" timings: `balanced` flavor, 280ms tapping term, 175ms quick-tap
- `require-prior-idle-ms = 150` prevents misfires during fast typing
- `hold-trigger-key-positions` limits holds to opposite-hand + thumb keys
- `hold-trigger-on-release` resolves same-hand rolls as taps
- quick-tap enables tap-then-hold key repeat (tap `A`, then hold to repeat it)

### Layer 1: NAV (Navigation + Clipboard)
*Activated by holding Space*
```
┌───────┬───────┬───────┬───────┬───────┐   ┌───────┬───────┬───────┬───────┬───────┐
│ UNDO  │  CUT  │ COPY  │PASTE  │ REDO  │   │ CAPS  │ HOME  │  UP   │  END  │ PGUP  │
├───────┼───────┼───────┼───────┼───────┤   ├───────┼───────┼───────┼───────┼───────┤
│  GUI  │  ALT  │  CTL  │  SFT  │  TAB  │   │  INS  │ LEFT  │ DOWN  │  UP   │ RIGHT │
├───────┼───────┼───────┼───────┼───────┤   ├───────┼───────┼───────┼───────┼───────┤
│       │ RALT  │       │       │       │   │       │ HOME  │       │  END  │ PGDN  │
└───────┴───────┴───────┼───────┼───────┤   ├───────┼───────┼───────┴───────┴───────┘
                        │       │ ████  │       │ ENTER │  DEL  │
                        └───────┴───────┴───────┴───────┴───────┘
```

### Layer 2: NUM (Numpad - Miryoku style)
*Activated by holding Backspace*
```
┌───────┬───────┬───────┬───────┬───────┐   ┌───────┬───────┬───────┬───────┬───────┐
│   [   │   7   │   8   │   9   │   ]   │   │       │       │       │       │       │
├───────┼───────┼───────┼───────┼───────┤   ├───────┼───────┼───────┼───────┼───────┤
│   ;   │   4   │   5   │   6   │   =   │   │       │  SFT  │  CTL  │  ALT  │  GUI  │
├───────┼───────┼───────┼───────┼───────┤   ├───────┼───────┼───────┼───────┼───────┤
│   `   │   1   │   2   │   3   │   \   │   │       │       │       │ RALT  │       │
└───────┴───────┴───────┼───────┼───────┤   ├───────┼───────┼───────┴───────┴───────┘
                        │   _   │   0   │   -   │ ENTER │ ████  │
                        └───────┴───────┴───────┴───────┴───────┘
```

### Layer 3: SYM (Symbols - Shifted Numpad)
*Activated by holding Enter*
```
┌───────┬───────┬───────┬───────┬───────┐   ┌───────┬───────┬───────┬───────┬───────┐
│   {   │   &   │   *   │   (   │   }   │   │       │       │       │       │       │
├───────┼───────┼───────┼───────┼───────┤   ├───────┼───────┼───────┼───────┼───────┤
│   :   │   $   │   %   │   ^   │   +   │   │       │  SFT  │  CTL  │  ALT  │  GUI  │
├───────┼───────┼───────┼───────┼───────┤   ├───────┼───────┼───────┼───────┼───────┤
│   ~   │   !   │   @   │   #   │   |   │   │       │       │       │ RALT  │       │
└───────┴───────┴───────┼───────┼───────┤   ├───────┼───────┼───────┴───────┴───────┘
                        │   (   │   )   │  TAB  │ ████  │   _   │
                        └───────┴───────┴───────┴───────┴───────┘
```

### Layer 4: MEDIA + FUNCTION KEYS
*Activated by holding Escape (media left, F-keys right, trackball = SCROLL)*
*Q = Toggle GAME mode*
```
┌───────┬───────┬───────┬───────┬───────┐   ┌───────┬───────┬───────┬───────┬───────┐
│ GAME  │       │       │       │       │   │ PSCR  │  F7   │  F8   │  F9   │  F12  │
├───────┼───────┼───────┼───────┼───────┤   ├───────┼───────┼───────┼───────┼───────┤
│ MUTE  │ VOLD  │ VOLU  │ NEXT  │       │   │ SLCK  │  F4   │  F5   │  F6   │  F11  │
├───────┼───────┼───────┼───────┼───────┤   ├───────┼───────┼───────┼───────┼───────┤
│       │ PREV  │ PLAY  │       │       │   │ PAUSE │  F1   │  F2   │  F3   │  F10  │
└───────┴───────┴───────┼───────┼───────┤   ├───────┼───────┼───────┴───────┴───────┘
                        │ ████  │       │       │ LCLK  │ RCLK  │
                        └───────┴───────┴───────┴───────┴───────┘
```

### Layer 5: KEYBOARD CONTROLS
*Activated by combo: Q + ' (both outer top keys) OR hold Z*
```
┌───────┬───────┬───────┬───────┬───────┐   ┌───────┬───────┬───────┬───────┬───────┐
│ BOOT  │ RESET │       │       │       │   │  BT0  │  BT1  │  BT2  │  BT3  │  BT4  │
├───────┼───────┼───────┼───────┼───────┤   ├───────┼───────┼───────┼───────┼───────┤
│       │       │       │       │       │   │BT_CLR │CLR_ALL│       │       │       │
├───────┼───────┼───────┼───────┼───────┤   ├───────┼───────┼───────┼───────┼───────┤
│       │       │       │       │       │   │       │       │       │       │       │
└───────┴───────┴───────┼───────┼───────┤   ├───────┼───────┼───────┴───────┴───────┘
                        │       │       │       │       │       │
                        └───────┴───────┴───────┴───────┴───────┘
```

### Layer 6: GAME (Inverted-T Arrows)
*Toggle from Layer 4: hold ESC, tap Q. Tap Q again to exit GAME mode*
*All dual-role keys become single-purpose; Space stays plain (no NAV layer)*
```
┌───────┬───────┬───────┬───────┬───────┐   ┌───────┬───────┬───────┬───────┬───────┐
│   ·   │   ·   │   ↑   │   ·   │   ·   │   │   J   │   L   │   U   │   Y   │   '   │
├───────┼───────┼───────┼───────┼───────┤   ├───────┼───────┼───────┼───────┼───────┤
│  SFT  │   ←   │   ↓   │   →   │   ·   │   │   M   │   N   │   E   │   I   │   O   │
├───────┼───────┼───────┼───────┼───────┤   ├───────┼───────┼───────┼───────┼───────┤
│  CTRL │   X   │   C   │   D   │   V   │   │   K   │   H   │   ,   │   .   │   /   │
└───────┴───────┴───────┼───────┼───────┤   ├───────┼───────┼───────┴───────┴───────┘
                        │ ESC/M │  ████  │  TAB  │  RET  │  BSP/NUM│
                        └───────┴───────┴───────┴───────┴───────┘
```
Inverted-T arrows: **F=↑, R=←, S=↓, T=→**. **A = Left Shift**, **Z = Left Ctrl**.
Home-row mods and layer-taps are disabled so sustained holds never emit stray
modifiers. Space outputs a plain space and never activates the NAV layer. Holding the
right-thumb Backspace activates the full numpad on the left while the right side keeps
its Game Mode letters (see [Game Mode](docs/game-mode.md)).

### Layer 7: NOSCROLL
*Toggle from Layer 4: hold ESC, tap W. Tap W again to exit.*

Converts trackball movement into scroll events, effectively disabling cursor
movement. Shares the scroll input-processor group with Layer 4 (MEDIA).

```
┌───────┬───────┬───────┬───────┬───────┐   ┌───────┬───────┬───────┬───────┬───────┐
│   ·   │NOSCRL │   ·   │   ·   │   ·   │   │   ·   │   ·   │   ·   │   ·   │   ·   │
├───────┼───────┼───────┼───────┼───────┤   ├───────┼───────┼───────┼───────┼───────┤
│   ·   │   ·   │   ·   │   ·   │   ·   │   │   ·   │   ·   │   ·   │   ·   │   ·   │
├───────┼───────┼───────┼───────┼───────┤   ├───────┼───────┼───────┼───────┼───────┤
│   ·   │   ·   │   ·   │   ·   │   ·   │   │   ·   │   ·   │   ·   │   ·   │   ·   │
└───────┴───────┴───────┼───────┼───────┤   ├───────┼───────┼───────┴───────┴───────┘
                        │   ·   │   ·   │   ·   │ LCLK  │ RCLK  │
                        └───────┴───────┴───────┴───────┴───────┘
```

### Layer 8: GAME_NUM (conditional)
*Activates automatically when GAME + NUM are both active — hold Backspace during Game Mode.*

Full numpad on the left half while the right half keeps its Game Mode letters.
Defined as a `zmk,conditional-layers` entry, not a directly-toggled layer.

```
┌───────┬───────┬───────┬───────┬───────┐   ┌───────┬───────┬───────┬───────┬───────┐
│   [   │   7   │   8   │   9   │   ]   │   │   ·   │   ·   │   ·   │   ·   │   ·   │
├───────┼───────┼───────┼───────┼───────┤   ├───────┼───────┼───────┼───────┼───────┤
│   ;   │   4   │   5   │   6   │   =   │   │   ·   │   ·   │   ·   │   ·   │   ·   │
├───────┼───────┼───────┼───────┼───────┤   ├───────┼───────┼───────┼───────┼───────┤
│   `   │   1   │   2   │   3   │   \   │   │   ·   │   ·   │   ·   │   ·   │   ·   │
└───────┴───────┴───────┼───────┼───────┤   ├───────┼───────┼───────┴───────┴───────┘
                        │   _   │   0   │   -   │   ·   │   ·   │
                        └───────┴───────┴───────┴───────┴───────┘
```

---

## Trackball Features

The integrated trackball replaces the need for a dedicated mouse layer. Pointer
behaviour is set by input processors rather than driver Kconfig, so speed and
scroll tuning live in `charybdis_trackball_processors.dtsi`.

| Feature              | Description                                | Config                          |
| -------------------- | ------------------------------------------ | ------------------------------- |
| **Sensor CPI**       | Hardware resolution                        | `cpi = <2400>`                  |
| **Pointer Movement** | Scaled down from sensor CPI                | `&zip_xy_scaler 1 4` (~600 eff.) |
| **Scroll Mode**      | Layers 4 (MEDIA) + 7 (NOSCROLL)            | `&zip_xy_scaler 1 15` → mapper → `&zip_scroll_scaler 1 3` |
| **Scroll Direction** | Y inverted (natural scrolling)             | `INPUT_TRANSFORM_Y_INVERT`      |
| **Orientation**      | Matches sensor mounting                    | `swap-xy`, `invert-x`, `invert-y` |
| **BLE Report Rate**  | Throttles HID reports over the air         | `REPORT_INTERVAL_MIN=12`        |
| **Power-up Delay**   | Waits for ext-power VCC before sensor init | `INIT_POWER_UP_EXTRA_DELAY_MS=1000` |

Effective pointer speed is `cpi ÷ scaler divisor`. Prefer changing `cpi` over the
scaler — downscaling high-resolution sensor data tracks more smoothly than
upscaling low-resolution data.

`force-awake` is deliberately **not** enabled: it pins the sensor in RUN mode
(~3.5mA continuous) rather than letting it downshift to REST (~50–200µA). The
power-up delay above solves the sensor init race without that battery cost.

---

## Combos

| Combo             | Keys                     | Action                    |
| ----------------- | ------------------------ | ------------------------- |
| Keyboard Controls | Q + '                    | Toggle Layer 5            |
| Caps Word         | Left Shift + Right Shift | Enable Caps Word          |
| Return to Base    | B + J                    | Go to Layer 0             |

**Layer 4 (MEDIA) Toggle:**
| Key (while holding ESC) | Action                              |
| ----------------------- | ----------------------------------- |
| Q                       | Toggle GAME mode (inverted-T arrows) |

---

## Building

This repository uses GitHub Actions to automatically build firmware on push.

### Automatic Build (GitHub Actions)

1. Push changes to this repository
2. Go to **Actions** tab in GitHub
3. Select the latest workflow run
4. Download the `firmware` artifact (zip file)

---

## Flashing the Firmware

After the GitHub Actions build completes:

### 1. Download the Firmware

- Go to the **Actions** tab on your GitHub repository
- Click on the latest successful workflow run
- Scroll down to **Artifacts**
- Download the `firmware.zip` file
- Extract the zip to get `.uf2` files:
  - `charybdis_left-nice_nano__zmk-zmk.uf2`
  - `charybdis_right-nice_nano__zmk-zmk.uf2`
  - `settings_reset-nice_nano__zmk-zmk.uf2`

### 2. Enter Bootloader Mode

For each half of the keyboard:

**Option A: Double-tap reset button**

- Quickly press the reset button twice on the controller

**Option B: Use bootloader key**

- Hold `Z` (or tap the `Q` + `'` combo) for the Keyboard Controls layer, then press BOOT

The controller will appear as a USB drive named `NICENANO` (or similar).

### 3. Flash the Firmware

1. **Left half**: Copy `charybdis_left-nice_nano__zmk-zmk.uf2` to the USB drive
2. Wait for it to disconnect and reconnect
3. **Right half**: Copy `charybdis_right-nice_nano__zmk-zmk.uf2` to the USB drive

The keyboard will automatically reboot after flashing.

### 4. Clean Reflash (when changing ZMK versions or BLE settings)

Persisted settings — Bluetooth bonds, the split pairing, output selection —
survive a normal reflash. When they need clearing:

1. Flash `settings_reset-nice_nano__zmk-zmk.uf2` to the **left** half, wait for unmount
2. Flash `settings_reset-nice_nano__zmk-zmk.uf2` to the **right** half, wait for unmount
3. Flash `charybdis_left-...uf2` to the **left** half
4. Flash `charybdis_right-...uf2` to the **right** half

> **Do not stop after step 2.** The settings reset firmware has Bluetooth
> disabled by design — the keyboard will be invisible to all Bluetooth device
> lists until normal firmware is flashed back on.

### 5. Pair the Halves

If the halves don't connect automatically:

1. Turn off both halves
2. Turn both back on at roughly the same time
3. They should pair within a few seconds

### 6. Connect to Your Computer

- The keyboard appears as `Charybdis` in Bluetooth settings
- Use BT0–BT4 on **Layer 5 (KBD)** to switch between paired devices
- Passkey pairing is enabled: macOS/Windows will show a numeric code —
  **type it on the Charybdis itself** and press Enter
- `BT_CLR` clears the current profile; `BT_CLR_ALL` clears all five

---

## Troubleshooting

### Halves won't connect

1. Flash `settings_reset` to **both** halves, then flash normal firmware to both
   (see [Clean Reflash](#4-clean-reflash-when-changing-zmk-versions-or-ble-settings))
2. Reset both halves at roughly the same time
3. Re-pair following the steps above

### Bluetooth issues

- Make sure you're on the intended BT profile (BT0–BT4 on Layer 5)
- Clear the profile with `BT_CLR`, or all profiles with `BT_CLR_ALL`, then re-pair
- **Also forget the keyboard on the host.** If you clear the bond on only one
  side, the security keys no longer match and reconnection fails silently
- If the keyboard is invisible to Bluetooth entirely, confirm `settings_reset`
  isn't still flashed — it disables Bluetooth by design
- `CONFIG_BT_CTLR_PHY_2M=n` and `CONFIG_ZMK_BLE_PASSKEY_ENTRY=y` are both set as
  [ZMK-documented](https://zmk.dev/docs/troubleshooting/connection-issues)
  workarounds for hosts that refuse to pair

### Keys not registering on one row or column

Matrix wiring, not firmware. Rows and columns are defined in
`charybdis.dtsi` (rows) and each half's `.overlay` (columns):

| Line | Pro Micro | nRF pin |
| ---- | --------- | ------- |
| Row 0 (top) | `18` | P0.26 |
| Row 1 (home) | `5` | P0.06 |
| Row 2 (bottom) | `4` | P0.04 |
| Row 3 (thumbs) | `9` | P0.09 |
| Columns | `19, 20, 10, 6, 7, 8` | P0.27, P0.29, P0.09, P0.15, P0.13, P0.11 |

A whole row dead → check that row's GPIO. A whole column dead → check that
column's GPIO. A single key dead → check the switch and its solder joints.

### Trackball not working

- The trackball is on the right half only.
- Ensure the right half firmware includes the PMW3610 driver.
- Follow the step-by-step diagnosis in [trackball-troubleshooting.md](docs/trackball-troubleshooting.md).

---

## Documentation

- [Game Mode](docs/game-mode.md) — inverted-T arrows, single-purpose keys, Game Mode + numpad.
- [Trackball Troubleshooting](docs/trackball-troubleshooting.md) — diagnosing PMW3610 pointer issues.

---

## Configuration Files

| File                                                                     | Purpose                              |
| ------------------------------------------------------------------------ | ------------------------------------ |
| `config/charybdis.keymap`                                                | Key bindings and layers              |
| `config/charybdis.conf`                                                  | Global ZMK + Bluetooth settings      |
| `config/west.yml`                                                        | ZMK + driver module versions         |
| `config/boards/shields/charybdis/charybdis.dtsi`                         | Matrix transform, kscan rows         |
| `config/boards/shields/charybdis/charybdis_right.conf`                   | Trackball driver settings            |
| `config/boards/shields/charybdis/charybdis_right.overlay`                | SPI, sensor node, orientation, CPI   |
| `config/boards/shields/charybdis/charybdis_layers.h`                     | Layer numbers shared with processors |
| `config/boards/shields/charybdis/charybdis_trackball_processors.dtsi`    | Pointer/scroll input processors      |
| `build.yaml`                                                             | Build matrix for GitHub Actions      |

---

## References

- [ZMK Documentation](https://zmk.dev/docs)
- [ZMK Pointing Devices](https://zmk.dev/docs/features/pointing)
- [ZMK Input Processors](https://zmk.dev/docs/keymaps/input-processors)
- [ZMK Connection Issues](https://zmk.dev/docs/troubleshooting/connection-issues)
- [PMW3610 Driver (badjeff)](https://github.com/badjeff/zmk-pmw3610-driver)
- [urob's timeless home row mods](https://github.com/urob/zmk-config)
- [Bastard Keyboards](https://bastardkb.com/)
