# 📏 Evaluation Metrics

Evaluating a depth estimation model requires measuring how accurately it predicts the distance between the camera and every pixel in an image. Since different models produce either **relative** or **metric (absolute)** depth, several evaluation metrics are used to compare their performance.

---

# 🎯 Why Evaluation Metrics Matter

A model may generate visually appealing depth maps but still perform poorly in real-world robotics or autonomous navigation. Evaluation metrics provide a quantitative way to compare models based on:

- Prediction accuracy
- Distance estimation error
- Edge preservation
- Boundary sharpness
- Computational efficiency
- Real-time capability

These metrics help determine which model is best suited for a particular application.

---

# 📊 Types of Evaluation Metrics

Depth estimation metrics are generally divided into two categories:

## 1. Monocular Depth Metrics

Used for models that estimate depth from a **single RGB image**.

Examples:

- Depth Anything V2
- MiDaS
- Depth Pro
- Marigold

---

## 2. Stereo Depth Metrics

Used for models that estimate depth from **two synchronized camera images**.

Example:

- FoundationStereo

---

# 📈 Common Monocular Metrics

## 1. Absolute Relative Error (AbsRel)

Measures the average relative difference between the predicted depth and the actual (ground truth) depth.

### Formula

```text
AbsRel = |Predicted - Ground Truth| / Ground Truth
```

### Interpretation

- Lower value = Better model
- Measures overall depth accuracy
- Sensitive to large prediction errors

### Typical Performance

| Performance | AbsRel |
|-------------|---------|
| Excellent | < 0.07 |
| Very Good | 0.07 – 0.10 |
| Good | 0.10 – 0.15 |
| Average | > 0.15 |

---

## 2. δ₁ Accuracy (Delta Accuracy)

Measures the percentage of pixels whose predicted depth lies within **25%** of the ground truth.

### Interpretation

Higher is better.

Example

```
δ₁ = 96%

Meaning:

96% of all pixels
are predicted within
25% of the true depth.
```

### Typical Performance

| Performance | δ₁ |
|-------------|-----|
| Excellent | >95% |
| Very Good | 90–95% |
| Good | 85–90% |
| Average | <85% |

---

## 3. Root Mean Square Error (RMSE)

Measures the average magnitude of prediction error.

### Formula

```text
RMSE = √((Prediction − Ground Truth)²)
```

### Interpretation

Lower RMSE indicates better predictions.

RMSE penalizes large errors more heavily than AbsRel, making it useful for evaluating robustness.

---

## 4. Log RMSE

Instead of comparing absolute depth values, Log RMSE compares logarithmic depth values.

Useful when evaluating scenes with:

- Very near objects
- Very distant objects
- Wide depth ranges

Lower values indicate better performance.

---

## 5. F1 Boundary Score

Measures how accurately a model preserves object boundaries in the generated depth map.

Instead of evaluating every pixel, this metric focuses on edges.

Example:

```
Tree

███████

Sharp boundary ✓

Blurred boundary ✗
```

Higher values indicate better boundary preservation.

Important for:

- Robotics
- Object grasping
- Image segmentation
- Scene reconstruction

---

# 📊 Stereo Depth Metrics

Stereo models predict disparity before converting it into depth.

Different evaluation metrics are therefore used.

---

## 1. BP-2 (Bad Pixel 2)

Measures the percentage of pixels whose disparity error exceeds **2 pixels**.

Lower is better.

Example

```
100 pixels

Only 1 incorrect

BP-2 = 1%
```

A lower BP-2 indicates more reliable stereo matching.

---

## 2. D1 Error

Measures the percentage of pixels whose disparity error is:

- Greater than 3 pixels
- AND greater than 5% of the ground truth disparity

Widely used for evaluating:

- KITTI
- Autonomous driving
- Robotics

Lower values indicate higher accuracy.

---

## 3. End Point Error (EPE)

Measures the average disparity error for every pixel.

```text
EPE = Average Pixel Error
```

