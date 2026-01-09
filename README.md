# pico-mpu6050-ssd1331-dashboard



> **Embedded system project using Raspberry Pi Pico to interface MPU6050 IMU and SSD1331 OLED display — real-time motion and sensor data visualization.**

---

## Table of Contents

1. [Overview](#overview)
2. [Features](#features)
3. [Hardware Required](#hardware-required)
4. [Wiring / Pinout (example)](#wiring--pinout-example)
5. [Software Requirements](#software-requirements)
6. [Installation](#installation)
7. [Usage](#usage)
8. [Code Structure & Key Files](#code-structure--key-files)
9. [Calibration & Tuning](#calibration--tuning)
10. [Troubleshooting](#troubleshooting)
11. [License](#license)
12. [Contributing](#contributing)

---

## Overview

This repository contains a MicroPython project for the **Raspberry Pi Pico** that reads motion data from an **MPU6050** (IMU) and displays live sensor output and simple graphics on an **SSD1331** OLED display. The project demonstrates I2C communication with the MPU6050 and SPI communication with the SSD1331, basic filtering of accelerometer/gyroscope data, and a small dashboard UI on the OLED.

The code is suitable for students, hobbyists, and embedded developers who want a compact example of combining sensors and display output on a pico-class board.

---

## Features

* Read raw accelerometer & gyroscope data from MPU6050
* Compute basic orientation values (pitch / roll) using complementary filter
* Render numeric sensor values and simple graphics (bars, vectors) on SSD1331 OLED
* Configurable sampling rate and display refresh
* Easy-to-follow MicroPython code, ready to flash to a Pico

---

## Hardware Required

* Raspberry Pi Pico (or compatible RP2040 board)
* MPU6050 (Gyro + Accelerometer module)
* SSD1331 0.96" RGB OLED (or compatible SSD1331-based module)
* Connecting wires / breadboard
* 3.3V power supply (Pico provides 3.3V)

> **Note:** SSD1331 typically uses SPI. MPU6050 uses I2C. Confirm your module pin labeling and voltage levels before wiring.

---

## Wiring / Pinout (example)

Below is an **example** wiring. Adjust pins to match your breakout/module and code configuration.

### MPU6050 (I2C)

```
MPU6050 VCC  -> Pico 3V3
MPU6050 GND  -> Pico GND
MPU6050 SDA  -> Pico SDA pin (example: GP0 or GP4)
MPU6050 SCL  -> Pico SCL pin (example: GP1 or GP5)
```

### SSD1331 (SPI) — example 4-wire SPI + control pins

```
SSD1331 VCC  -> Pico 3V3
SSD1331 GND  -> Pico GND
SSD1331 SCLK -> Pico SCLK (SPI MOSI/CLK pins, example: GP10/GPIO14 depending on chosen SPI)
SSD1331 MOSI -> Pico MOSI
SSD1331 CS   -> Pico any free GPIO (chip select)
SSD1331 DC   -> Pico any free GPIO (data/command)
SSD1331 RST  -> Pico any free GPIO (optional reset)
```

**Important:** The actual GPIO numbers depend on which SPI/I2C peripheral you select in your code — the examples in `main.py` show the pins used in this repo. Change the pins in the code if your wiring differs.

---

## Software Requirements

* **MicroPython** firmware for Raspberry Pi Pico (UF2 flashed)
* **Thonny** (recommended) or any serial file transfer tool (ampy, rshell) to upload files
* This project uses only standard MicroPython libraries and a few helper modules included in `lib/`.

---

## Installation

1. Flash MicroPython UF2 to your Pico (follow Raspberry Pi's official instructions).
2. Open Thonny (or your preferred IDE) and select the Pico as the target interpreter.
3. Extract the repository files from this project and open the folder in Thonny's file browser.
4. Upload the following files to the Pico's root:

   * `main.py`
   * `mpu6050.py` (driver)
   * `ssd1331.py` (driver)
   * `config.py` (pin and settings)
   * `lib/` (any helper modules)

Alternatively, copy the whole folder using `ampy` or `rshell`.

### Example: Upload with `rshell`

```bash
# from your PC terminal (if rshell is installed)
rshell
cp -r ./pico-project /pyboard
```

---

## Usage

1. Power the Pico and open a serial console (Thonny REPL). The script should start automatically.
2. You will see a simple dashboard on the OLED showing numeric accelerometer/gyro values and small graphical indicators.
3. To adjust update rate, open `config.py` and change `SAMPLE_RATE` and `DISPLAY_REFRESH` values, then re-upload.

### Common Commands (in REPL)

```python
# restart main
import machine
machine.reset()

# run main manually
import main
main.run()
```

> Tip: Put the Pico into safe mode (hold BOOTSEL) to replace files if your script interferes with uploading.

---

## Code Structure & Key Files

```
/ (repo root)
├─ main.py           # Entry point: initialize sensors & display, main loop
├─ mpu6050.py        # MPU6050 driver (I2C read, DMP bypass simple read)
├─ ssd1331.py        # SSD1331 driver (SPI commands, basic drawing primitives)
├─ config.py         # Pin configuration and constants
├─ lib/              # Utility modules (filters, math helpers)
├─ assets/           # Images/screenshots for README (optional)
└─ README.md         # This file
```

**`main.py`**: Handles initialization, loop timing, reading MPU6050, filtering, and calling draw routines.

**Drivers**: The `mpu6050.py` and `ssd1331.py` drivers are small and documented; they expose straightforward APIs (`mpu.read_accel()`, `mpu.read_gyro()`, `display.text()`, `display.blit()`).

---

## Calibration & Tuning

* **Gyro bias:** Allow the device to sit still at power-up. `main.py` includes a short calibration routine that averages several samples to compute gyro bias.
* **Filter constants:** A complementary filter is used to fuse accel & gyro — tune `ALPHA` in `config.py` (value range 0..1) for responsiveness vs stability.
* **Display refresh:** Lower the display refresh rate to reduce CPU load if sampling faster than the display can update.

---

## Troubleshooting

* **No display output:**

  * Check power (3.3V) and GND.
  * Verify SPI pins and CS/DC wiring.
  * Confirm the SSD1331 module accepts 3.3V logic (do not feed 5V).

* **No sensor data / I2C errors:**

  * Ensure SDA/SCL lines are connected and pull-ups present (some modules include pull-ups).
  * Confirm correct I2C address (0x68 common for MPU6050). Use a bus scanner script to confirm.

* **Script won't upload:**

  * Hold BOOTSEL and connect Pico to enter mass storage, then re-upload UF2 or files.

---

## License

This project is released under the **MIT License**. See [LICENSE](LICENSE) for details.

---

## Contributing

Contributions are welcome! Please open issues for bugs or feature requests and submit pull requests for improvements. Keep code style consistent and include short descriptions for changes.

---

## Acknowledgements & References

* MPU6050 datasheet and community MicroPython drivers
* SSD1331 command reference and example drivers
* Raspberry Pi Pico MicroPython documentation



