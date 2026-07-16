# 🤖 Object Detection in Walle GCS — Simple English Explanation

> This is a Ground Control Station (GCS) for a drone. It receives a live video feed from the drone's camera and uses AI to understand what's in the video. Let's break down **everything** related to object detection step by step.

---

## 🌍 The Big Picture — What Happens in Simple Words

Imagine the drone is flying and its camera is showing you a live video. The project does **4 big AI jobs** on that live video, all at the same time:

1. **Object Detection** — "Is there a person? A car? A truck?" → Draws colored boxes around them
2. **Depth Estimation** — "How far away is that object?" → Colorizes the scene based on distance
3. **Human Tracking** — "That person is #3 — they moved to the left!" → Follows people across frames
4. **Face Recognition** — "That's Alice!" → Recognizes known people by their face

This document focuses on **#1 and #2**, with some detail on **#3**.

---

## 🧠 Part 1: What is Object Detection?

**Object Detection** means looking at an image and answering:
- **WHAT** is in it? (a person, a car, a dog, etc.)
- **WHERE** is it? (draw a box around it — called a "bounding box")

Think of it like this: you look at a photo and say *"there's a dog in the top-left corner, and a car in the middle."* The AI does this automatically, for every frame of video (30 times per second!).

---

## 🛠️ Part 2: The Tool Used — RF-DETR

The project uses a model called **RF-DETR** (Real-time Features Detection Transformer).

> **What is a "model"?** It's like a super-smart algorithm that has been trained by looking at millions of images and learning to recognize objects. You don't have to teach it yourself — you just use it.

There are **two editions** of the app:

| Edition | Model Used | Speed vs Accuracy |
|---|---|---|
| `base` | RF-DETR **Base** | Faster, a bit less accurate |
| `max` | RF-DETR **Medium** | Slightly slower, more accurate |

