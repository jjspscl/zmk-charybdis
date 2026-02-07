# Changelog

All notable changes to the ZMK Charybdis firmware configuration.

This is a fork with custom keymap and trackball configuration for the Charybdis split keyboard with PMW3610 trackball sensor.

---

## [0.13.0] - 2026-02-07

### Summary
Reverted to inorichi PMW3610 driver for reliable trackball tracking. The badjeff driver migration in 0.12.0 caused trackball issues.

### Technical Details

#### Driver Reversion
Switched back to inorichi's PMW3610 driver which has built-in scroll/snipe layer support:
```yaml
# west.yml
remotes:
  - name: zmkfirmware
    url-base: https://github.com/petejohanson
  - name: inorichi
    url-base: https://github.com/inorichi
projects:
  - name: zmk
    remote: zmkfirmware
    revision: feat/pointers-move-scroll
  - name: zmk-pmw3610-driver
    remote: inorichi
    revision: main
```

#### Trackball Configuration (Kconfig-based)
Restored all inorichi driver settings:
```properties
CONFIG_PMW3610_CPI=2400
CONFIG_PMW3610_CPI_DIVIDOR=4
CONFIG_PMW3610_ORIENTATION_90=y
CONFIG_PMW3610_SNIPE_CPI=800
CONFIG_PMW3610_SCROLL_TICK=76
CONFIG_PMW3610_INVERT_X=y
CONFIG_PMW3610_SMART_ALGORITHM=y
CONFIG_PMW3610_POLLING_RATE_125_SW=y
```

#### Simplified Overlay
Using driver's built-in layer features instead of ZMK input processors:
```dts
trackball: trackball@0 {
    compatible = "pixart,pmw3610";
    scroll-layers = <4>;
    snipe-layers = <6>;
};
```

#### Board Name Fix
```yaml
# build.yaml
- board: nice_nano
+ board: nice_nano_v2
```

### Files Changed
- `.github/workflows/build.yml` (reverted to simple trigger)
- `build.yaml` (board name fix)
- `config/west.yml` (driver change)
- `config/boards/shields/charybdis/charybdis_right.conf` (Kconfig settings)
- `config/boards/shields/charybdis/charybdis_right.overlay` (simplified)
- `config/boards/shields/charybdis/charybdis_left.conf` (emptied)
- `config/charybdis.conf` (restored BT buffer settings)

### Branch Backup
Previous badjeff configuration saved to `badjeff` branch for reference.

---

## [0.12.0] - 2026-02-04

### Summary
Major simplification of trackball configuration and migration to ZMK 0.4 pointing API.

### Technical Details

#### Pointing API Migration
```diff
# charybdis_right.conf
- CONFIG_ZMK_MOUSE=y
+ CONFIG_ZMK_POINTING=y
```
ZMK 0.4 renamed the mouse subsystem to "pointing" for broader input device support.

#### Driver Version Pinning
```yaml
# west.yml
- revision: main
+ revision: zmk-0.4
```
Pinned `zmk-pmw3610-driver` to the `zmk-0.4` branch for API compatibility.

#### Trackball Sensor Configuration
```dts
trackball: trackball@0 {
    compatible = "pixart,pmw3610-alt";
    spi-max-frequency = <2000000>;
    cpi = <800>;           // Reduced from 2400
    invert-y;              // Changed from invert-x
    force-awake;
    force-awake-4ms-mode;  // NEW: 4ms polling for responsiveness
};
```

#### Simplified Input Listener
Removed all per-layer input processors:
```dts
// BEFORE: Complex layer-based processing
trackball_listener {
    input-processors = <&zip_xy_transform (INPUT_TRANSFORM_X_INVERT)>;
    scroll_mode { layers = <4>; ... };
    snipe_mode { layers = <6>; ... };
    disabled_mode { layers = <7>; ... };
};

// AFTER: Simple listener
trackball_listener {
    compatible = "zmk,input-listener";
    device = <&trackball>;
};
```

