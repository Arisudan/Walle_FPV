# Walle FPV — R&D Evaluation Criteria (P0)

Repository: Walle_FPV
Camera: DJI O4 Pro (monocular)
Status: P0 Priority
Last Updated: 2026-07-14

## Purpose
This document defines the standardized evaluation criteria used to benchmark
all core perception/R&D modules built on the Walle FPV platform. Every module
below must be tested and reported using these metrics before being marked
complete in the R&D log.

---

## 1. Object Detection

Primary Metrics:
- Precision, Recall, F1-Score
- mAP@0.5 and mAP@0.5:0.95
- Mean IoU (bounding box localization accuracy)

Speed / Deployment Metrics:
- FPS on target compute hardware
- Inference latency (ms) — pre/inference/post breakdown
- Model size (MB), parameter count

Robustness Metrics:
- Accuracy across altitude/distance bands (5m / 15m / 30m AGL)
- Accuracy under lighting variation (day / dusk / low-light)
- Accuracy under motion blur (hover / slow pan / fast FPV motion)
- Accuracy under partial occlusion

Report Template:
Model: 
Dataset (name, size, split): 
mAP@0.5: 
mAP@0.5:0.95: 
Precision / Recall / F1: 
Avg FPS: 
Avg Inference Latency (ms): 

---

## 2. Person Tracking (Follow-Me)

Core MOT Metrics:
- MOTA (Multi-Object Tracking Accuracy)
- MOTP (Multi-Object Tracking Precision)
- IDF1 (Identity F1)
- ID Switches (IDSW)
- Fragmentations (FM)

Application-Level Metrics:
- Track retention rate (% of flight time correctly tracked)
- Re-acquisition time after occlusion/frame exit (s)
- End-to-end tracking latency (ms)
- Positional tracking error (px or est. meters)

Robustness Scenarios:
- Partial/full occlusion
- Subject exit/re-entry from frame
- Multiple similar-looking subjects (ID confusion test)
- Fast subject motion / drone yaw

Report Template:
Tracker: 
MOTA / MOTP / IDF1: 
ID Switches / Fragmentations: 
Track Retention Rate: 
Avg Re-acquisition Time (s): 
End-to-End Latency (ms): 

---

## 3. 3D Reconstruction (Lingbot Pipeline)

Geometric Accuracy Metrics:
- Chamfer Distance (cm)
- RMSE vs ground truth (cm)
- Completeness (% surface reconstructed)
- Accuracy (% points within threshold, e.g. 5cm)

Camera Pose / SfM Metrics:
- Reprojection error (px)
- ATE / RPE (if ground-truth trajectory available)
- Registration success rate (images registered / total)

Mesh Quality:
- Point cloud density (points/m²)
- Texture quality (qualitative + PSNR/SSIM if applicable)
- Hole/gap ratio

Scale Recovery (Monocular Constraint):
- Method used (GPS/RTK tags, known object reference, IMU-assisted)
- Scale error (%)

Report Template:
Pipeline: Lingbot (monocular SfM/MVS)
Images Registered: 
Reprojection Error (px): 
Chamfer Distance (cm): 
Completeness: 
Scale Recovery Method: 
Scale Error: 

---

## 4. Face Recognition

Detection Stage:
- Precision / Recall / F1 for face bounding box
- Minimum usable face resolution (px width) vs distance

Recognition Stage:
- Accuracy (% correct matches)
- TAR @ FAR (e.g., TAR=95% @ FAR=0.1%)
- ROC / AUC
- Rank-1 / Rank-5 identification accuracy
- Equal Error Rate (EER)

Drone-Specific Robustness:
- Accuracy vs camera-to-face distance
- Accuracy vs face pose (frontal / 3-4 / profile)
- Accuracy vs transmission compression artifacts
- Accuracy under motion blur

Report Template:
Detector: 
Recognition Model: 
Gallery Size: 
TAR @ FAR=0.1%: 
Rank-1 Accuracy: 
EER: 
Effective Recognition Range (m): 

---

## General Reporting Rules (applies to all 4 modules)
- Always specify inference hardware (device, RAM, compute unit).
- Always report dataset size, split, and source (drone-captured vs public benchmark).
- Use a consistent table format: Metric | Value | Test Condition | Notes.
- Each module's results go in its own subfolder under /docs/evaluation/results/.
