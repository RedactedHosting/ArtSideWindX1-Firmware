# ASWX1-FW-MOD  
**Artillery Sidewinder X1 Firmware Mod**  


<img align="right" width="175" src="https://github.com/MarlinFirmware/Marlin/raw/2.0.x/buildroot/share/pixmaps/logo/marlin-250.png" style="max-width:100%;">

<img align="left" width="175" src="https://github.com/pinguinpfleger/ASWX1-FW-MOD/blob/2.0.x/artillery_logo_brand.png?raw=true" style="max-width:100%;">

 
The ASWX1-FW-Mod is an optimization for the Artillery Sidewinder X1 3D printer.  
The Artillery Sidewinder X1 is delivered with Marlin 1.19 [link](http://www.artillery3d.com/DownLoad/15688.html) and deactivated EEPROM memory function `M500`.  
  
This optimized firmware is based on [Marlin Firmware Version 2.0.x](https://github.com/MarlinFirmware/Marlin/tree/2.0.x)  
and on Marlin [Artillery Sidewinder X1 config](https://github.com/MarlinFirmware/Configurations/tree/master/config/examples/Artillery/Sidewinder%20X1)  

<br>

There is also an [optimized firmware for Artillery Sidewinder X1 touch display](https://github.com/pinguinpfleger/ASWX1-TFTFW-MOD) which you can install too but it is optional.  

## This Fork

This fork is configured for my Artillery Sidewinder X1 with these hardware assumptions:

- Stock Artillery Sidewinder X1 mainboard and motion hardware
- No BLTouch or other bed probe
- All-metal hotend
- 100k hotend thermistor using Marlin `TEMP_SENSOR_0 1`

Local firmware changes:

- Hotend maximum temperature is set to `300C`
- Hotend thermal protection hysteresis is set to `6C`
- PLA preheat is set to `225C` hotend and `60C` bed
- PETG preheat is set to `260C` hotend and `60C` bed
- The active `mega2560` PlatformIO environment has the obsolete `TMC26XStepper` dependency removed, because the old URL returns 404 and this stock Sidewinder X1 build does not need it
- GitHub Actions workflows from the upstream Marlin project were removed from this fork to avoid unnecessary scheduled or pull-request automation

The precompiled firmware for this setup is:

```text
ASWX1-FW-MOD-allmetal-300C.hex
```

## Improvements  

1. **Save to EEPROM**  
   Enabled EEPROM `M500` to persist settings.  
   Now you can store PIDs and Z-Offsets to EEPROM  

2. **LIN_ADVANCE activated**  
    Linear Advance brings you better dimensional precision due to reduced bleeding edges.  
    Higher printing speeds are possible without any loss of print quality - as long as your extruder can handle the needed speed changes.  
    Visible and tangible print quality is increased even at lower printing speeds.  
    No need for high acceleration and jerk values to get sharp edges.  
   Read https://marlinfw.org/docs/features/lin_advance.html for more details and how to calibrate.  
   By default the K_Factor is set to 0, so it is disabled.  
   To enable it using gcode you should first calibrate your specific K factor.  
   You can do this [here](https://marlinfw.org/tools/lin_advance/k-factor.html). Accordingly set the K factor within your slicer using e.g. `M900 K0.2`  

4. **S_CURVE_ACCELERATION activated**  
   This option eliminates vibration during printing by fitting a Bézier curve to move acceleration, producing much smoother direction changes.  
  
5. **ADAPTIVE_STEP_SMOOTHING activated**  
    Adaptive Step Smoothing increases the resolution of multi-axis moves, particularly at step frequencies below 1kHz (for AVR) or 10kHz (for ARM), where aliasing between axes in multi-axis moves causes audible vibration and surface artifacts.
    The algorithm adapts to provide the best possible step smoothing at the lowest stepping frequencies.  



## Individual adjustments  
Individual adjustments can be made in [Configuration.h](/Marlin/Configuration.h) and [Configuration_adv.h](/Marlin/Configuration_adv.h)  
  
Examples can be found in the links below.  
[Enabling BL-Touch](https://5020dafe-17d8-4c4c-bf3b-914a8fdd5140.filesusr.com/ugd/f5a1c8_d40d077cf5c24918bd25b6524f649f11.pdf), also look at [Issue \#6](https://github.com/pinguinpfleger/ASWX1-FW-MOD/issues/6#issuecomment-619517281)  
[E3D Hemera - Artillery (Evnovo) Sidewinder X1 Firmware Modification](https://e3d-online.dozuki.com/Guide/Hemera+Artillery+(Evnovo)+Sidewinder+X1+Firmware+Modification/121?fbclid=IwAR2BITq4oZrkuaCNYb0VciskU4G2GWVfzusxQzLEnCxz8Fv-EvAqf_lkZS4) 
  
[Read more about configuring Marlin](https://marlinfw.org/docs/configuration/configuration.html)  
  
Of course the firmware must be recompiled than.  
There are serveral ways to compile.

### Recreate This Fork's Changes

These are the basic tools and steps used to create this firmware from the original project:

1. Install [Git for Windows](https://git-scm.com/download/win).
2. Install Python, then install PlatformIO:

```powershell
py -m pip install --user -U platformio
```

3. Fork the original repository on GitHub, then clone your fork:

```powershell
git clone https://github.com/YOUR_USERNAME/YOUR_FORK_NAME.git
cd YOUR_FORK_NAME
```

4. Edit the configuration files:

- `Marlin/Configuration.h`
  - Set `HEATER_0_MAXTEMP` to `300`
  - Keep `TEMP_SENSOR_0 1` for a standard 100k thermistor
  - Set `PREHEAT_1_LABEL` to `"PLA"`
  - Set `PREHEAT_1_TEMP_HOTEND` to `225`
  - Set `PREHEAT_1_TEMP_BED` to `60`
  - Set `PREHEAT_2_LABEL` to `"PETG"`
  - Set `PREHEAT_2_TEMP_HOTEND` to `260`
  - Set `PREHEAT_2_TEMP_BED` to `60`
- `Marlin/Configuration_adv.h`
  - Set `THERMAL_PROTECTION_HYSTERESIS` to `6`
- `platformio.ini`
  - In `[env:mega2560]`, remove the old `TMC26XStepper=https://github.com/trinamic/TMC26XStepper/archive/master.zip` line if the build fails with a 404

5. Compile the firmware:

```powershell
py -m platformio run
```

6. Copy the compiled firmware to a friendly filename:

```powershell
Copy-Item .pio\build\mega2560\firmware.hex ASWX1-FW-MOD-allmetal-300C.hex -Force
```

7. Commit and push the changes:

```powershell
git add Marlin/Configuration.h Marlin/Configuration_adv.h platformio.ini ASWX1-FW-MOD-allmetal-300C.hex
git commit -m "Update Sidewinder X1 all-metal hotend firmware"
git push
```

8. For this personal fork, remove or disable the upstream Marlin GitHub Actions workflows under `.github/workflows`. They are useful for Marlin project development, but they are not needed for a local Sidewinder X1 firmware fork and can introduce avoidable third-party automation risk.

\[Linux / Mac\]  
An easy one is [platformio CLI](https://docs.platformio.org/en/latest/installation.html#installation-methods) command.  
To complile you just need execute `platformio run` in the root folder of this repository (where platformio.ini is also located).  
The new compiled firmware is saved here: .pio/build/megaatmega2560/firmware.hex  

\[Windows\]  
There is a great instruction how to [use Arduino-IDE on Marlin.org](https://marlinfw.org/docs/basics/install_arduino.html).  
This should be the easiest way on Windows.  
  
Board: "Arduino/Genuino Mega or Mega 2560"  
Processor: "ATmega2560 (Mega 2560)"  

Customize your configs, use "Sketchs -> Export compiled Binary", flash

  
## Flashing  
**Important**: Don't forget to read and backup your current EEPROM-settings with `M503`!  
  
The display and the USB-Port are sharing the same wires so flashing the motherboard-firmware need some extra work.  
There are two ways possible to flash the firmware.  
  
### 1. Disconnect the display  
**be careful, DISCONNECT the 230V power cord, you do not need it for flashing, everything will be savely powered using the USB Connection**  
Either compile the attached source or flash the precompiled hex file. For flashing the precompiled hex file you can e.g. install and use Prusa Slic3r 2.x. In order to be able to flash the firmware you must unscrew the bottom plate of you printer **(danger, 230V connectors, unplug power cord first)** and disconnect the MKS LCD cable. Otherwise it wont let you flash as both, the TFT and Flasher communicate using serial. After flashing and powering off you simply reconnect the tft+enclosure fan and install the bottom plate again.

  
### 2. Loop method
With this method we try to talk to the motherboard before the display is ready to listen. 
You have to use a Linux or Mac mashine for this.
  
The [flash.sh](/flash.sh) script is trying to flash the command in a loop until the command finishs succesfully.  
Steps:  
- Make sure Artillery Sidewinder X1 is unplugged from the power supply.  
- Unplug USB Cable too.  
- Connect your linux box (or mac) with the printer USB-Port  
- Place firmware.hex and flash.sh in same directory and execute flash.sh.  
- You will see timeout errors thats normal.  
- Plug USB Cable  
- Leave flash.sh running and unplug / plug USB Cable or hit the reset button until the flash.sh finishs  
  
A detailed explanation of this procedure is available from [joskfg at Github](https://www.thingiverse.com/make:734108/)
  
  
### Reset to factory defaults  
I recommend to reset the newly flashed firmware to its defaults and overwrite any older settings.  
***Don't forget to read your settings by `M503` and copy/save them somewhere.***  
The gcode command to reset the firmware to the hardcoded defaults is `M502`,  
followed by `M500` to save these default setting to EEPROM.  
After that you can restore your settings from your M503-Backup e.g. `M92 X80.12 Y80.12 Z399.78 E420.00`  
Save again by `M500` and finally reload all stored data from Eeprom by `M501`  

You can execute the gcode commands using a terminal program like Arduino-IDE, [Pronterface](https://www.pronterface.com/)) or using the Terminal Tab in Octoprint.    
  
<br><hr>  

## Credits  
The repository here is the continuation of the MarlinFW from [**Robscar's firmware mod** at Thingiverse](https://www.thingiverse.com/thing:3856144).  
The modified firmware for the Makerbase MKS-TFT 3.2 touch display has been seperated to an own repository:  
https://github.com/pinguinpfleger/ASWX1-TFTFW-MOD
  

## Links

### Slicer Machine & Profile Settings
https://3d-nexus.com/resources/file-archives/category/8-artillery-evnovo

### Youtube
RICS 3D Marlin 2 https://www.youtube.com/watch?v=JlgykMHhMzw  
Waggster Mod https://www.youtube.com/watch?v=ynm8inRMVkE

### Other Mods
Waggster Mod (BL-Touch) https://pretendprusa.co.uk/index.php?action=downloads;cat=5  
3D Nexus (Mesh Bed Leveling, Marlin 1.1.x) https://3d-nexus.com/resources/file-archives/download/5-printer-firmware/11-artillery-swx1-marlin-1-1-9-advanced-firmware-and-gui
3D Print Beginner (also derived from Robscar's firmware) https://3dprintbeginner.com/sidewinder-x1-firmware/

<br><hr>  

## Disclaimer, use at your own risk!  
There are inherent dangers of upgrading your firmware and config files. I caution you to make sure that you completely understand the potential risks before applying/uploading any of the files provided to your 3D-Printer. The firmware and Config Files are provided "as is" without warranty of any kind, either express or implied.