**Where is this in the code?** → [walle_gcs.py Lines 124-127](file:///d:/Drone%20Projects/walle_gcs/walle_gcs.py#L124-L127)

```python
"base": Edition("base", RFDETRBase,   "Small", ...),
"max":  Edition("max",  RFDETRMedium, "Base",  ...),
```

---

## 📦 Part 3: What Objects Can It Detect?

The model knows **80 types of objects** from the COCO dataset (a famous AI training dataset). Here are some important ones:

| Number | Object |
|---|---|
| 1 | **person** |
| 2 | bicycle |
| 3 | **car** |
| 8 | **truck** |
| 17 | cat |
| 18 | dog |
| 62 | chair |

**Where is this in the code?** → [walle_gcs.py Lines 375-394](file:///d:/Drone%20Projects/walle_gcs/walle_gcs.py#L375-L394) — the `COCO_NAMES` dictionary.

### 🚨 Danger Classes
Some objects are flagged as **dangerous** for the drone:
```python
DANGER_CLASSES = {"person", "stairs", "obstacle", "car", "truck"}
```
When one of these is detected, it gets **priority** in the display — shown first and most prominently.

---

## ⚙️ Part 4: The ObjectDetectionEngine — The Brain of Detection

The `ObjectDetectionEngine` class (in [walle_gcs.py Line 470](file:///d:/Drone%20Projects/walle_gcs/walle_gcs.py#L470-L474)) is the heart of the whole detection system.

### How it works — Step by Step:

```
Live Camera Frame
       ↓
 ObjectDetectionEngine.process_frame()
       ↓              ↓
 DETECTION         DEPTH
  WORKER          WORKER
 (RF-DETR)     (Depth Anything V2)
       ↓              ↓
 "Person at      "That person is
  top-left"       2.3 meters away"
       ↓
 Draw boxes on the video frame
       ↓
 Show on screen
```

### 🚦 Why Two Workers?

Running AI models is **slow** (it can take 50–200 milliseconds). If the main app waited for the AI every frame, the video would freeze or lag.

So instead:
- The **main thread** (what you see on screen) runs at full camera speed (30 FPS)
- The **detection worker** runs in the *background*, in its own thread, processing frames as fast as it can
- When it finishes analyzing a frame, it saves the results. The main thread **draws those saved results** onto the next video frame

This is called **"non-blocking"** — the screen never freezes waiting for AI.

---

## 📏 Part 5: Depth Estimation — How Far Away Is It?

### The Tool: Depth Anything V2

**Depth Anything V2** is another AI model that looks at a regular camera image and estimates how far each pixel is from the camera. No special 3D camera needed!

```
Regular camera image  →  Depth Anything V2  →  Depth map (colorized)
```

The colorized map uses the **Spectral_r colormap**:
- 🔴 Red/warm = very close to the drone
- 🔵 Blue/cool = far from the drone

**Two model sizes used:**

| Edition | Depth Model |
|---|---|
| `base` | Depth-Anything-V2-Metric-Indoor-**Small** |
| `max` | Depth-Anything-V2-Metric-Indoor-**Base** |

**Where is this in the code?** → [walle_gcs.py Lines 370-373](file:///d:/Drone%20Projects/walle_gcs/walle_gcs.py#L370-L373)

### How Distance Is Calculated Per Object

For each detected object (e.g., a person):
1. Find the center region of the bounding box
2. Sample the depth map in that region
3. Take the **25th percentile** depth value (ignores noisy pixels)
4. Smooth it over 5 frames (so the number doesn't jump around)
5. Use the drone's **altitude** and **camera pitch** (tilt angle) to get a more accurate real-world distance

**The result**: every detected object shows a distance in meters next to its label on screen.

**Where is this?** → [walle_gcs.py Lines 775-801](file:///d:/Drone%20Projects/walle_gcs/walle_gcs.py#L775-L801)

---

## 🚦 Part 6: Depth Zones — Obstacle Avoidance Advisory

The frame is split into **three zones**: Left, Center, Right.

For each zone, the system checks "how close is the closest thing?"

```
+----------+----------+----------+
|          |          |          |
|   LEFT   |  CENTER  |  RIGHT   |
|          |          |          |
+----------+----------+----------+
```

Based on the center zone depth, the system shows a **warning banner**:

| Situation | Decision |
|---|---|
| Center is very close | **STOP** |
| Center is close, left is clearer | **AVOID LEFT** |
| Center is close, right is clearer | **AVOID RIGHT** |
| All clear | **CLEAR** |

> ⚠️ **Important Note**: As mentioned in the README, this is **display-only** — it shows a banner on the HUD but does NOT actually control the drone's movement yet. That wiring is missing.

**Where is this?** → [walle_gcs.py Lines 446-467](file:///d:/Drone%20Projects/walle_gcs/walle_gcs.py#L446-L467)

---

## 🏷️ Part 7: Object Tracking — Giving Each Object a Stable ID

Imagine the drone sees a person. In frame 1 they get detected. In frame 2, they move a bit. Should the system think it's a *new* person or the *same* person?

**Object tracking** solves this by giving each detected object a **stable ID** that persists across frames.

### Two Tracking Methods (used as fallback):

#### Method 1 (Preferred): DeepSort + MobileNet
- **How it works**: Uses a MobileNet neural network to create an "appearance embedding" (like a fingerprint) of each object. Even if the object moves or disappears briefly, it can be re-identified by *what it looks like*, not just *where it was*.
- Uses a **Kalman filter** internally to predict where the object will be next
- Survives short **occlusions** (when an object goes behind something)
- Max age: 120 detection-frames before track is dropped

#### Method 2 (Fallback): IoU Tracker
- **IoU** = Intersection over Union — measures how much two boxes overlap
- If a box from the current frame overlaps a lot with a box from the last frame (same class), they're the same object
- Simpler, no neural network needed, but can't handle occlusion as well

**Where is this?** → [walle_gcs.py Lines 939-1020](file:///d:/Drone%20Projects/walle_gcs/walle_gcs.py#L939-L1020)

---

## 🧍 Part 8: Human Tracker — Special Tracking Just for People

There's a separate, much more advanced tracker **only for humans**, in [human_tracker_engine.py](file:///d:/Drone%20Projects/walle_gcs/human_tracker_engine.py).

It uses:
- **YOLOv8-pose** — detects people AND their body keypoints (nose, shoulders, hips, knees, etc.)
- **DeepSort** — tracks each person with a stable ID across frames
- **Kalman Filter** — predicts where the person will be
- **Motion Classifier** — figures out what the person is doing

### What the Motion Classifier Does

Looking at the body keypoints, it figures out the **motion state**:

| State | How It Detects It |
|---|---|
| **FALLING** | Nose is below the hips (person is tilted over) |
| **SITTING** | Knees are at the same height as hips |
| **CROUCHING** | Entire body height (nose to ankle) is very short |
| **RUNNING** | Moving fast (high speed value) |
| **WALKING** | Moving at medium speed |
| **STATIONARY** | Barely moving |

**Why does this matter?** The motion state feeds into the **Trajectory Predictor** to shape the prediction arc:
- A running person → long arc predicted far in front of them
- A sitting person → almost no arc (they're not going anywhere)
- A falling person → downward arc with gravity

**Where is this?** → [core/motion_classifier.py](file:///d:/Drone%20Projects/walle_gcs/core/motion_classifier.py)

---

## 📈 Part 9: Trajectory Prediction — Where Will This Person Go?

After detecting and tracking a person, the system **predicts their future path** using a **Kalman Filter**.

### What is a Kalman Filter?

A Kalman Filter is a math algorithm that:
1. Keeps track of **position** (where they are now)
2. Estimates **velocity** (how fast and which direction they're moving)
3. Estimates **acceleration** (are they speeding up or slowing down?)
4. Uses these to **predict future positions**

The state has 6 values: `[x, y, vx, vy, ax, ay]`
- `x, y` = position
- `vx, vy` = velocity (speed in x and y direction)
- `ax, ay` = acceleration

**Where is this?** → [core/kalman_tracker.py](file:///d:/Drone%20Projects/walle_gcs/core/kalman_tracker.py)

### Ghost Tracking — What Happens When Someone Disappears?

If a person walks behind a tree or wall and temporarily disappears from the camera:
1. The system enters **"ghost mode"** for that track
2. It keeps **predicting** where they should be, based on their last known velocity
3. It draws a dotted/ghost box at their predicted location
4. After too many frames with no detection (`GHOST_MAX_FRAMES`), the track is **removed**

This is how the system avoids falsely "forgetting" a person just because they briefly went out of sight.

**Where is this?** → [core/trajectory_predictor.py Lines 74-85](file:///d:/Drone%20Projects/walle_gcs/core/trajectory_predictor.py#L74-L85)

---

## 🎨 Part 10: How Detections Are Drawn on Screen

### Corner Brackets Style
Instead of boring full rectangles, the code draws **corner bracket boxes** (like `⌜ ⌟`) around detected objects. This looks cleaner and less cluttered.

**Where is this?** → [walle_gcs.py Lines 1108-1124](file:///d:/Drone%20Projects/walle_gcs/walle_gcs.py#L1108-L1124)

### Color Coding
Each object class gets its own color:
```python
"person"  → green  (0, 220, 0)
"car"     → red    (255, 100, 100)
"truck"   → dark red (255, 50, 50)
"bicycle" → light green
"dog"     → purple
"cat"     → pink
```
Danger classes get **extra visual emphasis**.

### Label on Each Box
Each box shows: `ClassName #ID  Dist: 2.3m  (conf%)`

For example: `person #3  Dist: 2.3m  (87%)`

---

## 🔍 Part 11: Filtering What Gets Shown

Not everything detected gets drawn on screen. There's a **filtering step** that:

1. **Class filter** — Only show objects in the whitelist: `person, car, truck, bus, bicycle, motorcycle, dog, cat, chair, couch, bed, dining table`

2. **Distance cutoff** — Only show objects closer than **6 meters** (configurable). Far objects are hidden because they're less relevant for drone safety.

3. **Relative depth filter** — Among all detected objects, hide the **farthest 35%** (keeps display clean even when depth calibration drifts)

4. **Priority ranking** — Objects are sorted:
   - 🚨 Danger classes first (person, car, truck)
   - 📏 Then by distance (closest first)
   - 📊 Then by confidence score

**Where is this?** → [walle_gcs.py Lines 1035-1106](file:///d:/Drone%20Projects/walle_gcs/walle_gcs.py#L1035-L1106)

---

## 🏗️ Part 12: How All the Pieces Fit Together

```
┌─────────────────────────────────────────────────────┐
│                  Live Camera Feed                    │
│              (DJI O4 via HDMI capture)               │
└───────────────────────┬─────────────────────────────┘
                        │
          ┌─────────────▼────────────────┐
          │     process_frame()          │
          │  (called every camera frame) │
          └──┬──────────────────────┬───┘
             │                      │
     ┌───────▼───────┐    ┌─────────▼──────────┐
     │  Detection    │    │   Depth Worker      │
     │  Worker       │    │   (Background)      │
     │  (Background) │    │                     │
     │               │    │  Depth Anything V2  │
     │  RF-DETR      │    │  → depth map        │
     │  → boxes      │    │  → colorized view   │
     │  → class IDs  │    └─────────────────────┘
     │  → confidence │             │
     └───────┬───────┘             │
             │                     │
     ┌───────▼───────────────────────┐
     │      Combine Results          │
     │  - Sample depth per box       │
     │  - Compute distance in meters │
     │  - Assign stable track IDs    │
     │  - Filter for display         │
     └───────────────┬───────────────┘
                     │
     ┌───────────────▼───────────────┐
     │       Draw on Screen          │
     │  - Corner bracket boxes       │
     │  - Color coded by class       │
     │  - Labels with distance       │
     │  - Depth zone banner (HUD)    │
     └───────────────────────────────┘
```

---

## 📁 Part 13: Which Files Do What (Object Detection)

| File | What It Does |
|---|---|
| [walle_gcs.py Lines 470–1200](file:///d:/Drone%20Projects/walle_gcs/walle_gcs.py#L470-L1200) | The main `ObjectDetectionEngine` class — runs RF-DETR + Depth Anything V2, assigns track IDs, filters & draws results |
| [human_tracker_engine.py](file:///d:/Drone%20Projects/walle_gcs/human_tracker_engine.py) | Separate engine just for tracking humans with YOLOv8-pose + DeepSort |
| [core/kalman_tracker.py](file:///d:/Drone%20Projects/walle_gcs/core/kalman_tracker.py) | The Kalman filter math — predicts future positions |
| [core/trajectory_predictor.py](file:///d:/Drone%20Projects/walle_gcs/core/trajectory_predictor.py) | Per-person state management — ghost tracking, history, velocity |
| [core/motion_classifier.py](file:///d:/Drone%20Projects/walle_gcs/core/motion_classifier.py) | Figures out if a person is walking, running, sitting, falling, etc. |
| [Eval_models/](file:///d:/Drone%20Projects/walle_gcs/Eval_models/) | Jupyter notebooks for comparing depth model performance |

---

## 💡 Key Terms Glossary (Super Simple)

| Term | Simple Meaning |
|---|---|
| **Bounding Box** | A rectangle drawn around a detected object |
| **Confidence Score** | How sure is the AI? 0% = not sure, 100% = very sure |
| **COCO** | A huge dataset of labeled images used to train detection models |
| **Depth Map** | An image where each pixel stores "how far is this from the camera" |
| **Kalman Filter** | A math tool that predicts where a moving object will go next |
| **IoU** | "Intersection over Union" — measures how much two boxes overlap (used to match objects across frames) |
| **DeepSort** | A tracking algorithm that uses appearance (what it looks like) + motion to keep IDs stable |
| **Ghost Tracking** | Keeps predicting where a person is even when they disappear from view |
| **Keypoints** | Specific body points (nose, shoulder, knee, etc.) detected by YOLOv8-pose |
| **Thread / Worker** | A separate process running in the background so the main display doesn't lag |
| **Latency** | The delay between the drone capturing a frame and the AI finishing processing it |
| **Warmup** | Running the model on a dummy image once at startup so it's fast when real frames arrive |
