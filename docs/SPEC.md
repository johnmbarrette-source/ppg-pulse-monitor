# Fingertip PPG Pulse Monitor — Target Specification & Verification Plan

| | |
|---|---|
| **Document** | Target Specification & Verification Plan |
| **Version** | 1.0 — **FROZEN** (acceptance criteria locked prior to data collection) |
| **Date frozen** | 2026-07-19 |
| **Author** | *John Barrette* |
| **Status** | Targets locked. Result / Pass–Fail columns to be completed after the validation study (Week 6). |

> **Why this document exists:** Acceptance criteria are defined here *before* any validation
> data is collected, then the device is tested against them and the measured results recorded in
> the tables below. Setting the spec first and testing against it — rather than tuning the device
> and reporting whatever it happens to do — is the core of a verification-and-validation (V&V)
> workflow. **These targets are frozen as of the date above and must not be changed after data
> collection begins.**

---

## 1. Purpose & Scope

A tethered fingertip photoplethysmography (PPG) monitor built to (a) acquire and display heart
rate (HR) and an uncalibrated blood-oxygen (SpO2) estimate, and (b) serve as the subject of a
measurement-agreement study against a reference HR device. Scope covers the sensor front end,
firmware signal path, the 3D-printed fingertip enclosure, and the MATLAB validation analysis.

## 2. Intended Use & Non-Use

**Intended use:** Personal engineering prototype and measurement-study platform. Educational and
portfolio purposes only.

**Explicitly NOT intended for:** Any clinical, diagnostic, or health-monitoring use. This is not
a medical device. It has not undergone regulatory testing, clinical validation, or SpO2
calibration against co-oximetry. Readings must not be used to make any health decision.

## 3. System Description

- **Sensor:** MAX30102 reflectance PPG (red ~660 nm + IR ~880 nm LEDs, single photodiode).
- **Controller:** ESP32 (Arduino framework), USB-powered.
- **Display:** SSD1306 OLED, shared I2C bus with the sensor.
- **Data path:** Timestamped samples streamed over serial → CSV on host for MATLAB analysis.
- **Enclosure (worn part):** 3D-printed fingertip housing that fixes sensor position and shrouds
  the fingertip from ambient light. Electronics remain off-body on a breadboard.
- **Reference (ground truth):** Chest-strap HR monitor (electrical R-wave detection, ~±1 bpm).

## 4. Functional Requirements

| ID | Requirement | Verification |
|----|-------------|--------------|
| FR-01 | Acquire raw red & IR PPG from the MAX30102 at ≥ 50 Hz | Bench / serial inspection |
| FR-02 | Compute and display live HR on the OLED, refreshed ≥ every 2 s | Bench / demo |
| FR-03 | Compute and display an **uncalibrated** SpO2 estimate (ratio-of-ratios) | Bench / demo |
| FR-04 | Stream timestamped samples over serial for CSV logging | File inspection |
| FR-05 | Enclosure shrouds the fingertip (ambient-light rejection) and holds the sensor at a repeatable position and contact pressure | Physical inspection / repeatability test |

## 5. Performance Requirements — Acceptance Criteria

> Targets are **frozen** at v1.0. Fill **Result** and **Pass/Fail** after the validation study
> (Week 6). Numbers below are the author's pre-registered targets for a low-motion fingertip
> device.

| ID | Metric | Target (acceptance criterion) | Verification method | Result | Pass/Fail |
|----|--------|-------------------------------|---------------------|--------|-----------|
| PR-01 | HR accuracy vs reference | Mean absolute error ≤ **5 bpm**; bias within **±3 bpm**; 95% limits of agreement within **±10 bpm**; over **50–120 bpm** | Bland–Altman + regression vs chest strap | _____ | _____ |
| PR-02 | HR acquisition time | Valid HR within **15 s** of finger placement in **≥ 90%** of trials | Timed bench trials (n ≥ 10) | _____ | _____ |
| PR-03 | Signal-quality detection | Device flags & excludes no-finger (IR below threshold) and gross motion-artifact segments | Bench test with induced artifacts | _____ | _____ |
| PR-04 | Repeatability at rest | HR SD ≤ **3 bpm** across **5** repeated placements | Repeated-placement test | _____ | _____ |
| PR-05 | SpO2 plausibility (uncalibrated) | Reports a physiologically plausible value (≈ 95–100%) on a healthy resting subject; **accuracy not claimed** | Bench observation | _____ | _____ |

## 6. Design Constraints

| ID | Constraint |
|----|------------|
| DC-01 | USB-powered; battery/power management out of scope for this revision |
| DC-02 | Electronics off-body on breadboard; only the sensor housing is worn |
| DC-03 | FDM-printed enclosure — PLA for the fixed cradle (v1), PETG for the sprung clip (v2, if built) |
| DC-04 | MAX30102 and SSD1306 share one I2C bus (distinct addresses) |

## 7. Limitations & Out of Scope

- **Single subject (n = 1).** This is a device-agreement study, not a population/clinical study.
- **SpO2 uncalibrated.** No co-oximetry, no controlled desaturation; method shown, accuracy not claimed.
- **Reference is not absolute truth.** The chest strap carries its own (~±1 bpm) error and is
  treated as the practical ground truth, not a gold standard.
- **Not a medical device.** No regulatory, biocompatibility, or clinical validation performed.
- **Motion.** Targets are set for low-motion conditions; performance under vigorous motion is
  characterized, not guaranteed.

## 8. Verification / Test Protocol (summary)

1. **Bench verification** of FR-01–FR-04 (signal present, display live, CSV logging works).
2. **Agreement study (PR-01):** Wear device and chest strap simultaneously. Capture paired HR
   across the full range by alternating rest and stair-climbing, recording the recovery decay,
   to sweep ~50–120 bpm. Target ≥ 30 paired points spanning the range. Analyze in MATLAB:
   Bland–Altman (bias, ±1.96 SD limits of agreement, 95% CI on the limits), linear regression,
   and a check for proportional bias (difference vs. magnitude).
3. **Timed trials (PR-02)** and **repeated-placement test (PR-04).**
4. **Induced-artifact test (PR-03):** remove finger / introduce motion, confirm flagging.
5. Record all measured values in §5 and write the honest pass/fail summary in the repo README.

## 9. Revision History

| Version | Date | Change |
|---------|------|--------|
| 1.0 | 2026-07-19 | Targets set and **frozen** prior to data collection |