#### Left Half Configuration
New file `charybdis_left.conf`:
```properties
CONFIG_ZMK_SPLIT=y
CONFIG_ZMK_SPLIT_ROLE_CENTRAL=n
CONFIG_BT_CTLR_TX_PWR_PLUS_8=y
```

#### Mise Build System Improvements
- Added Zephyr SDK v0.17.0 auto-installation
- Fixed path resolution: `${ZMK_WORKSPACE}/config` → `$PWD/config`
- Added explicit `export ZEPHYR_BASE="$PWD/zephyr"` in build tasks

### Files Changed
- `.gitignore` (29 additions)
- `config/boards/shields/charybdis/charybdis_left.conf` (new, 9 lines)
- `config/boards/shields/charybdis/charybdis_right.conf` (1 change)
- `config/boards/shields/charybdis/charybdis_right.overlay` (45 lines removed)
- `config/west.yml` (1 change)
- `mise.toml` (42 additions/changes)

---

## [0.11.0] - 2026-02-04

### Summary
Added mise task runner configuration and fixed input processor scroll mode.

### Technical Details

#### Mise Configuration
New `mise.toml` with full build automation:
```toml
[tools]
python = "3.11"
cmake = "latest"
ninja = "latest"

[env]
ZEPHYR_BASE = "{{ config_root }}/zephyr"

[tasks.setup]     # Initialize workspace with west
[tasks."build:left"]   # Build left half firmware
[tasks."build:right"]  # Build right half firmware  
[tasks."build:all"]    # Build both halves
[tasks.clean]     # Clean build artifacts
[tasks.flash]     # Flashing instructions
```

#### Input Processor Fix
```dts
scroll_mode {
    layers = <4>;
    input-processors = <&zip_xy_transform (INPUT_TRANSFORM_Y_INVERT)>,
                       <&zip_xy_to_scroll_mapper>,
                       <&zip_scroll_scaler 1 16>;
};
```

### Files Changed
- `config/boards/shields/charybdis/charybdis_right.overlay` (2 changes)
- `mise.toml` (new, 98 lines)

---

## [0.10.0] - 2026-02-04

### Summary
Fixed missing header includes and input processor configuration.

### Technical Details

#### Added Missing Includes
```dts
#include <dt-bindings/zmk/input_transform.h>
#include <input/processors.dtsi>
```

#### Fixed zip_discard Processor
```dts
zip_discard: zip_discard {
    compatible = "zmk,input-processor-scaler";
    #input-processor-cells = <2>;
    type = <INPUT_EV_REL>;
    codes = <INPUT_REL_X INPUT_REL_Y>;
    track-remainders;
};
```

### Files Changed
- `config/boards/shields/charybdis/charybdis_right.overlay`

---

## [0.9.0] - 2026-02-04

### Summary
Updated input processor syntax for ZMK main branch compatibility.

### Technical Details
ZMK main branch changed input processor syntax. Updated to use new API:
- `zip_xy_transform` for axis transformations
- `zip_xy_to_scroll_mapper` for scroll mode
- `zip_xy_scaler` for speed adjustments

### Files Changed
- `config/boards/shields/charybdis/charybdis_right.overlay`

---

## [0.8.0] - 2026-02-04

### Summary
Fixed Bluetooth buffer configuration for Zephyr 4.1 compatibility.

### Technical Details

#### Bluetooth Buffer Constraint
Zephyr 4.1 enforces: `CONFIG_BT_BUF_EVT_RX_COUNT > CONFIG_BT_BUF_ACL_TX_COUNT`

```properties
# charybdis.conf
CONFIG_BT_BUF_EVT_RX_COUNT=20
CONFIG_BT_BUF_ACL_TX_COUNT=16
CONFIG_BT_L2CAP_TX_BUF_COUNT=16
```

### Files Changed
- `config/charybdis.conf` (5 additions, 2 changes)

