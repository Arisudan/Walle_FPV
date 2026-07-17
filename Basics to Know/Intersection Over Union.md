# 🎯 Intersection over Union (IoU)

> **Intersection over Union (IoU) measures how closely a predicted bounding box matches the ground-truth bounding box.**

---

## 🎯 Purpose

- Evaluate object detection accuracy
- Measure bounding box overlap
- Validate object tracking performance

---

## 📐 Formula

\[
\text{IoU} =
\frac{\text{Area of Intersection}}
{\text{Area of Union}}
\]

Where:

- **Intersection** → Overlapping area between predicted and ground-truth boxes.
- **Union** → Total area covered by both boxes.

---

## 🖼️ Visualization

```text
Ground Truth Box
+----------------------+
|      +---------+     |
|      |Overlap  |     |
|      |         |     |
|      +---------+     |
+----------------------+

Predicted Box
```

---

## 📊 IoU Score

| IoU | Interpretation |
|-----|----------------|
| **1.0** | Perfect overlap |
| **> 0.75** | Excellent detection |
| **≥ 0.50** | Valid detection (commonly used threshold) |
| **< 0.50** | Poor detection |
| **0** | No overlap |

---

## 🚁 IoU in Drones

IoU is commonly used for:

- Object Detection (e.g., YOLO)
- Object Tracking
- Collision Avoidance
- Visual Navigation (Visual SLAM)

---

## 📌 Key Points

- **IoU ranges from 0 to 1.**
- **Higher IoU = Better localization accuracy.**
- **IoU ≥ 0.5** is commonly considered a correct detection.
- Widely used as an evaluation metric in object detection and tracking.
