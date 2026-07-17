# 📊 Mean Average Precision (mAP)

> **Mean Average Precision (mAP) is the standard metric used to evaluate the accuracy of object detection models.**

---

## 🎯 Purpose

- Evaluate object detection performance
- Measure detection accuracy across all classes

---

## 🔄 mAP Hierarchy

```text
Precision
      ↓
Average Precision (AP)
      ↓
Mean Average Precision (mAP)
```

---

## 🧩 Components

| Metric | Description |
|--------|-------------|
| **Precision** | Percentage of correct detections |
| **AP** | Detection accuracy for a single class |
| **mAP** | Average AP across all classes |

---

## 🚁 mAP in Drones

Used for:

- Object Detection
- Obstacle Detection
- Search & Rescue
- Aerial Inspection
- Autonomous Navigation

---

## 📊 Score Interpretation

| mAP | Performance |
|-----|-------------|
| **90–100%** | Excellent |
| **70–90%** | Good |
| **50–70%** | Fair |
| **< 50%** | Poor |

---

## 📌 Key Points

- **Higher mAP = Better object detection.**
- Evaluates both **detection** and **localization** accuracy.
- Standard benchmark for models like **YOLO**, **RF-DETR**, and **DETR**.