---

## [0.7.0] - 2026-02-04

### Summary
Fixed syntax error in overlay file.

### Technical Details
Removed stray semicolon causing build failure:
```dts
- };; 
+ };
```

### Files Changed
- `config/boards/shields/charybdis/charybdis_right.overlay`

---

## [0.6.0] - 2026-02-04

### Summary
Updated board name for ZMK main branch compatibility.

### Technical Details
ZMK main branch renamed board identifiers:
```yaml
# build.yaml
- board: nice_nano_v2
+ board: nice_nano
```

### Files Changed
- `build.yaml`

---

## [0.5.0] - 2026-02-04

### Summary
Refined GitHub Actions workflow triggers.

### Technical Details
```yaml
on:
  push:
    branches: [main]
    paths:
      - 'config/**'
      - 'build.yaml'
      - '.github/workflows/build.yml'
```

### Files Changed
- `.github/workflows/build.yml`

---

## [0.4.0] - 2026-02-04

### Summary
Major feature: Migration to badjeff's PMW3610 trackball driver with per-layer input processing.

### Technical Details

#### West Manifest Changes
```yaml
remotes:
  - name: zmkfirmware
    url-base: https://github.com/zmkfirmware
  - name: badjeff
    url-base: https://github.com/badjeff
projects:
  - name: zmk
    remote: zmkfirmware
    revision: main
  - name: zmk-pmw3610-driver
    remote: badjeff
    revision: main
```

#### SPI Configuration for PMW3610
```dts
&pinctrl {
    spi0_default: spi0_default {
        group1 {
            psels = <NRF_PSEL(SPIM_SCK, 0, 8)>,
                    <NRF_PSEL(SPIM_MOSI, 0, 17)>,
                    <NRF_PSEL(SPIM_MISO, 0, 17)>;
        };
    };
};

&spi0 {
    status = "okay";
    compatible = "nordic,nrf-spim";
    cs-gpios = <&gpio0 20 GPIO_ACTIVE_LOW>;
    
    trackball: trackball@0 {
        compatible = "pixart,pmw3610-alt";
        reg = <0>;
        spi-max-frequency = <2000000>;
        irq-gpios = <&gpio0 6 (GPIO_ACTIVE_LOW | GPIO_PULL_UP)>;
        cpi = <2400>;
        invert-x;
        evt-type = <INPUT_EV_REL>;
        x-input-code = <INPUT_REL_X>;
        y-input-code = <INPUT_REL_Y>;
        force-awake;
    };
};
```

#### Per-Layer Input Processing
```dts
trackball_listener {
    compatible = "zmk,input-listener";
    device = <&trackball>;
    
    // Default: X axis inverted for orientation
    input-processors = <&zip_xy_transform (INPUT_TRANSFORM_X_INVERT)>;
    
    // Layer 4 (MEDIA): Scroll mode
    scroll_mode {
        layers = <4>;
        input-processors = <&zip_xy_transform (INPUT_TRANSFORM_Y_INVERT)>,
                           <&zip_xy_to_scroll_mapper>,
                           <&zip_scroll_scaler 1 16>;
    };
    
    // Layer 6 (SNIPE): Precision mode (1/6 speed)
    snipe_mode {
        layers = <6>;
        input-processors = <&zip_xy_scaler 1 6>;
    };
    
    // Layer 7 (NOSCROLL): Disable trackball
    disabled_mode {
        layers = <7>;
        input-processors = <&zip_discard 0 1>;
    };
};
```

#### Kconfig for Right Half
```properties
CONFIG_SPI=y
CONFIG_INPUT=y
CONFIG_ZMK_MOUSE=y
CONFIG_PMW3610_ALT=y
CONFIG_ZMK_EXT_POWER=y
CONFIG_PMW3610_ALT_INIT_POWER_UP_EXTRA_DELAY_MS=500
```

