# Shenghua Su — Engineering Portfolio

Auckland, New Zealand · [shenghua.su@gmail.com](mailto:shenghua.su@gmail.com) · GitHub [@sushenghua](https://github.com/sushenghua)

I work on two tracks:

- **Embedded firmware** — ESP32, STM32, SAMD21, nRF52, Raspberry Pi; FreeRTOS / ESP-IDF, Zephyr / nRF Connect SDK, and bare-metal Cortex-M7 audio; multi-task RTOS architectures, real-time DSP, BLE, MQTT, OTA, sensor / display / power drivers; host-based unit + integration + HIL test discipline; Nordic PPK2 power profiling.
- **Web / mobile** — React, Next.js (App Router), TypeScript; Capacitor (iOS / Android) with BLE; React Native / Expo with native C++ audio modules over JSI; Mapbox, MongoDB, FastAPI / Node.js backends; Vitest with CI coverage gates, `fast-check` property tests, MSW for fetch-boundary mocking, Playwright E2E.

This repo is the index. Each linked project below has its own repo (some are private — happy to share source on request).

---

## Skills snapshot

### Embedded firmware

- **MCUs & SBCs:** ESP32 / ESP32-S3 (ESP-IDF), STM32H750 (Cortex-M7, Daisy Seed), nRF52832 (Cortex-M4), STM32F103 (Cortex-M3), SAMD21 (Cortex-M0+), ATmega328P, Raspberry Pi
- **RTOS & SDKs:** FreeRTOS, ESP-IDF, Zephyr / nRF Connect SDK (devicetree, Kconfig, west), bare-metal libDaisy / DaisySP; exposure to STM32 HAL and CMSIS-DSP
- **Protocols & connectivity:** SPI, I2C (multiplexed), UART, DMA, SDMMC, I²S / SAI, USB host (MIDI + HID), WiFi (STA + SoftAP, mDNS), BLE (NimBLE + Zephyr BT host), HTTP / WebSocket, MQTT (QoS 0/1/2, TLS)
- **Sensors & ICs:** PM2.5 / CO2 / temperature-humidity / light / IMU drivers, TFT and OLED display drivers (incl. LVGL on capacitive-touch LCD), audio codecs and Class-D I²S amps, battery-management ICs
- **Real-time audio / DSP:** 48 kHz audio ISR within a 1 ms budget, WSOLA time-stretch, per-stem FX chains, Schroeder–Moorer and Dattorro reverbs, polyBLEP synth, SD-streamed playback rings — host-tested portable C++ DSP library
- **Test & verification:** Unit, integration (with subsystem fakes), and hardware-in-loop tests via Zephyr `twister` and ESP-IDF Unity; oscilloscope, logic analyser, Nordic PPK2 power profiler
- **PCB & hardware:** Schematic reading and design, component selection, RC filter design for input conditioning
- **Languages:** C++, C, Python (incl. C extensions)

### Web / mobile

- **Frontend:** TypeScript, React, Next.js (App Router, SSR / SSG / ISR), HTML5, CSS3, Tailwind CSS
- **Mobile:** Capacitor (iOS / Android), React Native / Expo (custom dev client, EAS, Expo Modules API + JSI native C++ modules), BLE via `@capacitor-community/bluetooth-le` and `react-native-ble-plx`
- **UI engineering:** Design systems, component libraries, accessible UI (WCAG, ARIA, keyboard navigation), Lighthouse 90+ shipped
- **State & data:** React Query, Zustand, Mapbox GL JS, D3.js, React Flow
- **Testing & QA:** Vitest with `@vitest/coverage-v8` and CI coverage gates, property-based tests via `fast-check`, MSW, Playwright E2E
- **Backend & APIs:** Python (FastAPI), Node.js, RESTful APIs, GraphQL, JWT authentication
- **Data & cloud:** MySQL, MongoDB, Redis, SQLite; AWS (EC2, VPC, ALB, RDS, CloudFormation), Vercel, Docker
- **Languages:** TypeScript, JavaScript (ES6+), Python

---

## Projects — embedded firmware

### [`RoamoodDemo`](https://github.com/sushenghua/RoamoodDemo) — embedded slice

Roamood is a portable AI remix instrument: a phone app generates and arranges music stems with cloud AI, streams them to a handheld unit, and the unit performs them live. Two firmwares: a **Daisy Seed (STM32H750, Cortex-M7 @ 480 MHz)** bare-metal C++ audio master running 4 stems × (WSOLA time-stretch → tone → EQ → fader → echo → roll → gate → flanger → crush) + an 8-voice polyBLEP synth + a Dattorro / Schroeder master reverb, measured at ~0.4 ms of a 1 ms 48 kHz callback while streaming a backing track from SD; and an **ESP32-S3 (ESP-IDF 5.5 / FreeRTOS)** comms + UI node handling WiFi / BLE (NimBLE) coexistence, USB-host MIDI / HID, and an LVGL touch UI, linked to the Daisy over UART 1 Mbps (CRC16-framed) + SPI 8 MHz DMA. Contract-first protocol docs on every seam, shared header-only mirrors compiled into both firmwares, wireless OTA for both, and bench-measured bus throughput / SD signal-integrity / ISR-budget notes. Showcase repo — docs and media; the companion mobile app is described in the web / mobile section below.

### [`CoolingDockNRF`](https://github.com/sushenghua/CoolingDockNRF)

Zephyr / nRF Connect SDK firmware port of the ESP32-C3 CoolingDock product onto the Nordic nRF52832 DK. Wire-compatible with the original Capacitor frontend — same UUIDs, JSON shapes, MTU, so the existing app runs against this firmware unmodified. Includes a `twister`-driven test pyramid (unit + integration with kernel / NVS / `nrfx` fakes + Python HIL smoke) and a [PPK2 power-profile guide](https://github.com/sushenghua/CoolingDockNRF/blob/main/doc/ppk2_profile.md) that landed a 74 % baseline-current reduction (1.45 mA → 381 µA) after diagnosing the UART driver as the dominant idle-current source.

### [`CoolingDockDemo`](https://github.com/sushenghua/CoolingDockDemo)

End-to-end portfolio aggregator for the original CoolingDock product — ESP-IDF / FreeRTOS firmware (ESP32-C3), React 19 + TypeScript Capacitor companion app, 3D-printed enclosure CAD and assembled-unit photos, PPK2 power captures. The full loop from PCB and firmware through to the iOS / Android UI.

### [`InternetConnectionControlDemo`](https://github.com/sushenghua/InternetConnectionControlDemo) — embedded slice

ESP32-C6 firmware (the "Minion") that drives household routers via their vendor admin APIs and enforces per-child daily online-time budgets at the MAC-filter layer. Custom command-key protocol multiplexed over HTTP, WebSocket, MQTT, and BLE — same command surface across all four transports. The repo also contains the web and mobile companion apps (see the web / mobile section below).

### [`IoT_ESP32_Dehumidifier`](https://github.com/sushenghua/IoT_ESP32_Dehumidifier)

ESP32 dehumidifier with dual-zone hysteresis control — two SHT3x sensors driving two PWM fans. Native ESP-IDF HTTP + WebSocket server (no external libraries), CRC-8 sensor-bus validation, and OTA firmware updates delivered directly from the browser UI with MD5 verification and dual-partition A/B failsafe.

### Other embedded work — source available on request

- **IoT Environmental Sensor Platform** (commercially released as *Q Monitor* on iOS / Android, 2017–2019) — 10+ FreeRTOS tasks, fleet OTA over MQTT with dual-partition A/B failsafe and MD5-verified images, six sensor drivers across I2C / UART / ADC, five LCD / OLED display drivers, BQ24295 battery-management IC, EAP enterprise WiFi.
- **Auto-Watering System** (2025) — closed-loop irrigation across two repos: an ESP32 controller reading temperature / humidity sensors and driving a relay-driven water valve, paired with a Python service (deployed as a systemd daemon) that talks to the ESP32 over WebSocket, fetches weather forecasts, and runs the watering logic. SQLite for historical analysis and plotting.
- **Mining Rig Monitoring & Control** (fixed-scope contract, 2021–2022) — Raspberry Pi multi-service systemd architecture, TCA9548A I2C mux, pigpio PWM, Redis IPC, IFTTT alerting with rate limiting.
- **DodoBot** (2016–2017) — distributed multi-MCU robotics platform; custom SPI command protocol deployed across four MCUs spanning three architectures (Cortex-M3, Cortex-M0+, AVR); Python C extensions for low-latency BCM2835 register access; OpenOCD / JTAG debugging via Raspberry Pi.

---

## Projects — web / mobile

### [`RoamoodDemo`](https://github.com/sushenghua/RoamoodDemo) — mobile slice

React Native + TypeScript (Expo custom dev client, EAS) companion app for the Roamood hardware above. Cloud-AI stem generation (Stable Audio 2, provider-pluggable), a mixer / arranger with timeline, FX, and time-stretch, and Skia-rendered waveform tiles. The audio engine is Superpowered C++ in a local Expo native module driven over JSI so per-frame control values bypass the JS bridge and no PCM ever crosses it — implementing the same per-stem effect chain, macro curves, and beat clock as the device firmware so what you hear in the app is what the hardware plays. Device link over BLE (`react-native-ble-plx`) for control and HTTP over WiFi (STA or the device's own SoftAP, mDNS discovery) for bulk stems. Jest for pure DSP / format logic plus C++ host tests linking the real audio SDK on macOS. The repo includes an app walkthrough video and a live-set recording.

### [`InternetConnectionControlDemo`](https://github.com/sushenghua/InternetConnectionControlDemo) — web / mobile slice

A Next.js 16 self-hosted parental-control console + a Capacitor iOS / Android companion app, paired with the ESP32-C6 firmware above. Vendor-agnostic `RouterClient` abstraction over UniFi and Asus routers, session-based auth with encrypted secret storage, and a device-fingerprinting layer to maintain stable identity across MAC randomisation and DHCP rotation. The repo includes screenshots of both the web and mobile UIs.

### [`application_ui`](https://github.com/sushenghua/application_ui)

UI screenshots portfolio — curated screens from shipped products and recent frontend work.

### [`CryptoPortfolioDemo`](https://github.com/sushenghua/CryptoPortfolioDemo/blob/main/binance_crypto.ipynb)

Python (NumPy, Pandas, SciPy, Seaborn) analytical tooling for portfolio performance assessment, with clear interpretable visualisations of return, drawdown, and risk metrics.

### Other web / mobile work — source available on request

- **Trip Planner Map** (live demo: [trip-planner-map.vercel.app](https://trip-planner-map.vercel.app)) — Next.js, Mapbox GL JS, MongoDB, React Query, Zustand; 90+ Lighthouse accessibility *and* performance scores; AI-assisted trip suggestions with predictable fallback states; Playwright E2E across desktop, tablet, and mobile viewports.
- **Student Grade Analysis (SGA)** (live demo: [usgaweb.vercel.app/students](https://usgaweb.vercel.app/students)) — Auckland ICT Graduate School academic management system; Next.js + FastAPI + Pandas + MySQL, deployed on AWS EC2 with Docker.
- **CryptoBot** (remote contract, 2025) — market-analysis assistant; Next.js + Zustand frontend on a Python FastAPI + CrewAI backend; owned application architecture and end-to-end UX, iterating on output clarity and workflow predictability based on user feedback.
- **Cryptocurrency DEX** at Hoo Smart Chain (2022–2023) — high-clarity trading interface; React.js, Ether.js, Tailwind, Node.js; responsive data-dense UI for order placement, order books, and portfolio views; optimised render performance and bundle size for sub-second interactions under load.

---

## Contact

- **Email** [shenghua.su@gmail.com](mailto:shenghua.su@gmail.com)
- **GitHub** [@sushenghua](https://github.com/sushenghua)
- **Location** Auckland, New Zealand · full NZ work rights

## License

[MIT](LICENSE).
