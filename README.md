# Upgrade CR-10 V2/V3 to Klipper with an SKR Mini E3 V3 motherboard
This guide details how to install the BigTreeTech SKR Mini E3 V3.0 motherboard into your Creality CR-10 V2 or CR-10 V3 3D printer, enabling the use of Klipper firmware.

## 🎉 Why would anyone want to do this?

1: Easier Stepper Driver Control: The Creality 2.5.2 motherboard's TMC2208 stepper drivers are wired in standalone mode, meaning you have to physically open the controller box and adjust potentiometers for any changes. The SKR Mini allows you to control its TMC2209 stepper drivers directly from the firmware via UART, making adjustments much simpler.

2: Advanced Features: The newer TMC2209 drivers on the SKR Mini support advanced Klipper features like [linear advance](https://help.prusa3d.com/article/linear-advance_2252) and [sensorless homing](https://all3dp.com/2/klipper-sensorless-homing-simply-explained/), the first one can improve print quality, and the latter is a learning experience for your future printer builds.

3: Wider Community Support: The SKR Mini is a popular upgrade for Ender 3 printers and is used in VORON Design's [Voron V0](https://docs.vorondesign.com/build/electrical/v0_miniE3_v30_wiring.html). This means a much larger community and more readily available documentation and support compared to the less common Creality 2.5.2 board, which I dare you to try to Google search for pinout and wiring diagram.

4: Quieter Operation: The Creality 2.5.2 board wires the hotend and controller box fans directly to the power supply, so they're always on. The SKR Mini has three controllable fan headers, allowing you to manage control box, heatblock, and part cooling fan speeds via firmware. Additionally, the TMC2209 drivers can be set to StealthChop mode for even quieter printing.

5: Faster Klipper Reconnection: In the event of an emergency stop, the SKR Mini board reconnects to your Raspberry Pi host much faster with Klipper firmware (typically 5-30 seconds) compared to the Creality 2.5.2 board (10-60 seconds) in personal experience.

## 🔧 Materials

- Creality CR-10 V2 or CR-10 V3
- BIGTREETECH SKR Mini E3 V3.0
- A micro SD card
- A BL Touch-style ABL probe
- Sonic Pad
- Control box fans
   - If you want to reuse old control box fans
      - 2 xh2.54 JST 2-pins female connectors
      - An xh2.54 JST 2-pins male connector
   - If you want to change to PC 12V fans (Noctua, etc...)
      - An xh2.54 JST 2-pins male connector
      - A 12V 4010 or 4020 fan
      - A 12V 5010 or 5015 fan
      - LM2596 step-down module
- 5 M3x10 screws
- Heat shrinks
 
## 🔧 Wiring Diagrams
### Creality 2.5.2
![Screenshot of my application](images/252-1.jpg)
### SKR Mini E3 V3
![Screenshot of my application](images/skr-1.png)
![Screenshot of my application](images/skr-3.png)
## 🔧 Hardware Setup
1. Configure jumper pins on SKR Mini E3 V3.0.
   - [User manual](https://github.com/bigtreetech/BIGTREETECH-SKR-mini-E3/blob/master/hardware/BTT%20SKR%20MINI%20E3%20V3.0/Hardware/BTT%20SKR%20MINI%20E3%20V3.0%20user%20manual.pdf).
   - For each axis (X, Y, Z) where you want to enable sensorless homing, you'll need to place a jumper on the corresponding pins .
   - Onboard 5V step-down module or external 5V power supply.
   - USB Power (recommended to remove this jumper to prevent the undervoltage warnings).
2. Combine two control box fans into a single xh2.54 JST 2-pins male connector, wire them in parallel.
3. Cut off the extruder heater's connector, as the SKR Mini E3 V3.0 uses screw terminals.
4. Everything else is plug and play!![Screenshot of my application](images/IMG_1463.JPG)
   - The 12864 LCD only uses a single cable to the EXP3 header.
   - The X+ header is the filament sensor.
   - The Z+ header and D11 jumper pin are the BL Touch wires.
## 🔧 Software Setup
1. Install Klipper Firmware on SKR Mini E3 V3.
   1. Download firmware-USB.bin from [here](https://github.com/bigtreetech/BIGTREETECH-SKR-mini-E3/blob/master/firmware/V3.0/Klipper/firmware-USB.bin)
   2. Rename the downloaded file to firmware.bin.
   3. Copy the file to the root directory of a FAT32 formatted micro SD card.
   4. Install the micro SD card into the SKR Mini E3 V3 and power up the board.
   5. The Klipper firmware should be successfully installed on SKR Mini E3 V3. The firmware.bin file on the SD card will be renamed to FIRMWARE.CUR after a successful flash.
2. Install Printer Configuration File
   1. Rename [printer_creality-cr10-v3-SKR-mini-E3-V3.0.cfg](printer_creality-cr10-v3-SKR-mini-E3-V3.0.cfg) to printer.cfg and put it on the root of usb stick.
   2. Put printer.cfg in your config folder via a web interface (Fluid/Mainsail) or SFTP. The config folder location is:
   ```Bash
   ~/printer_data/config/
   ```

4. Configure MCU ID
   1. Find MCU ID.
      1. Connect the SKR Mini E3 V3 board to the Raspberry Pi using a USB cable.
      2. Open a terminal on Raspberry Pi via SSH.
      3. Enter the following command to list connected USB serial devices:
      ```Bash
      ls /dev/serial/by-id/
      ```
      4. Look for an output that starts with /dev/serial/by-id/usb-Klipper_stm32g0b1xx_. and copy it, that is your SKR Mini E3 V3's unique MCU ID. Example Output:
      ```Bash
      usb-Klipper_stm32g0b1xx_4D003C000B50345033313820-if00
      ```
   2. Update printer.cfg
      1. Find the [mcu] section within the printer.cfg file.
      2. Locate the serial: line. It will have a placeholder value similar to the one you copied in the previous step.
      3. Replace the placeholder value with your SKR Mini E3 V3's MCU ID.
      4. Save and restart Klipper.
5. Do [E-step calibration](https://www.klipper3d.org/Rotation_Distance.html) and start printing!

## 🔨 Resources

- [SKR Mini E3 V3 pinout](https://github.com/bigtreetech/BIGTREETECH-SKR-mini-E3/blob/master/hardware/BTT%20SKR%20MINI%20E3%20V3.0/Hardware/BTT%20E3%20SKR%20MINI%20V3.0_PIN.pdf)
- [SKR Mini E3 V3 Klipper config](https://github.com/bigtreetech/BIGTREETECH-SKR-mini-E3/blob/master/firmware/V3.0/Klipper/SKR-mini-E3-V3.0-klipper.cfg)
- [SKR Mini E3 V3 Manual](https://github.com/bigtreetech/BIGTREETECH-SKR-mini-E3/blob/master/hardware/BTT%20SKR%20MINI%20E3%20V3.0/Hardware/BTT%20SKR%20MINI%20E3%20V3.0%20user%20manual.pdf).
- [SKR MINI E3 V3: Analysis and complete guide of this new electronic board revision](https://3dwork.io/en/skr-mini-e3-v3/)
- [SKR MINI E3 V3 Installation & Firmware](https://www.youmaketech.com/bigtreetech-skr-mini-e3-v3-0-mainboard-upgrade-for-ender-3/)
- [CR-10 V3 Klipper config](https://github.com/Klipper3d/klipper/blob/master/config/printer-creality-cr10-v3-2020.cfg)