#### Keymap Layer Additions
Added layers 6 (SNIPE) and 7 (NOSCROLL) for trackball mode control:
```c
#define SNIPE 6
#define NOSCROLL 7

MEDIA {
    bindings = <
        &tog SNIPE &tog NOSCROLL ...
    >;
};
```

### Files Changed
- `.gitignore` (1 addition)
- `config/boards/shields/charybdis/charybdis_right.conf` (29 changes)
- `config/boards/shields/charybdis/charybdis_right.overlay` (72 additions)
- `config/charybdis.keymap` (17 additions)
- `config/west.yml` (10 changes)

---

## [0.3.0] - 2026-02-04

### Summary
Keymap refinements and trackball toggle functionality iterations.

### Technical Details

#### Layer Tap Timing Adjustments
```c
#define U_TAPPING_TERM 200
#define QUICK_TAP_TERM 175

&lt {
    tapping-term-ms = <U_TAPPING_TERM>;
    quick-tap-ms = <0>;              // Disabled for layer-taps
    flavor = "balanced";
    require-prior-idle-ms = <150>;   // Prevent misfires
};
```

#### Trackball Toggle Implementation
Added toggle layers for trackball modes accessible from MEDIA layer:
- `&tog SNIPE` - Toggle precision mode
- `&tog NOSCROLL` - Toggle trackball disable

### Commits Included
- `d62ecf5` - Adjust quick tap timing and flavor for layer tap stability
- `e0bbcbe` - Remove trackball toggle layer
- `da05352` - Add trackball toggle functionality
- `83ca79b` - Merge branch

---

## [0.2.0] - 2026-02-04

### Summary
Major documentation and SNIPE layer implementation.

### Technical Details

#### SNIPE Layer for Precision Control
```c
#define SNIPE 6

SNIPE {
    label = "SNIPE";
    bindings = <
        &tog SNIPE &trans ... &mkp LCLK &mkp RCLK
    >;
};
```

Trackball moves at 1/6 speed (~400 effective CPI from 2400 base) for precision work.

#### Home Row Mods Configuration
```c
hrm_left: hrm_left {
    compatible = "zmk,behavior-hold-tap";
    tapping-term-ms = <200>;
    quick-tap-ms = <0>;
    require-prior-idle-ms = <150>;
    flavor = "balanced";
    // Trigger only on opposite hand keys
    hold-trigger-key-positions = <5 6 7 8 9 15 16 17 18 19 25 26 27 28 29 32 33 34>;
    hold-trigger-on-release;
};
```

#### Keymap Flavor Experiments
- `tap-preferred` - For fast typists
- `hold-preferred` - For heavy modifier users
- `balanced` - Final choice (Miryoku-style)

#### README Documentation
Added comprehensive 206-line README with:
- Layer descriptions and visualizations
- Build instructions
- Keymap philosophy explanation

### Commits Included
- `c5f90df` - Toggle functionality for SNIPE mode
- `52be027` - Tap-preferred flavor
- `5b324c0` - Hold-preferred flavor
- `2628e30` - Quick tap and prior idle timing
- `1ca4894` - Replace DOT with UNDER
- `38ae575` - SNIPE layer and documentation
- `db85392` - Tapping/quick tap timing
- `3a3ebbd` - Z key hold for KBD layer
- `2ba2e35` - Layer activation refinements
- `74f9000` - Balanced flavor and layer descriptions
- `341c2ec` - README documentation

---

## [0.1.0] - 2026-02-04

### Summary
Initial fork with complete Charybdis configuration including custom keymap, trackball support, and extensive iterative refinements.

### Technical Details

