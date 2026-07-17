# 📶 Signal-to-Noise Ratio (SNR)

> **Signal-to-Noise Ratio (SNR) measures how strong the desired signal is compared to the background noise.**

---

## 🎯 Purpose

- Evaluate communication link quality
- Determine signal reliability
- Detect interference and noise

---

## 📊 Unit

- **Decibels (dB)**

---

## 📈 SNR Interpretation

| SNR Value | Signal Quality |
|-----------|----------------|
| **> 20 dB** | Excellent 🟢 |
| **10 – 20 dB** | Good 🟡 |
| **0 – 10 dB** | Weak 🟠 |
| **< 0 dB** | Poor 🔴 (Noise stronger than signal) |

> 📌 **Higher SNR = Cleaner and more reliable communication.**

---

## 🔄 Concept

```text
Signal Strength
        │
        ▼
+--------------------+
| Desired Signal     |
+--------------------+

Background Noise
        │
        ▼
+--------------------+
| RF Noise           |
| Interference       |
| Environmental Noise|
+--------------------+

SNR = Signal / Noise
```

---

## 🚁 SNR in FPV Drones

SNR is used to evaluate the quality of the communication link between:

- Radio Transmitter (TX)
- Radio Receiver (RX)

A higher SNR results in:

- Stable control link
- Reliable telemetry
- Fewer video glitches
- Reduced chance of failsafe

---

## 📡 RSNR (Received Signal-to-Noise Ratio)

**RSNR** is the SNR measured at the **receiver**.

It indicates how well the receiver can distinguish the transmitted signal from background noise.

---

## 📊 SNR vs RSSI vs LQ

| Metric | Measures |
|---------|----------|
| **RSSI** | Received signal strength |
| **SNR / RSNR** | Signal quality relative to noise |
| **LQ (Link Quality)** | Percentage of successfully received packets |

---

## 📌 ExpressLRS Note

For **ExpressLRS (ELRS)** systems:

- Modern RF chips can decode packets even when the signal is **below the noise floor**.
- As a result, **RSNR may appear low or clipped**, even when the control link is functioning correctly.
- **Link Quality (LQ)** is generally a more reliable indicator of link health.

> 🎯 **Recommended:** Monitor **LQ** first, then use **RSSI** and **RSNR** as supporting metrics.

---

## 🎯 Ideal Link Quality

| Metric | Recommended Value |
|---------|-------------------|
| **LQ (Link Quality)** | **> 90** |
| **SNR** | As high as possible |

---

## 📌 Key Points

- SNR compares **signal strength** against **background noise**.
- Higher SNR indicates a cleaner and more reliable communication link.
- RSNR represents the SNR measured at the receiver.
- In ExpressLRS, **LQ is generally more useful than RSNR** for assessing link health.
```
