# AI-Driven Secure Health Monitoring System

> **Platform:** Renesas RA6E2 Microcontroller  
> **Status:** Prototype / Ongoing Development (Supported by Renesas Electronics Corporation)

An AI-enabled embedded health monitoring system designed for real-time vital data acquisition, secure processing, and intelligent edge-based anomaly detection. Targeted at wearable and edge-health applications for early identification of critical conditions.

---

## 📌 Project Overview & Motivation

Early detection of conditions like arrhythmia, hypoxia, and fever can save lives. This system provides low-cost, low-power, continuous monitoring with secure data handling on edge hardware without constant hospital supervision.

### Core Targets & Health Conditions
| Target Condition | Monitored Input / Signal | Threshold / Clinical Condition | Primary Sensor |
| :--- | :--- | :--- | :--- |
| **Arrhythmia** | Heart Rate / RR Intervals | Tachycardia (>100 bpm) or Bradycardia (<60 bpm at rest) | **MAX30100** (PPG) |
| **Hypoxia** | SpO₂ Saturation (%) | Mild (90–95%) or Severe (<90%) | **MAX30100** (PPG) |
| **Fever** | Body Temperature | Body Temperature > 38°C | **MLX90614** (IR Temp) |

---

## 🛠️ System Architecture & Workflow
[ MAX30100 & MLX90614 ]
│
▼
[ Signal Preprocessing ]  ──► (Filtering, Outlier Removal, Normalization & Windowing)
│
▼
[ AI / ML Inference ]     ──► (Edge Impulse Model: Decision Trees / Neural Networks)
│
▼
[ Renesas RA6E2 MCU ]     ──► (Secure Boot & Encrypted Wi-Fi Transmission via HS4001)


1. **Data Acquisition:** Captures PPG pulse signals and IR body temperature across resting, active, and simulated condition datasets.
2. **Preprocessing & Cleaning:**
   * **Noise Filtering:** Applies moving average, low-pass, and band-pass filters to isolate valid heartbeat signals.
   * **Feature Extraction:** Computes average HR, SpO₂ trends, and temperature patterns.
   * **Normalization & Windowing:** Scales features ($0.0 - 1.0$) and segments signals into small time frames to detect trends over time.
3. **Edge AI Inference:** Lightweight models (Decision Trees or 2–3 layer Dense Neural Networks) trained via **Edge Impulse** for real-time local anomaly scoring.
4. **Hardware Security & Transmission:** Secure boot validation on the Renesas RA6E2 board with encrypted Wi-Fi data transfer.

---

## 🔐 Hardware & Technical Specifications

* **Microcontroller:** Renesas RA6E2 (Arm Cortex-M33)
* **Sensors:**
  * **MAX30100:** Optical Photoplethysmography (PPG) for Heart Rate and SpO₂.
  * **MLX90614:** Non-contact Infrared Temperature Sensor.
* **Connectivity Module:** HS4001 Wi-Fi Module.
* **Security Layer:** Renesas Hardware Security Engine, Secure Boot, and End-to-End Encrypted Payload Transmission.

---

## 💻 Signal Processing Snippet (Python Prototype)

Below is an example of the Python preprocessing pipeline used to clean raw PPG sensor readings by stripping physiological outliers and applying a moving average smoothing filter:

python
import numpy as np
import matplotlib.pyplot as plt

# Example noisy raw heart rate readings from MAX30100 sensor
raw_hr = [78, 80, 150, 82, 79, 200, 81, 77, 300, 76, 75]

# 1. Remove non-physiological outliers (<40 or >180 bpm)
cleaned_hr = [x for x in raw_hr if 40 <= x <= 180]

# 2. Moving average smoothing filter
window = 3
smoothed_hr = np.convolve(cleaned_hr, np.ones(window) / window, mode='valid')

print("Raw HR Data:    ", raw_hr)
print("Cleaned Data:   ", cleaned_hr)
print("Smoothed Data:  ", smoothed_hr)
