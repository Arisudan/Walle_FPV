# 🎯 MOTA & MOTP

> **MOTA measures tracking accuracy, while MOTP measures localization precision.**

---

## 📊 Quick Comparison

| Metric | Measures | Better |
|---------|----------|--------|
| **MOTA** (Multi-Object Tracking Accuracy) | Overall tracking performance | Higher ↑ |
| **MOTP** (Multi-Object Tracking Precision) | Bounding box localization accuracy | Lower Distance / Higher IoU ↑ |

---

# 📈 MOTA (Multi-Object Tracking Accuracy)

Measures how well the tracker:

- Detects objects
- Maintains object identities
- Avoids tracking errors

### Errors Considered

| Error | Description |
|--------|-------------|
| **FP** | False Positives (extra detections) |
| **FN** | False Negatives (missed objects) |
| **IDS** | Identity Switches (wrong object ID assignment) |

> 📌 **Higher MOTA = Better overall tracking performance.**

---

# 📍 MOTP (Multi-Object Tracking Precision)

Measures how accurately the tracker localizes detected objects.

Typically computed using:

- Bounding Box Distance
- Intersection over Union (IoU)

> 📌 **Higher IoU (or lower localization error) = Better MOTP.**

---

## ⚖️ MOTA vs MOTP

| MOTA | MOTP |
|------|------|
| Evaluates tracking accuracy | Evaluates localization precision |
| Counts FP, FN & IDS | Measures bounding box alignment |
| Depends on object association | Independent of identity tracking |
| Higher is better | Better localization = Higher IoU / Lower error |

---

## 📌 Key Points

- **MOTA** → *"Did the tracker correctly track the objects?"*
- **MOTP** → *"How accurately were the objects localized?"*
- A good tracker should achieve **high MOTA** and **high localization precision (high IoU / low error).**
