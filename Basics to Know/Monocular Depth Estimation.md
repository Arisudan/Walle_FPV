````md
# 📷 Monocular Depth Estimation (MDE)

> **Monocular Depth Estimation (MDE) is a computer vision technique that estimates the depth (distance) of objects from a single RGB image.**

---

## 🎯 Purpose

- Estimate object distance using a single camera
- Generate a depth map of the scene
- Enable depth perception without specialized sensors

---

## 🔄 How MDE Works

```mermaid
flowchart LR

A[RGB Image] --> B[AI Model]

B --> C[Analyze Visual Cues]

C --> D[Generate Depth Map]

D --> E[Estimate Object Distance]
```

---

## 🧠 Visual Cues Used

The AI model estimates depth using visual information such as:

- Perspective
- Object Size
- Shading
- Texture
- Occlusion

---

## 🗺️ Depth Map

A **Depth Map** is the output of an MDE model.

- Each pixel represents the estimated distance from the camera.
- Depth is typically visualized using grayscale or color coding.

| Color / Intensity | Meaning |
|-------------------|---------|
| Dark | Near |
| Bright | Far |

> 📌 *(Visualization may vary depending on the model or color map used.)*

---

## 🚁 Why Use MDE in Drones?

| Benefit | Description |
|----------|-------------|
| **Lightweight** | Uses only a single RGB camera |
| **Low Power** | No additional depth sensors required |
| **Cost Effective** | Eliminates expensive LiDAR or stereo cameras |
| **Real-Time** | Suitable for onboard AI inference |

---

## 🎯 Applications

- Obstacle Avoidance
- Autonomous Navigation
- Terrain Mapping
- GPS-Denied Navigation

---

## 🧩 Modern MDE Approaches

| Approach | Description |
|----------|-------------|
| **Zero-Shot Metric Depth** | Estimates actual distances (e.g., meters) without task-specific training |
| **Self-Supervised Learning** | Trains using unlabeled video sequences instead of ground-truth depth |
| **Semantic-Aware Models** | Prioritize important foreground objects for improved depth estimation |

---

## 🌟 Example: Depth Anything V2

**Depth Anything V2** is a modern MDE model that:

- Produces dense depth maps
- Supports metric depth estimation (with appropriate variants)
- Emphasizes foreground objects
- Delivers fast and accurate inference suitable for real-time applications

---

## 📌 Key Points

- MDE estimates **3D depth from a single 2D RGB image**.
- It does **not** require LiDAR or stereo cameras.
- The output is a **depth map**, where each pixel represents an estimated distance.
- Ideal for lightweight UAVs due to its low hardware requirements.
- Widely used for obstacle avoidance and autonomous navigation.
````
