# Charybdis ZMK Keymap

A Colemak-DH based keymap for the Bastard Keyboards Charybdis with integrated trackball, optimized for a 5-thumb cluster layout.

## Features

- **Colemak-DH** alpha layout
- **Home Row Mods** (GUI, ALT, CTL, SFT on home row) with balanced flavor
- **8 Layers** optimized for the 5-thumb cluster
- **Numpad Layer** on left hand (Miryoku-style)
- **Gaming Layer** with inverted-T arrows on the left half
- **Trackball Integration** for pointer movement and scrolling
- **Bluetooth Support** with 5 device profiles

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
- Uses `balanced` flavor with 200ms tapping term (Miryoku default)
- `require-prior-idle-ms` prevents misfires during fast typing

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
*Activated by combo: Q + P (both outer top keys) OR hold Z*
```
┌───────┬───────┬───────┬───────┬───────┐   ┌───────┬───────┬───────┬───────┬───────┐
│ BOOT  │ RESET │       │       │       │   │  BT0  │  BT1  │  BT2  │  BT3  │  BT4  │
├───────┼───────┼───────┼───────┼───────┤   ├───────┼───────┼───────┼───────┼───────┤
│       │       │       │       │       │   │BT_CLR │       │       │       │       │
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
│   ·   │   ·   │   ↑   │   ·   │   ·   │   │       │       │       │       │       │
├───────┼───────┼───────┼───────┼───────┤   ├───────┼───────┼───────┼───────┼───────┤
│   A   │   ←   │   ↓   │   →   │   ·   │   │   M   │   N   │   E   │   I   │   O   │
├───────┼───────┼───────┼───────┼───────┤   ├───────┼───────┼───────┼───────┼───────┤
│   Z   │   X   │   ·   │   ·   │   ·   │   │       │       │       │       │       │
└───────┴───────┴───────┼───────┼───────┤   ├───────┼───────┼───────┴───────┴───────┘
                        │ ESC/M │  ████  │  TAB  │       │       │
                        └───────┴───────┴───────┴───────┴───────┘
```
Inverted-T arrows: **F=↑, R=←, S=↓, T=→**. Home-row mods are disabled on this layer so sustained key holds never emit stray modifiers. Space outputs a plain space and never activates the NAV layer.

---

## Trackball Features

The integrated trackball replaces the need for a dedicated mouse layer:

| Feature                | Description                           | Config Value  |
| ---------------------- | ------------------------------------- | ------------- |
| **Pointer Movement**   | Move cursor with trackball            | Default       |
| **DPI**                | Pointer sensitivity                   | 2400 CPI      |
| **Scroll Mode**        | Layer 4 (MEDIA) - hold ESC            | 150 tick      |
| **Auto Mouse Timeout** | Return to typing mode                 | 400ms         |

---

## Combos

| Combo             | Keys                     | Action                    |
| ----------------- | ------------------------ | ------------------------- |
| Keyboard Controls | Q + P                    | Toggle Layer 5            |
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
  - `charybdis_left-nice_nano_v2-zmk.uf2`
  - `charybdis_right-nice_nano_v2-zmk.uf2`

### 2. Enter Bootloader Mode

For each half of the keyboard:

**Option A: Double-tap reset button**

- Quickly press the reset button twice on the controller

**Option B: Use bootloader key (if configured)**

- Press the BOOT key on the Keyboard Controls layer (Q + P combo, then BOOT)

The controller will appear as a USB drive named `NICENANO` (or similar).

### 3. Flash the Firmware

1. **Left half**: Copy `charybdis_left-nice_nano_v2-zmk.uf2` to the USB drive
2. Wait for it to disconnect and reconnect
3. **Right half**: Copy `charybdis_right-nice_nano_v2-zmk.uf2` to the USB drive

The keyboard will automatically reboot after flashing.

### 4. Pair the Halves

If the halves don't connect automatically:

1. Turn off both halves
2. Turn on the **right half** first (central)
3. Turn on the **left half** (peripheral)
4. They should pair within a few seconds

### 5. Connect to Your Computer

- The keyboard will appear as `Charybdis` in Bluetooth settings
- Use BT0-BT4 keys on Layer 4 to switch between paired devices
- Use BT_CLR to clear the current profile and re-pair

---

## Troubleshooting

### Halves won't connect

1. Clear both halves: Enter bootloader on each, flash fresh firmware
2. Clear Bluetooth bonds: Use BT_CLR on both halves
3. Re-pair following the steps above

### Bluetooth issues

- Make sure you're selecting the correct BT profile (BT0-BT4)
- Try clearing the profile with BT_CLR and re-pairing

### Trackball not working

- The trackball is on the right half only
- Ensure the right half firmware includes the PMW3610 driver

---

## Configuration Files

| File                                                     | Purpose                         |
| -------------------------------------------------------- | ------------------------------- |
| `config/charybdis.keymap`                              | Key bindings and layers         |
| `config/charybdis.conf`                                | Global ZMK settings             |
| `config/boards/shields/charybdis/charybdis_right.conf` | Trackball/pointer settings      |
| `build.yaml`                                           | Build matrix for GitHub Actions |

---

## References

- [ZMK Documentation](https://zmk.dev/docs)
- [ZMK Pointing Devices](https://zmk.dev/docs/features/pointing)
- [PMW3610 Driver](https://github.com/inorichi/zmk-pmw3610-driver)
- [Bastard Keyboards](https://bastardkb.com/)
