# Shenghua (Woody) Su — Embedded Firmware Portfolio

Auckland, New Zealand · [shenghua.su@gmail.com](mailto:shenghua.su@gmail.com) · GitHub [@sushenghua](https://github.com/sushenghua)

Embedded firmware in C and C++ on ESP32, STM32 and nRF52, with FreeRTOS, ESP-IDF and Zephyr. Each project links to its repo, docs or videos, and some repos hold docs, photos and measurements rather than code. My web and mobile work is on the [web / mobile page](WEB.md), and the [index](README.md) lists both.

## Skills

| Area | What |
|---|---|
| MCUs and boards | ESP32 (C3, C6, S3), STM32H750 (Daisy Seed), nRF52832, nRF52840, Raspberry Pi |
| RTOS and SDKs | FreeRTOS, ESP-IDF, Zephyr / nRF Connect SDK (devicetree, Kconfig, west, MCUboot), bare-metal Cortex-M7 audio |
| Buses and radios | UART, SPI, I2C, RS485 Modbus, USB host (MIDI and HID), BLE (NimBLE and Zephyr host, Coded PHY, mcumgr SMP), WiFi, MQTT |
| Testing | Host-based unit and integration tests with subsystem fakes, hardware-in-loop smoke tests |
| Firmware updates | Signed OTA (ESP-IDF secure-signed apps, MCUboot), A/B slots with rollback, canary then stable rollout |
| Power and bench work | Nordic PPK2 current profiling, oscilloscope checks, bus and SD throughput measurements, deep-sleep and switched-rail design |
| Languages | C, C++, Python |

---

## Projects

### [`GardenAI`](https://github.com/sushenghua/GardenAI) — nodes, gateway, and server

<p align="center">
  <a href="https://github.com/sushenghua/GardenAI"><img src="https://raw.githubusercontent.com/sushenghua/GardenAI/main/docs/deployment/balcony.jpg" alt="Solar nRF52840 sensor node on a balcony railing next to potted blueberries" height="320"></a>
  <a href="https://github.com/sushenghua/GardenAI"><img src="https://raw.githubusercontent.com/sushenghua/GardenAI/main/docs/hardware/nrf-node.jpg" alt="nRF52840 node with solar lid, Li-ion cell, MAX3485, SHT31, and RS485 soil probe" height="320"></a>
  <a href="https://github.com/sushenghua/GardenAI"><img src="https://raw.githubusercontent.com/sushenghua/GardenAI/main/docs/deployment/plantcam.jpg" alt="Solar camera node at the edge of a blackberry pot" height="320"></a>
</p>
<p align="center">
  <a href="https://github.com/sushenghua/GardenAI"><img src="https://raw.githubusercontent.com/sushenghua/GardenAI/main/docs/hardware/lppc-pcb.jpg" alt="Watering node PCB with an ESP32-S3 DevKitC-1 and headers for two pump drivers" height="300"></a>
  <a href="https://github.com/sushenghua/GardenAI"><img src="https://raw.githubusercontent.com/sushenghua/GardenAI/main/docs/hardware/nrf-pcb.jpg" alt="nRF52840 sensor node carrier PCB" height="300"></a>
  <a href="https://github.com/sushenghua/GardenAI"><img src="https://raw.githubusercontent.com/sushenghua/GardenAI/main/docs/hardware/lppc-enclosure.jpg" alt="Watering node board in an outdoor enclosure with battery, pump and soil probe" height="300"></a>
  <a href="https://github.com/sushenghua/GardenAI"><img src="https://raw.githubusercontent.com/sushenghua/GardenAI/main/docs/deployment/garden-bed.jpg" alt="Watering node PCB in its outdoor box on a raised garden bed, with the water bucket and drip line" height="300"></a>
</p>

Battery and solar garden nodes report over MQTT to a Python server that decides when to water; the device does not decide on its own.

- Nodes: ESP32-C6 watering node (12 V pump, BTS7960, RS485 soil probe, pump guard and cycle watchdog), ESP32-S3 vertical farm controller, and nRF52840 solar sensors (Zephyr, MCUboot) behind an ESP32-S3 BLE gateway.
- Server: strategies over any node's readings, rain forecast, and an evaporation water balance; nodes keep an offline copy.
- Signed OTA for every node type, BLE included: canary first, stable after approval, rollback on the node.
- Two fabricated PCBs (ESP32-S3 watering board, nRF52840 carrier), generated from Python scripts with a small grid router.
- Tests: 324 server, 187 client, 74 firmware host tests.

### [`RoamoodDemo`](https://github.com/sushenghua/RoamoodDemo) — hardware and firmware

<p align="center">
  <a href="https://github.com/sushenghua/RoamoodDemo"><img src="https://raw.githubusercontent.com/sushenghua/RoamoodDemo/main/media/hardware/hero.jpg" alt="Roamood handheld unit with the play screen showing four channel strips" height="320"></a>
  <a href="https://github.com/sushenghua/RoamoodDemo"><img src="https://raw.githubusercontent.com/sushenghua/RoamoodDemo/main/media/hardware/bench.jpg" alt="Roamood bench build with the ESP32-S3, Daisy Seed, LCD, and pad controller" height="320"></a>
</p>

Roamood is a handheld music instrument. A phone app generates stems with cloud AI and sends them to the device. The device mixes and performs them live with no phone attached. The repo holds the design docs, photos, and two videos.

- Audio MCU: Daisy Seed (STM32H750, Cortex-M7 at 480 MHz), bare-metal C++. Four stems, each with time-stretch, tone, EQ, fader, echo, roll, gate, flanger, and crush. An 8-voice synth and a master reverb.
- Measured: about 0.4 ms worst case inside a 1 ms, 48 kHz audio callback while loading stems from SD during playback.
- Front-end MCU: ESP32-S3 on ESP-IDF 5.5 and FreeRTOS. WiFi and BLE (NimBLE) together, USB host for MIDI and HID, LVGL touch UI. Linked to the Daisy over 1 Mbps UART with CRC16 frames and 8 MHz SPI with DMA.
- Both firmwares update over the air. The protocols between the two MCUs and the app are documented, and the same header files are compiled into both firmwares.
- The docs include bench notes on bus throughput, SD signal integrity, and ISR budget, plus a host-tested C++ DSP library.

Watch it run: [live set](https://github.com/sushenghua/RoamoodDemo#readme) (pads, take switching, effects, synth over a streamed backing track) and [app walkthrough](https://github.com/sushenghua/RoamoodDemo#readme), both embedded at the top of the repo README.

<!-- TODO video (Roamood, 30 to 60 s, landscape, clear audio):
     - Start with the device already playing four stems.
     - Turn one knob (or move the XY pad) so one stem's filter or echo audibly changes while the others keep playing.
     - Switch a take on one pad, then hold a roll or gate for a bar.
     - Keep the LCD in frame so the channel strips are visible.
     - Export H.264 mp4 under 10 MB, upload by dragging it into a GitHub issue or README edit box, and paste the user-attachments URL here. -->

### [`CoolingDockNRF`](https://github.com/sushenghua/CoolingDockNRF)

<p align="center">
  <a href="https://github.com/sushenghua/CoolingDockNRF"><img src="https://raw.githubusercontent.com/sushenghua/CoolingDockNRF/main/doc/assets/nrf_board_ppk2_oscope.jpg" alt="nRF52 DK with SHT3x sensor, PPK2, and oscilloscope showing the fan PWM" width="640"></a>
</p>

Zephyr / nRF Connect SDK firmware for the Nordic nRF52832 DK. It re-implements the local-control part of the ESP32-C3 [CoolingDock](https://github.com/sushenghua/CoolingDockDemo) product: SHT3x sampling over I2C, fan PWM with hysteresis and ramping, and a BLE GATT peripheral with secure pairing and bonded-only advertising. Same UUIDs, JSON shapes, and MTU as the original, so the existing Capacitor app connects to it. On iOS the app needs write-with-response turned on.

- Tests: unit and integration tests built and run on the host with fakes for the kernel, settings / NVS, and `nrfx`; an optional `twister` target for the integration test; a Python (`bleak`) hardware-in-loop smoke test. See [`tests/README.md`](https://github.com/sushenghua/CoolingDockNRF/blob/main/tests/README.md).
- Power: the [PPK2 profile guide](https://github.com/sushenghua/CoolingDockNRF/blob/main/doc/ppk2_profile.md) walks from 1.45 mA idle to 290 to 570 µA depending on BLE state (381 µA in the usual bonded state, about 74 % lower), after finding the UART driver was the main idle load.

Watch it run: the [repo README](https://github.com/sushenghua/CoolingDockNRF#readme) embeds a short clip where a finger warms the sensor and the scope shows the PWM duty widen.

<!-- TODO video (CoolingDock, 30 to 45 s):
     - This port is BLE only, so use the Capacitor app on a phone: change the mode from Fixed to Sensor, drag the speed slider, and show the fan speed change on the real dock.
     - For the "web UI driving the fans" clip, use the original ESP32-C3 unit instead: open the browser UI over WebSocket, move the PWM slider, and keep both the screen and the fan in frame.
     - Paste the user-attachments URL here. -->

### [`CoolingDockDemo`](https://github.com/sushenghua/CoolingDockDemo)

<p align="center">
  <a href="https://github.com/sushenghua/CoolingDockDemo"><img src="https://raw.githubusercontent.com/sushenghua/CoolingDockDemo/main/doc/3d_print/CoolingDockSide.jpg" alt="3D-printed CoolingDock under a Mac mini" width="380"></a>
  <a href="https://github.com/sushenghua/CoolingDockDemo"><img src="https://raw.githubusercontent.com/sushenghua/CoolingDockDemo/main/doc/app/CoolingDockApp.PNG" alt="CoolingDock iOS app showing temperature, humidity, and fan speed" width="180"></a>
</p>

The original product the nRF port above came from: a 3D-printed cooling dock for a Mac mini with ESP32-C3 firmware (ESP-IDF, FreeRTOS, NimBLE) and a React 19 + TypeScript Capacitor app for iOS and Android. The repo holds photos of the printed enclosure, CAD renders, the app screenshot, and PPK2 current captures for each BLE state. The README describes the firmware and app structure.

### [`InternetConnectionControlDemo`](https://github.com/sushenghua/InternetConnectionControlDemo) — firmware

<p align="center">
  <a href="https://github.com/sushenghua/InternetConnectionControlDemo"><img src="https://raw.githubusercontent.com/sushenghua/InternetConnectionControlDemo/main/docs/device/minion1.jpg" alt="ESP32-C6 dev board running the Minion firmware" width="360"></a>
</p>

An ESP32-C6 device (the "Minion") that logs into home routers through their admin APIs and enforces a daily online-time budget per child at the MAC-filter level. One command interpreter serves HTTP, WebSocket, and BLE, so the web console and the phone app send the same commands. The repo holds docs and screenshots. The web console and mobile app are on the [web / mobile page](WEB.md#internetconnectioncontroldemo--web-console-and-mobile-app).

### [`MazeRoute`](https://github.com/sushenghua/MazeRoute)

<p align="center">
  <a href="https://github.com/sushenghua/MazeRoute"><img src="https://raw.githubusercontent.com/sushenghua/MazeRoute/main/media/poster.jpg" alt="MazeRoute live view computed on an ESP32-S3: the agent has reached B after re-planning around a dropped obstacle, with the state machine, per-frame chip cost, on-chip benchmark and test suite" width="640"></a>
</p>

A car-like agent drives from A to B through irregular, concave obstacles while new ones are dropped in its way. It plans for its own footprint on inflated grids (coarse, fine, then a 1 px escape), re-plans around every drop, and always ends in `ARRIVED` or an honest `FAILED`: every state has a deadline, a progress watchdog catches stalls, and recovery escalates in bounded steps. A* and grid builds are time-sliced so each frame stays under 16.7 ms.

- Three implementations of one simulation core: vanilla JS + canvas in the browser, a C++23 port (10 to 20 times faster, matched run for run), and a single-precision core on an ESP32-S3 with a web page that draws the chip's output over USB.
- Verified: 192,300 recorded missions across all four environments with 0 failures; on the chip, 0 of 10,065 real-time frames over 16.7 ms (worst 14.8 ms).

Watch it run: the [repo README](https://github.com/sushenghua/MazeRoute#readme) embeds a demo video of missions computed on the ESP32-S3.

### [`IoT_ESP32_Dehumidifier`](https://github.com/sushenghua/IoT_ESP32_Dehumidifier)

<p align="center">
  <a href="https://github.com/sushenghua/IoT_ESP32_Dehumidifier"><img src="https://raw.githubusercontent.com/sushenghua/IoT_ESP32_Dehumidifier/master/doc/img/show1.jpg" alt="Two fans mounted under a cabinet with the controller box on the wall" height="400"></a>
  <a href="https://github.com/sushenghua/IoT_ESP32_Dehumidifier"><img src="https://raw.githubusercontent.com/sushenghua/IoT_ESP32_Dehumidifier/master/doc/img/webui.jpg" alt="Dehumidifier web UI with two zones and threshold settings" height="400"></a>
</p>

Earlier ESP-IDF C++ project (2023). Two SHT3x sensors, each switching its own fan through a MOSFET board with hysteresis thresholds. The device serves its own web page from ESP-IDF's `esp_http_server` and talks to it over WebSocket. Sensor reads are checked with CRC-8. A button in the UI triggers an OTA update that the device fetches from a small updater service, verifies by MD5, and writes to the inactive OTA partition. An MQTT client is included. No tests. The UI text is in Chinese.


### Other embedded work

- **Q Monitor** (2017 to 2019) — ESP32 environmental sensor product released on iOS and Android. FreeRTOS firmware with fleet OTA over MQTT, several I2C / UART / ADC sensor drivers, LCD and OLED drivers, a battery-management IC, and enterprise WiFi.
- **Mining rig monitoring and control** (2021 to 2022) — Raspberry Pi with several systemd services, a TCA9548A I2C mux, PWM fan control, Redis between services, and rate-limited alerts.
- **Auto-watering system** (2025) — ESP32 controller with a relay-driven valve and a Python service on a small server that reads the forecast and decides when to water. The predecessor of [`GardenAI`](https://github.com/sushenghua/GardenAI).
- **DodoBot** (2016 to 2017) — multi-MCU robot with a custom SPI command protocol across Cortex-M3, Cortex-M0+, and AVR parts, driven from a Raspberry Pi.

---

## Contact

- **Email** [shenghua.su@gmail.com](mailto:shenghua.su@gmail.com)
- **GitHub** [@sushenghua](https://github.com/sushenghua)
- **Location** Auckland, New Zealand · open work visa to Aug 2028, no sponsorship needed
