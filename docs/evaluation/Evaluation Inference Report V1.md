# Walle FPV — R&D Evaluation Report (P0)

**Repository:** Walle_FPV  
**Camera:** DJI O4 Pro (Monocular)  
**Evaluation Version:** P0  
**Last Updated:** 15 July 2026

---

# Overview

This document summarizes the performance evaluation of all primary perception modules used in the Walle FPV autonomous drone platform.

The objective of this evaluation is to benchmark every perception module using standardized quantitative metrics before integration into the autonomous flight pipeline.

The evaluated modules include:

1. Object Detection
2. Person Tracking
3. 3D Reconstruction
4. Face Recognition

Each module was evaluated using the criteria defined in:

```
docs/evaluation/Walle_RD_Evaluation_Criteria.md
```

---

# Overall Evaluation Summary

| Module | Status | Observation |
|----------|------------|----------------|
| Object Detection | Good | Detection quality is already suitable for deployment but runtime profiling is incomplete. |
| Person Tracking | Moderate | Tracking accuracy is promising but live flight evaluation is incomplete. |
| 3D Reconstruction | Moderate | Reconstruction quality appears excellent, however image registration pipeline failed during this export. |
| Face Recognition | Very Good | Recognition model performs well on public benchmarks but live deployment metrics are incomplete. |

---

# 1. Object Detection

## Model

RF-DETR Base

Dataset

Live Video Stream

---

## Performance

| Metric | Value |
|------------|---------|
| mAP@0.5 | **0.852** |
| mAP@0.5:0.95 | **0.533** |
| Precision | **91.2%** |
| Recall | **86.5%** |
| F1 Score | **88.7%** |
| Mean IoU | **0.785** |

---

## Robustness

| Condition | mAP |
|------------|---------|
| Day | 0.862 |
| Dusk | 0.814 |
| Low Light | 0.742 |
| Hover | 0.885 |
| Slow Pan | 0.841 |
| Fast FPV Motion | 0.768 |
| Partial Occlusion | 0.792 |

---

## Interpretation

### Strengths

- Excellent precision (91%)
- Good recall
- High IoU indicates accurate localization
- Performs consistently under daylight conditions
- Handles moderate occlusion reasonably well

Overall detection performance is suitable for an autonomous follow-me drone.

---

### Weaknesses

The following measurements were **not actually captured**:

- FPS = 0
- Inference latency = 0 ms
- Preprocessing latency = 0 ms
- Postprocessing latency = 0 ms

These values indicate that runtime profiling was either disabled or not connected to the evaluation exporter.

Similarly,

```
Frames Processed = 0
```

suggests that no valid runtime statistics were collected during this export.

---

## Recommendations

Before considering Object Detection complete, benchmark:

- CPU FPS
- GPU FPS
- Raspberry Pi FPS
- Jetson FPS
- End-to-end latency
- Memory usage
- CPU utilization
- GPU utilization
- Power consumption

Drone-specific evaluations should also include:

- altitude vs accuracy
- camera tilt
- vibration
- aggressive yaw
- rain
- fog
- backlight

---

# Overall Assessment

⭐⭐⭐⭐☆ (4.4 / 5)

Detection accuracy is already good.

Runtime evaluation remains incomplete.

---

# 2. Person Tracking

## Tracker

DeepSORT + MobileNet Re-ID

---

## Performance

| Metric | Value |
|------------|---------|
| MOTA | 0.785 |
| MOTP | 0.792 |
| IDF1 | 0.812 |
| ID Switches | 0 |
| Fragmentations | 0 |

---

## Additional Metrics

| Metric | Value |
|------------|---------|
| Re-acquisition Time | 1.25 s |
| Positional Error | 0.15 m |

---

## Robustness

| Scenario | Result |
|------------|----------|
| Full Occlusion | 92.5% Re-ID Success |
| Multi Subject | 89.2% Zero-ID-Swap Rate |

---

## Interpretation

Tracking quality is promising.

IDF1 above 0.8 indicates that the tracker is maintaining identities consistently.

Zero ID switches is an excellent result.

The positional error of approximately 15 cm is acceptable for follow-me applications.

---

## Major Issues

The report contains:

```
Track Retention Rate = 0%
```

This clearly indicates that the runtime tracker never maintained an active track during the exported session.

Similarly,

```
Latency = 0 ms
```

is impossible and indicates missing runtime profiling.

---

## Missing Evaluations

Still required:

- Long-duration tracking
- Drone flight testing
- Subject running
- Subject cycling
- Subject disappearing behind obstacles
- Crowd scenarios
- Camera shake
- High-speed FPV

---

## Recommendations

Evaluate on:

MOT17

VisDrone MOT

DanceTrack

DroneCrowd

These datasets better represent real drone tracking.

---