#### Initial Project Structure
```
config/
├── boards/shields/charybdis/
│   ├── Kconfig.defconfig      # Shield defaults
│   ├── Kconfig.shield         # Shield definition
│   ├── charybdis.conf         # Global config
│   ├── charybdis.dtsi         # Shared device tree
│   ├── charybdis.zmk.yml      # Shield metadata
│   ├── charybdis_left.conf    # Left half config
│   ├── charybdis_left.overlay # Left half device tree
│   ├── charybdis_right.conf   # Right half config (trackball)
│   └── charybdis_right.overlay # Right half device tree
├── charybdis.conf             # User config
├── charybdis.keymap           # Keymap definition
├── info.json                  # Keyboard metadata
└── west.yml                   # West manifest
```

#### 5-Column Layout Adaptation
Modified from standard 6-column to 5-column per hand:
```c
// 30 keys + 5 thumb keys = 35 total
// Row 0: 10 keys (5 + 5)
// Row 1: 10 keys (5 + 5)  
// Row 2: 10 keys (5 + 5)
// Thumb: 5 keys (2 + 3)
```

#### Layer Architecture (8 Layers)
```c
#define BASE 0      // Colemak-DH base layer
#define NAV 1       // Navigation + clipboard
#define NUM 2       // Numpad on left
#define SYM 3       // Symbols (shifted numpad)
#define MEDIA 4     // Media + F-keys + trackball scroll
#define KBD 5       // Keyboard controls (BT, reset)
#define SNIPE 6     // Precision trackball
#define NOSCROLL 7  // Trackball disabled
```

#### Home Row Mods (Miryoku-style)
```c
// Left hand: A=GUI, R=ALT, S=CTRL, T=SHIFT
&hrm_left LGUI A   &hrm_left LALT R   &hrm_left LCTRL S  &hrm_left LSHFT T

// Right hand: N=SHIFT, E=CTRL, I=ALT, O=GUI
&hrm_right RSHFT N  &hrm_right RCTRL E &hrm_right RALT I  &hrm_right RGUI O
```

#### Thumb Cluster Layout
```
Left hand:  [ESC/MEDIA] [SPACE/NAV] [TAB]
Right hand:             [RET/SYM]   [BSPC/NUM]
```

#### Combos
```c
// Q + P = Toggle KBD layer
combo_kbd_layer {
    bindings = <&tog KBD>;
    key-positions = <0 9>;
};

// Both shifts = Caps Word
combo_caps_word {
    bindings = <&caps_word>;
    key-positions = <13 16>;
};

// T + Y = Return to base
combo_base_layer {
    bindings = <&to BASE>;
    key-positions = <4 5>;
};
```

#### Bluetooth Configuration
```properties
CONFIG_BT_CTLR_TX_PWR_PLUS_8=y
CONFIG_BT_PERIPHERAL_PREF_MAX_INT=9
CONFIG_BT_PERIPHERAL_PREF_LATENCY=16
CONFIG_ZMK_BLE_EXPERIMENTAL_CONN=y
CONFIG_ZMK_SPLIT_BLE_CENTRAL_BATTERY_LEVEL_FETCHING=y
CONFIG_ZMK_SPLIT_BLE_CENTRAL_BATTERY_LEVEL_PROXY=y
CONFIG_ZMK_BEHAVIORS_QUEUE_SIZE=512
CONFIG_BT_CTLR_PHY_2M=n  # Stability improvement
```

#### Initial Trackball Configuration (inorichi driver)
Original setup used inorichi's PMW3610 driver with Kconfig-based settings:
```properties
# CPI via Kconfig
CONFIG_PMW3610_CPI=1600

# Scroll layer configuration
CONFIG_PMW3610_SCROLL_LAYER=4

# Snipe layer configuration  
CONFIG_PMW3610_SNIPE_LAYER=6
```

#### Iterative Keymap Refinements (100+ commits)
The initial version went through extensive iteration including:

**Tap Dance Experiments:**
- Numbers as tap dances (single=number, double=shifted)
- Mouse layer access via tap dance
- Capslock tap dance

**Tapping Term Tuning:**
- Started at 200ms
- Experimented with 250ms
- Various quick-tap timings (0, 125, 150, 175ms)

