# Artillery Sidewinder X1 Local Firmware Build

Source base: pinguinpfleger/ASWX1-FW-MOD, branch `2.0.x`.

## Local hardware assumptions

- Printer: Artillery Sidewinder X1
- Mainboard/electronics: stock
- Probe: none
- Hotend: all-metal
- Hotend thermistor: 100k, using Marlin `TEMP_SENSOR_0 1`

## Local firmware changes

- `Marlin/Configuration.h`
  - `HEATER_0_MAXTEMP` changed from `275` to `300`
- `platformio.ini`
  - Removed the obsolete `TMC26XStepper=https://github.com/trinamic/TMC26XStepper/archive/master.zip` dependency from `[env:mega2560]` because that URL returns 404 and this stock X1 build does not use TMC26X drivers.

## Build

Run from this folder:

```powershell
py -m platformio run
```

Generated firmware:

```text
.pio/build/mega2560/firmware.hex
ASWX1-FW-MOD-allmetal-300C.hex
```

## Before flashing

1. Back up current EEPROM settings with `M503`.
2. Flash `ASWX1-FW-MOD-allmetal-300C.hex`.
3. After flashing, reset EEPROM defaults with `M502`, save with `M500`, then reload with `M501`.
4. Re-run hotend PID tuning for the all-metal hotend and save with `M500`.

The Sidewinder X1 display and USB share serial wiring. If flashing from Windows fails, disconnect the TFT cable from the mainboard before flashing, with printer AC power unplugged.
