# Trackball Troubleshooting

The Charybdis trackball is a PMW3610 optical sensor, present **only on the right half**,
connected over an SPI bus with a data-ready interrupt line. This guide walks through
diagnosis when the trackball does not work or works intermittently.

## Quick triage

Before changing anything, narrow down *where* the failure happens.

1. **Wired vs wireless:** Connect the right half to your computer with USB and use it
   as a keyboard. If the pointer works wired but not over Bluetooth, the problem is
   transport (BLE latency/HID), not the sensor.
2. **Keyboard keys still work?** If keys work but the pointer does not, the failure is
   isolated to the sensor path (power, SPI, IRQ, or optical). If nothing works, suspect
   the right-half MCU or its flash.
3. **MacOS host visible:** The firmware exposes a mouse HID interface. On macOS you can
   confirm the host sees it:

   ```sh
   hidutil list --matching '{"VendorID":7504,"ProductID":24926}'
   ```

   You should see a USB and/or Bluetooth entry showing usage page `1` / usage `2`
   (pointer). If the pointer usage is absent, the HID report descriptor may need a
   re-pair (over BLE) — see [Refreshing the HID descriptor](https://zmk.dev/docs/features/bluetooth#refreshing-the-hid-descriptor).

## Getting firmware logs

The production firmware is built with logging disabled. Build the **debug** right-half
firmware, which enables USB logging plus debug-level input logging. A `charybdis_right-debug`
artifact is produced by CI in `build.yaml` using the `zmk-usb-logging` snippet.

Flash it to the **right half**, connect over USB, and open the CDC-ACM serial port:

```sh
sudo cu -l /dev/tty.usbmodemXXXXX -s 115200  # macOS (see ZMK USB logging docs)
```

Watch at boot for the PMW3610 module (`pmw3610`) messages. These lines carry the
diagnosis:

| Log line                                              | Meaning                                        |
| ----------------------------------------------------- | ---------------------------------------------- |
| `PMW3610 initialized`                                 | Sensor init OK, interrupts armed.              |
| `Can't do self-test` / `Failed self-test (0x…)`       | Sensor not responding to Observation register. |
| `Incorrect product id` / `Cannot obtain product id`   | Wrong sensor, sensor dead, or failed SPI read. |
| `Reg read/write failed on SPI …`                      | SPI bus fault (wiring, level, or clock).       |
| `IRQ GPIO device not ready` / `Cannot configure IRQ`  | Interrupt pin wiring or GPIO config fault.     |
| `SPI CS ctrl failed`                                  | Chip-select GPIO problem.                      |
| `Device is not initialized yet`                       | Motion reported before init finished.          |

Key messages to look for on boot:

- `Setting CPI to 2400` — confirm the configured CPI applied.
- `async_init_step …` — init step progress; a failure stops after the failing step.
- `PMW3610 initialized` — the happy path.

## What each failure points to

### Self-test / product-id failure (sensor or SPI)

The MCU cannot read the PMW3610 Observation or Product-ID register over SPI. Given
`PMW3610 initialized` is absent, check in this order:

1. **Power:** confirm the sensor `VDD`/`3V3` and `GND` are connected and the regulator
   (if any) is enabled via `CONFIG_ZMK_EXT_POWER=y` in `charybdis_right.conf`.
2. **SPI wiring** against `charybdis_right.overlay`:
   - `SCK` → P0.08
   - `MOSI`/`SDIO` → P0.17
   - `MISO`/`SDIO` → P0.17 (shared, single-pin SDIO)
   - `CS` → P0.20 (active low)
   - `IRQ` → P0.06 (active low, pull-up)
3. **Clock:** `spi-max-frequency = <2000000>`. If the 2 MHz rate is marginal, try a
   lower value.
4. **Soldering:** a floating, bridged, or lifted pad on the sensor breakout; reflow and
   inspect under magnification. The ZMK [hardware-issues](https://zmk.dev/docs/troubleshooting/hardware-issues)
   and [connection-issues](https://zmk.dev/docs/troubleshooting/connection-issues) guides
   cover multimeter/tester-shield checks.

### SPI errors after initial success (intermittent)

If the sensor initializes then later fails during motion, the likely causes are:

- **Noise/short on SCK or SDIO** causing corrupted reads → verify with a scope and look
  for ring/shorted traces.
- **IRQ dropping** → confirm `irq-gpios` is `GPIO_ACTIVE_LOW | GPIO_PULL_UP`. A floating
  IRQ line causes lost motion interrupts.
- **Sensor power sag** under load → measure VDD at the sensor while moving; stabilize
  with bulk capacitance if it droops.

### Works after boot, stops after idle

The driver powers the sensor down through REST states. If the sensor fails to wake:

- Try `CONFIG_PMW3610_FORCE_AWAKE=y` as a test (higher power draw, but confirms the
  wake path is the issue).
- Check the run/REST downshift timings in `charybdis_right.conf`. The current profile
  uses a long `CONFIG_PMW3610_RUN_DOWNSHIFT_TIME_MS=3264` and relies on the default
  REST handling. The PMW3610 datasheet downshift/sample values may need tuning if the
  sensor misses the wake interrupt.

### Stutter / jumping that keys don't show

- **Polling rate:** `CONFIG_PMW3610_POLLING_RATE_125_SW=y` merges accumulated samples.
  If motion feels choppy over BLE, connection latency (not the sensor) is the usual
  culprit — `CONFIG_ZMK_BLE_PERIPHERAL_PREF_LATENCY` is tuned in `charybdis.conf`.
- **Orientation/axis:** verify `CONFIG_PMW3610_ORIENTATION_90` and the invert flags
  match your physical mounting; a reversed axis looks like the ball is "dead" in some
  directions.
- **Surface/ball:** test on a clean matte surface and confirm the ball is clean; the
  optical path must be unobstructed.

## Config reference

`config/boards/shields/charybdis/charybdis_right.conf` and
`config/boards/shields/charybdis/charybdis_right.overlay` hold the entire sensor setup.
The scroll layer is hard-coded to layer 4 (`scroll-layers = <4>`); SNIPE was removed in
favor of the Game Mode layer.

## Useful references

- [ZMK Pointing Devices](https://zmk.dev/docs/features/pointing)
- [ZMK USB Logging](https://zmk.dev/docs/development/usb-logging)
- [ZMK Hardware Issues](https://zmk.dev/docs/troubleshooting/hardware-issues)
- [inorichi/zmk-pmw3610-driver](https://github.com/inorichi/zmk-pmw3610-driver)
- [PMW3610 kernel driver (upstreamable, reference for power states)](https://github.com/badjeff/zmk-pmw3610-driver)