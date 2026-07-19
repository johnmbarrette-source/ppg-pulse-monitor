# ppg-pulse-monitor
Personal Project: Fingertip pulse oximeter on ESP32 — validated against an ECG reference with Bland–Altman analysis. Custom SolidWorks enclosure, MATLAB signal processing.

# Fingertip PPG Pulse Monitor

A from-scratch fingertip pulse oximeter built on an ESP32, designed and validated
with a verification mindset. Measures heart rate and an uncalibrated SpO2 estimate
via a MAX30102 sensor, with a live OLED readout and a custom 3D-printed enclosure.

**Status:** In progress — build and validation underway (target: September 2026).

## What this project covers
- **Firmware (Arduino / C++):** MAX30102 signal acquisition, filtering, and live HR/SpO2 on an SSD1306 OLED
- **Validation (MATLAB):** heart-rate accuracy tested against an ECG chest-strap reference using Bland–Altman agreement analysis
- **Enclosure (SolidWorks):** custom fingertip housing, tuned across print iterations for fit

## Target specifications
Acceptance criteria were defined *before* data collection — see [`docs/SPEC.md`](docs/SPEC.md).

## Repository structure

## Limitations
The SpO2 estimate is uncalibrated and this is **not a medical device** — it is an
engineering and learning project.

## License
MIT — see [LICENSE](LICENSE).