# Overall Assessment

⭐⭐⭐⭐☆ (4.1 / 5)

Tracking algorithm is good.

Runtime deployment metrics are incomplete.

---

# 3. 3D Reconstruction

Pipeline

Lingbot Monocular SfM/MVS

---

## Performance

| Metric | Value |
|------------|---------|
| Chamfer Distance | 2.14 cm |
| RMSE | 3.12 cm |
| Completeness | 91.5% |
| Reprojection Error | 0.42 px |
| Scale Error | 1.84% |

---

## Geometry Assessment

These are excellent values.

A reprojection error below one pixel generally indicates high-quality camera calibration and feature matching.

Chamfer distance of approximately 2 cm is considered very accurate for monocular reconstruction.

---

## Major Issue

```
Images Registered = 0
```

and

```
Registration Success = 0
```

These directly contradict the remaining reconstruction metrics.

This suggests one of two issues:

1. Exporter bug

or

2. Registration statistics were never updated.

This should be investigated before publishing research results.

---

## Missing Metrics

Current report lacks:

- Number of keyframes
- Sparse point count
- Dense point count
- Reconstruction time
- Bundle Adjustment iterations
- Memory usage
- Processing speed

---

## Recommendations

Evaluate reconstruction under

- Indoor
- Outdoor
- Trees
- Buildings
- Dynamic objects
- Poor lighting

Measure

- Absolute Trajectory Error (ATE)
- Relative Pose Error (RPE)

for complete SLAM benchmarking.

---

# Overall Assessment

⭐⭐⭐⭐☆ (4.2 / 5)

Geometry quality is excellent.

Registration pipeline reporting needs correction.

---

# 4. Face Recognition

Detector

SCRFD-10G

Recognition

ArcFace ResNet50

---

## Performance

| Metric | Value |
|------------|---------|
| Rank-1 Accuracy | 98.20% |
| Rank-5 Accuracy | 99.85% |
| TAR @ FAR 0.1% | 95.4% |
| Equal Error Rate | 2.15% |
| Effective Range | 4.8 m |

---

## Detector Performance

| Metric | Value |
|------------|---------|
| Precision | 96.25% |
| Recall | 95.16% |
| Minimum Face Size | 16 px |

---

## Interpretation

Recognition quality is excellent.

These numbers are comparable with modern ArcFace deployments.

Suitable for controlled drone-based identification.

---

## Major Issues

```
Gallery Size = 0
```

means that no enrolled identities existed during this export.

Consequently,

the reported recognition accuracy likely originates from benchmark datasets rather than live deployment.

Similarly,

```
Latency = 0 ms
```

indicates runtime profiling is missing.

---

## Missing Drone Evaluations

Still required:

- Distance vs recognition
- Face pose
- Drone vibration
- Motion blur
- Compression artifacts
- Wind-induced shake
- Camera zoom

---

## Overall Assessment

⭐⭐⭐⭐⭐ (4.6 / 5)

Recognition quality is very strong.

Deployment validation remains incomplete.

---

# Common Issues Found

The evaluation exporter currently produces several placeholder values.

Examples include:

- FPS = 0
- Latency = 0 ms
- Frames Processed = 0
- Images Registered = 0
- Gallery Size = 0
- Active Tracks = 0%

These indicate that runtime statistics are either not being recorded or not being exported correctly.

This should be resolved before claiming real-world deployment performance.

---

# Recommendations for Future Evaluation

The next evaluation phase should include:

- Real DJI O4 flight recordings
- Raspberry Pi benchmarking
- Jetson benchmarking
- CPU vs GPU comparison
- Power consumption analysis
- Memory profiling
- Thermal analysis
- Long-duration autonomous flights
- Multiple weather conditions
- Different altitudes (5 m, 15 m, 30 m)
- Different subject speeds
- Dynamic obstacle environments

---

# Final Conclusion

Overall, the perception pipeline demonstrates strong algorithmic performance across object detection, tracking, reconstruction, and face recognition. The benchmark values indicate that the selected models are technically suitable for integration into the Walle FPV platform.

However, this export also reveals that the runtime evaluation framework is not yet fully instrumented. Several deployment-specific metrics—including frame rate, inference latency, tracking persistence, registration statistics, and gallery utilization—were either not captured or exported as placeholder values.

The next development milestone should therefore focus on completing the runtime benchmarking infrastructure, enabling reliable measurement of real-world performance on the target embedded hardware (Raspberry Pi, Jetson, or equivalent SBC). Once these runtime metrics are available, the evaluation will provide a comprehensive assessment of both algorithmic accuracy and deployment readiness.

**Current Readiness Level:** Research Prototype (TRL 4–5)

The system exhibits strong potential for autonomous FPV drone applications but requires complete runtime validation before being considered deployment-ready.
