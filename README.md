# Woody Shenghua Su — Engineering Portfolio

Auckland, New Zealand · [shenghua.su@gmail.com](mailto:shenghua.su@gmail.com) · GitHub [@sushenghua](https://github.com/sushenghua)

I work on two tracks: embedded firmware (ESP32, STM32, nRF52) and web / mobile apps in TypeScript (React, Next.js, Capacitor, React Native). This page is the index. Each project below links to its own repo or live site. Some repos hold docs, photos, and measurements rather than code.

## Start here

**Embedded firmware**

- [`RoamoodDemo`](https://github.com/sushenghua/RoamoodDemo) — handheld audio device with real-time DSP on an STM32H750 and an ESP32-S3 front end. Docs, bench measurements, and two videos.
- [`CoolingDockNRF`](https://github.com/sushenghua/CoolingDockNRF) — Zephyr / nRF52832 port of a product firmware, with host-run unit and integration tests, a Python HIL smoke test, and a PPK2 power write-up.
- [`InternetConnectionControlDemo`](https://github.com/sushenghua/InternetConnectionControlDemo) — ESP32-C6 device that controls home routers, plus a web console and a mobile app. Docs and screenshots.

**Web / mobile (TypeScript)**

- [trip-planner-map.vercel.app](https://trip-planner-map.vercel.app) — live Next.js and Mapbox trip planner. Works on phone-sized screens.
- [usgaweb.vercel.app/students](https://usgaweb.vercel.app/students) — live student grade analysis site with sample data. Sign in with user `admin`, password `admin`.
- [`application_ui`](https://github.com/sushenghua/application_ui) — screenshots from shipped and recent apps, including some older native iOS and Android work.

The pinned repos on [github.com/sushenghua](https://github.com/sushenghua) should match this list: `RoamoodDemo`, `CoolingDockNRF`, `InternetConnectionControlDemo`, `application_ui`, plus this index.

---

## Skills snapshot

Every item here appears in a linked repo or live site. The last column says where.

| Area | What | Where to see it |
|---|---|---|
| MCUs and boards | ESP32 (C3, C6, S3), STM32H750 (Daisy Seed), nRF52832, Raspberry Pi | ESP32: `IoT_ESP32_Dehumidifier`, `InternetConnectionControlDemo`; STM32: `RoamoodDemo`; nRF52: `CoolingDockNRF`; Raspberry Pi: mining rig project below |
| RTOS and SDKs | FreeRTOS, ESP-IDF, Zephyr / nRF Connect SDK (devicetree, Kconfig, west), bare-metal Cortex-M7 audio | `IoT_ESP32_Dehumidifier`, `CoolingDockNRF`, `RoamoodDemo` |
| Buses and radios | UART, SPI, I2C, USB host (MIDI and HID), BLE (NimBLE and Zephyr host), WiFi, MQTT | UART / SPI / USB: `RoamoodDemo`; I2C and BLE: `CoolingDockNRF`; WiFi and MQTT: `IoT_ESP32_Dehumidifier` |
| Testing | Host-based unit and integration tests with subsystem fakes, hardware-in-loop smoke tests | `CoolingDockNRF` (`tests/`) |
| Power and bench work | Nordic PPK2 current profiling, oscilloscope checks, bus and SD throughput measurements | `CoolingDockNRF` (`doc/ppk2_profile.md`), `RoamoodDemo` (`docs/04-engineering-notes.md`) |
| Web | React, Next.js, TypeScript, Node.js | trip planner (live), grade analysis site (live), `InternetConnectionControlDemo` console (screenshots) |
| Mobile | Capacitor (iOS / Android), React Native / Expo | Capacitor: `InternetConnectionControlDemo`, `CoolingDockDemo`; React Native: `RoamoodDemo` app |
| Languages | C, C++, TypeScript, Python | across the repos above |

---

## Projects — embedded firmware

### [`RoamoodDemo`](https://github.com/sushenghua/RoamoodDemo) — hardware and firmware

<p align="center">
  <a href="https://github.com/sushenghua/RoamoodDemo"><img src="https://raw.githubusercontent.com/sushenghua/RoamoodDemo/main/media/hardware/hero.jpg" alt="Roamood handheld unit with the play screen showing four channel strips" height="320"></a>
  <a href="https://github.com/sushenghua/RoamoodDemo"><img src="https://raw.githubusercontent.com/sushenghua/RoamoodDemo/main/media/hardware/bench.jpg" alt="Roamood bench build with the ESP32-S3, Daisy Seed, LCD, and pad controller" height="320"></a>
</p>

Roamood is a handheld music instrument. A phone app generates stems with cloud AI and sends them to the device. The device mixes and performs them live with no phone attached. The repo holds the design docs, photos, and two videos.

- Audio MCU: Daisy Seed (STM32H750, Cortex-M7 at 480 MHz), bare-metal C++. Four stems, each with time-stretch (WSOLA), tone, EQ, fader, echo, roll, gate, flanger, and crush. An 8-voice polyBLEP synth and a Dattorro or Schroeder master reverb.
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

An ESP32-C6 device (the "Minion") that logs into home routers through their admin APIs and enforces a daily online-time budget per child at the MAC-filter level. One command interpreter serves HTTP, WebSocket, and BLE, so the web console and the phone app send the same commands. The repo holds docs and screenshots. The web and mobile apps are described in the web / mobile section below.

### [`IoT_ESP32_Dehumidifier`](https://github.com/sushenghua/IoT_ESP32_Dehumidifier)

<p align="center">
  <a href="https://github.com/sushenghua/IoT_ESP32_Dehumidifier"><img src="https://raw.githubusercontent.com/sushenghua/IoT_ESP32_Dehumidifier/master/doc/img/show2.jpg" alt="Dehumidifier controller box with an iPad showing its web UI" width="300"></a>
  <a href="https://github.com/sushenghua/IoT_ESP32_Dehumidifier"><img src="https://raw.githubusercontent.com/sushenghua/IoT_ESP32_Dehumidifier/master/doc/img/webui.jpg" alt="Dehumidifier web UI with two zones and threshold settings" width="300"></a>
</p>

Earlier ESP-IDF C++ project (2023). Two SHT3x sensors, each switching its own fan through a MOSFET board with hysteresis thresholds. The device serves its own web page from ESP-IDF's `esp_http_server` and talks to it over WebSocket. Sensor reads are checked with CRC-8. A button in the UI triggers an OTA update that the device fetches from a small updater service, verifies by MD5, and writes to the inactive OTA partition. An MQTT client is included. No tests. The UI text is in Chinese.


### Other embedded work

- **Q Monitor** (2017 to 2019) — ESP32 environmental sensor product released on iOS and Android. FreeRTOS firmware with fleet OTA over MQTT, several I2C / UART / ADC sensor drivers, LCD and OLED drivers, a battery-management IC, and enterprise WiFi.
- **Mining rig monitoring and control** (2021 to 2022) — Raspberry Pi with several systemd services, a TCA9548A I2C mux, PWM fan control, Redis between services, and rate-limited alerts.
- **Auto-watering system** (2025) — ESP32 controller with a relay-driven valve and a Python service on a small server that reads the forecast and decides when to water.
- **DodoBot** (2016 to 2017) — multi-MCU robot with a custom SPI command protocol across Cortex-M3, Cortex-M0+, and AVR parts, driven from a Raspberry Pi.

---

## Projects — web / mobile

### Trip Planner Map — [trip-planner-map.vercel.app](https://trip-planner-map.vercel.app)

<p align="center">
  <a href="https://trip-planner-map.vercel.app"><img src="https://raw.githubusercontent.com/sushenghua/application_ui/main/images/tpm_ui2.jpg" alt="Trip planner map on a phone" width="200"></a>
</p>

Next.js, Mapbox GL JS, MongoDB, React Query, Zustand. Add stops, see the route, and on a phone the trip list becomes a bottom sheet. AI trip suggestions are available after sign-in.

<!-- TODO image: desktop screenshot of the trip planner with the trip pane expanded and a full route visible, about 1200 px wide, saved as images/trip-planner.png in this repo. -->

### Student Grade Analysis (SGA) — [usgaweb.vercel.app/students](https://usgaweb.vercel.app/students)

<p align="center">
  <a href="https://usgaweb.vercel.app/students"><img src="https://raw.githubusercontent.com/sushenghua/application_ui/main/images/sga_ui2.png" alt="Student grade analysis table with filters and GPA badges" width="640"></a>
</p>

Grade and programme management for a graduate ICT school. Sign in with user `admin`, password `admin` to browse about 250 sample students, filtered by programme, term, course, or tag. Next.js front end, FastAPI + Pandas + MySQL back end, deployed with Docker on AWS EC2.

<!-- TODO image: desktop screenshot of the students table signed in as the demo account (sample data, no real names), about 1200 px wide, saved as images/sga-students.png in this repo. -->

### [`RoamoodDemo`](https://github.com/sushenghua/RoamoodDemo) — mobile app

<p align="center">
  <a href="https://github.com/sushenghua/RoamoodDemo"><img src="https://raw.githubusercontent.com/sushenghua/RoamoodDemo/main/media/app/mixer.png" alt="Roamood app mixer with four lanes and waveform tiles" width="200"></a>
  <a href="https://github.com/sushenghua/RoamoodDemo"><img src="https://raw.githubusercontent.com/sushenghua/RoamoodDemo/main/media/app/fx-drawer.png" alt="Roamood app with a lane's effects drawer open" width="200"></a>
  <a href="https://github.com/sushenghua/RoamoodDemo"><img src="https://raw.githubusercontent.com/sushenghua/RoamoodDemo/main/media/app/bind1.png" alt="Roamood app pad controller bindings with a 4 by 4 pad grid" width="200"></a>
</p>

React Native + TypeScript app for the Roamood device, built with an Expo custom dev client and EAS. It generates stems with cloud AI, currently Stable Audio 2 behind a provider interface, arranges them on a timeline with effects and time-stretch, and pushes them to the device. The audio engine is Superpowered C++ in a local Expo module driven over JSI, so control values skip the JS bridge and no audio samples cross it. It runs the same effect chain and beat clock as the firmware. BLE for control, HTTP over WiFi or the device's own access point for stem transfer. Jest for DSP and format logic, C++ host tests on macOS. The repo README embeds a walkthrough video.

### [`InternetConnectionControlDemo`](https://github.com/sushenghua/InternetConnectionControlDemo) — web console and mobile app

<p align="center">
  <a href="https://github.com/sushenghua/InternetConnectionControlDemo"><img src="https://raw.githubusercontent.com/sushenghua/InternetConnectionControlDemo/main/docs/screen-shot/webapp-screen1.png" alt="Parent control web console with weekday and weekend rules" width="260"></a>
  <a href="https://github.com/sushenghua/InternetConnectionControlDemo"><img src="https://raw.githubusercontent.com/sushenghua/InternetConnectionControlDemo/main/docs/screen-shot/mobileapp-screen2.png" alt="Mobile app listing router clients with block and unblock buttons" width="150"></a>
</p>

A self-hosted Next.js parental-control console and a Capacitor companion app (iOS so far), paired with the ESP32-C6 firmware above. The console has a router client interface with UniFi and Asus implementations, cookie-session auth with router credentials stored encrypted, and a device matcher that scores hostname, OUI, and vendor so a device keeps its identity when its MAC address changes. The repo holds screenshots of both apps.

### [`application_ui`](https://github.com/sushenghua/application_ui)

<p align="center">
  <a href="https://github.com/sushenghua/application_ui#dex-frontend-complex-web-ui"><img src="https://raw.githubusercontent.com/sushenghua/application_ui/main/images/web_ui_complex.jpg" alt="DEX trading front end" width="420"></a>
  <a href="https://github.com/sushenghua/application_ui#roamood"><img src="https://raw.githubusercontent.com/sushenghua/application_ui/main/images/Roamood_ui1.png" alt="Roamood mobile app track editor" width="130"></a>
</p>

Screenshots from apps I have built: Roamood, the parental-control console, the CoolingDock app, the trip planner, the grade analysis site, a DEX trading front end, and some older native work (an iOS game in Cocos2d, shown as a video, and an Android battery app).

### Other web / mobile work

- [`CryptoPortfolioDemo`](https://github.com/sushenghua/CryptoPortfolioDemo/blob/main/binance_crypto.ipynb) — Python notebook (NumPy, Pandas, SciPy, Seaborn) that pulls Binance price history and computes returns, volatility, Sharpe ratio, VaR, and efficient-frontier portfolios, with charts rendered in the notebook. Needs a Binance API key to re-run.
- **CryptoBot** (remote contract, 2025) — market-analysis assistant. Next.js + Zustand front end on a FastAPI + CrewAI back end.
- **Cryptocurrency DEX** at Hoo Smart Chain (2022 to 2023) — trading interface in React, Ethers.js, Tailwind, and Node.js, with order placement, order books, and portfolio views. One screen is in [`application_ui`](https://github.com/sushenghua/application_ui#dex-frontend-complex-web-ui).

---

## Contact

- **Email** [shenghua.su@gmail.com](mailto:shenghua.su@gmail.com)
- **GitHub** [@sushenghua](https://github.com/sushenghua)
- **Location** Auckland, New Zealand · full NZ work rights