Lower values indicate more precise disparity estimation.

---

# 📷 Relative Depth vs Metric Depth

One of the biggest differences between depth estimation models is the type of depth they produce.

---

## Relative Depth

Relative depth only predicts which objects are closer or farther.

Example

```
Person

Car

Building

Result:

Person → Closest

Car → Medium

Building → Farthest
```

Actual distance is unknown.

Cannot determine whether the person is:

- 2 meters away
- 20 meters away

Relative depth is sufficient for:

- Object ordering
- Scene understanding
- Image editing
- Background removal

Models:

- MiDaS
- Depth Anything V2
- Marigold
- DepthCrafter

---

## Metric Depth

Metric depth predicts actual physical distance.

Example

```
Person

↓

2.34 m

Car

↓

8.91 m

Building

↓

42.7 m
```

This is essential for:

- Robotics
- Drone navigation
- Autonomous driving
- SLAM
- Obstacle avoidance

Models:

- Depth Pro
- FoundationStereo

---

# ⚡ Inference Speed

Besides accuracy, speed is one of the most important evaluation criteria.

Real-time systems typically require:

| Application | Desired FPS |
|-------------|-------------|
| Mobile Robots | >20 FPS |
| Autonomous Cars | >30 FPS |
| FPV Drones | >30 FPS |
| Embedded Systems | >15 FPS |

Approximate relationship:

| Time per Frame | FPS |
|----------------|-----|
| 1000 ms | 1 FPS |
| 500 ms | 2 FPS |
| 100 ms | 10 FPS |
| 33 ms | 30 FPS |
| 16 ms | 60 FPS |

A highly accurate model may not be suitable if it cannot meet real-time processing requirements.

---

# 🗂 Common Benchmark Datasets

Researchers evaluate depth estimation models using publicly available datasets.

| Dataset | Primary Use |
|---------|-------------|
| KITTI | Autonomous driving |
| NYUv2 | Indoor depth estimation |
| ETH3D | Indoor & outdoor stereo |
| ScanNet | Indoor scene reconstruction |
| Sun RGB-D | Indoor robotics |
| Sintel | Synthetic video evaluation |
| Scene Flow | Stereo depth estimation |
| Middlebury | High-precision stereo evaluation |

These datasets provide ground truth depth maps for fair and standardized comparisons.

---

# 📊 Summary of Evaluation Metrics

| Metric | Better Value | Purpose |
|---------|--------------|---------|
| AbsRel | Lower | Relative depth accuracy |
| RMSE | Lower | Overall prediction error |
| Log RMSE | Lower | Large depth range evaluation |
| δ₁ | Higher | Percentage of accurate pixels |
| F1 | Higher | Boundary preservation |
| BP-2 | Lower | Stereo disparity error |
| D1 | Lower | KITTI stereo accuracy |
| EPE | Lower | Average disparity error |

---

# 💡 Choosing the Right Metric

Different applications prioritize different metrics.

| Application | Important Metrics |
|-------------|------------------|
| Robotics | AbsRel, δ₁, Speed |
| Autonomous Driving | D1, BP-2, EPE |
| Drone Navigation | AbsRel, δ₁, FPS |
| AR / VR | F1, RMSE |
| 3D Reconstruction | F1, RMSE |
| Video Processing | Temporal consistency, FPS |

---

# 📌 Key Takeaways

- **AbsRel** evaluates overall depth accuracy.
- **δ₁** measures the percentage of correctly estimated pixels.
- **F1** evaluates boundary sharpness.
- **RMSE** measures the average prediction error.
- **BP-2**, **D1**, and **EPE** are used for stereo depth estimation.
- **Inference speed** is critical for real-time applications such as robotics and autonomous drones.

---

# ➡ Next Section

The next part provides an in-depth overview of **Depth Anything V2**, including:

- Architecture
- Working Principle
- Training Strategy
- Model Variants
- Strengths
- Weaknesses
- Benchmark Performance
- Applications
- Deployment Recommendations