**DPI/CPI Adjustments:**
- 1250 → 1600 → 2400 → 800 (final)
- Scroll tick rate tuning

**Mouse Layer Evolution:**
- Auto mouse layer (removed)
- Dedicated mouse layer with clicks
- Scroll layer for trackball scrolling
- Right click position iterations

**Symbol Layer Iterations:**
- Merged with numbers (reverted)
- Separate symbols layer (final)
- Various symbol placements for Vim optimization

**Modifier Key Evolution:**
- Home row mods on/off cycles
- Thumb key mod-taps
- Dedicated modifier buttons
- Final: HRM with opposite-hand triggering

### Key Commits (Chronological Highlights)
| Commit | Description |
|--------|-------------|
| `6d586e1` | Initial Commit - 15 files, 472 lines |
| `bc11cbf` | Change to 5 columns |
| `eeaee4d` | Initial layout |
| `670376a` | Added HRM behavior |
| `ada3643` | Implement final keymap bindings |
| `8091b1a` | Added scroll function |
| `e4822f9` | Media controls and screenshot |
| `cf9f22c` | Mouse scroll experimental |
| `cc5f0a0` | Auto scroll layer |
| `627b8bf` | Removed HRM, symbols to own layer |
| `580fe4d` | Tap dance behavior |
| `a3965da` | Use homerow mods again |
| `afb0e6e` | Added snipe layer |
| `31c3475` | Keyboard controls layer |
| `2442e30` | Final symbols layout |
| `5d5b6b2` | Increase DPI |

### Files in Initial Release
- `.github/workflows/build.yml` - GitHub Actions CI
- `build.yaml` - ZMK build configuration
- `config/boards/shields/charybdis/*` - Shield configuration (11 files)
- `config/charybdis.conf` - User configuration
- `config/charybdis.keymap` - Keymap (159+ lines)
- `config/info.json` - Keyboard metadata
- `config/west.yml` - West manifest

---

## Version History Summary

| Version | Date | Description |
|---------|------|-------------|
| 0.12.0 | 2026-02-04 | ZMK 0.4 pointing API, simplified trackball |
| 0.11.0 | 2026-02-04 | Mise build automation |
| 0.10.0 | 2026-02-04 | Input transform header fix |
| 0.9.0 | 2026-02-04 | Input processor syntax update |
| 0.8.0 | 2026-02-04 | Zephyr 4.1 BT buffer fix |
| 0.7.0 | 2026-02-04 | Overlay syntax fix |
| 0.6.0 | 2026-02-04 | Board name update |
| 0.5.0 | 2026-02-04 | CI workflow refinement |
| 0.4.0 | 2026-02-04 | PMW3610 driver migration |
| 0.3.0 | 2026-02-04 | Trackball toggle, timing refinements |
| 0.2.0 | 2026-02-04 | SNIPE layer, documentation |
| 0.1.0 | 2026-02-04 | Initial fork (100+ iterative commits) |

---

## Technical Reference

### Pin Assignments (nice!nano v2 / nRF52840)
```
Trackball SPI:
- SCK:  P0.08
- MOSI: P0.17
- MISO: P0.17 (shared with MOSI for 3-wire SPI)
- CS:   P0.20
- IRQ:  P0.06

Matrix (Right Half):
- Cols: Pro Micro pins 19, 20, 10, 6, 7, 8
- Rows: Defined in charybdis.dtsi
```

### Build Commands
```bash
# Using mise
mise run setup        # Initialize workspace
mise run build:left   # Build left half
mise run build:right  # Build right half
mise run build:all    # Build both

# Manual west commands
west build -s zmk/app -b nice_nano -- \
    -DSHIELD=charybdis_left \
    -DZMK_CONFIG="$PWD/config"
```

### Firmware Output
```
build/zephyr/zmk.uf2
firmware/charybdis_left.uf2
firmware/charybdis_right.uf2
```
