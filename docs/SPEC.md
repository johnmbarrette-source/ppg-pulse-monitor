# Fingertip PPG Pulse Monitor — Design Specification & Test Plan

**Author:** John Barrette
**Version:** 1.0
**Date:** July 19, 2026
**Status:** Performance targets set before data collection. Measured results are added to the tables after testing.

This document lists the design requirements and target specifications for my fingertip pulse
monitor, and how each one is tested. I set the performance targets before collecting any data, so
the device is judged against fixed criteria instead of numbers chosen to match the results. The
result columns are left blank until testing is done.

## 1. Purpose and Scope

A tethered fingertip pulse monitor that measures heart rate (HR) and an uncalibrated blood-oxygen
(SpO2) estimate, and that is used as the subject of a measurement-agreement study against a
reference heart-rate device. The scope covers the sensor, the firmware signal path, the 3D-printed
fingertip enclosure, and the analysis in MATLAB.

## 2. Intended Use

This is a personal engineering prototype for learning and portfolio purposes. It is not a medical
device. It has not been clinically validated, and the SpO2 estimate has not been calibrated against
a reference oximeter, so no reading should be used to make a health decision.

## 3. System Description

- Sensor: MAX30102 reflectance PPG (red ~660 nm and IR ~880 nm LEDs, single photodiode)
- Controller: ESP32 (Arduino framework), powered over USB
- Display: SSD1306 OLED, sharing the I2C bus with the sensor
- Data path: timestamped samples sent over serial and saved as CSV for analysis
- Enclosure: 3D-printed fingertip housing that holds the sensor in a fixed position and shrouds the
  fingertip from ambient light. The electronics stay off-body on a breadboard.
- Reference: chest-strap heart-rate monitor (electrical R-wave detection, accurate to about ±1 bpm)

## 4. Functional Requirements

| ID | Requirement | How it is checked |
|----|-------------|-------------------|
| FR-01 | Read raw red and IR PPG values from the MAX30102 at 50 Hz or higher | Serial output |
| FR-02 | Show live heart rate on the OLED, updated at least every 2 seconds | Bench test |
| FR-03 | Compute and show an uncalibrated SpO2 estimate using the ratio-of-ratios method | Bench test |
| FR-04 | Send timestamped samples over serial for CSV logging | Check the saved file |
| FR-05 | Enclosure blocks ambient light and holds the sensor at a repeatable position and pressure | Physical check and repeatability test |

## 5. Performance Targets

| ID | What is measured | Target | How it is tested | Result | Pass/Fail |
|----|------------------|--------|------------------|--------|-----------|
| PR-01 | HR accuracy vs. the reference | Mean absolute error ≤ 5 bpm, bias within ±3 bpm, and 95% limits of agreement within ±10 bpm, over 50–120 bpm | Bland–Altman and regression against the chest strap | | |
| PR-02 | Time to a valid reading | A valid HR within 15 seconds of placing a finger, in at least 90% of tries | Timed trials (10 or more) | | |
| PR-03 | Signal-quality checking | Flags and ignores no-finger and heavy-motion segments | Bench test with deliberate motion and finger removal | | |
| PR-04 | Repeatability at rest | HR spread (standard deviation) ≤ 3 bpm across 5 repeated placements | Repeated-placement test | | |
| PR-05 | SpO2 plausibility | Gives a reasonable value (about 95–100%) on a healthy person at rest; accuracy is not claimed | Bench observation | | |

## 6. Design Constraints

- Powered over USB; battery and power management are out of scope for this version.
- Only the sensor housing is worn; the rest of the electronics stay on a breadboard.
- Enclosure is FDM printed — PLA for the fixed cradle, PETG for the spring clip if I build one.
- The MAX30102 and the OLED share one I2C bus, at different addresses.

## 7. Limitations

- Single subject (n = 1). This tests how well my device agrees with a reference, not how it
  performs across a population.
- The SpO2 estimate is uncalibrated. I show the method but do not claim it is accurate.
- The reference is not perfect truth. The chest strap has its own small error (about ±1 bpm) and is
  treated as a practical reference, not a gold standard.
- Not a medical device. No regulatory or clinical testing was done.
- Targets assume low motion. Behavior during heavy motion is described, not guaranteed.

## 8. Test Plan

1. Check the functional requirements (FR-01 to FR-04): confirm the signal is present, the display
   updates live, and the CSV logging works.
2. Agreement study (PR-01): wear the device and the reference at the same time and record paired
   heart-rate readings. Because the device is tethered to the laptop over USB and the sensor is
   sensitive to motion, all readings are taken while seated and still. The heart-rate range is
   covered two ways:
   - Resting captures: sit calmly and record, to anchor the low end (about 50–70 bpm).
   - Post-exercise recovery captures: run for a couple of minutes to raise the heart rate well
     above 120 bpm, then immediately sit down, place the finger on the sensor, hold still, and
     record for several minutes as the heart rate decays back toward rest. A single recovery
     capture sweeps most of the range under low-motion conditions.
   Repeat until there are at least 30 paired points spanning the full range.

   Clock syncing: the device and the reference log on separate clocks, so the two time series must
   be aligned before pairing. Confirm the laptop and reference-device clocks are correct beforehand,
   and mark a shared start instant for each capture (for example, note the exact start time and
   create an identifiable landmark in the signal). The two series are then aligned by this marker in
   MATLAB before any readings are paired.

   Analyze in MATLAB with a Bland–Altman plot (bias, ±1.96 SD limits of agreement, and confidence
   intervals on the limits), a regression, and a check for whether the error grows at higher heart
   rates.
3. Timed trials (PR-02) and the repeated-placement test (PR-04).
4. Signal-quality test (PR-03): remove the finger and add motion, and confirm those segments are
   flagged.
5. Record the measured values in Section 5 and summarize the pass/fail outcome in the README.

## 9. Revision History

| Version | Date | Change |
|---------|------|--------|
| 1.0 | 2026-07-19 | Targets set before data collection |

## 9. Revision History

| Version | Date | Change |
|---------|------|--------|
| 1.0 | 2026-07-19 | Targets set and **frozen** prior to data collection |
