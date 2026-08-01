# 31-07-2026
---
The **Intel RealSense D435i** is a highly versatile sensor suite. Because it contains **an RGB camera, two Infrared cameras (spaced for stereo), an IR projector, and a built-in IMU (inertial sensor)**, it can be run in any of the following modes for SLAM:

---

### 1. Monocular Mode (Mono)
* **What it uses:** Only one camera (either the RGB camera or the Left Infrared camera).
* **How it works:** SLAM tracks visual features from a single video stream. 
* **Limitation:** Since there is only one camera, it cannot measure absolute depth directly; it must estimate depth up to an unknown scale by moving the camera (called structure-from-motion).

### 2. Monocular-Inertial Mode (Mono-Inertial)
* **What it uses:** One camera (RGB or Left IR) + the IMU (gyroscope and accelerometer).
* **How it works:** The IMU provides high-frequency motion measurements (acceleration and rotation velocity) while the camera tracks visual features.
* **Advantage:** The IMU helps solve the scale ambiguity of monocular SLAM and keeps tracking stable during fast movements or temporary camera occlusion.

### 3. Stereo Mode
* **What it uses:** Both Infrared cameras (Left and Right IR).
* **How it works:** The two IR cameras capture images at the same time. By matching corresponding points between the left and right images (using the known distance between them), the system calculates true depth immediately for every pixel.
* **Advantage:** Computes true scale instantly without needing camera motion. It can run in the dark if you turn on the camera's built-in infrared projector.

### 4. Stereo-Inertial Mode
* **What it uses:** Both Infrared cameras (Left and Right IR) + the IMU.
* **How it works:** Fuses the stereo depth estimation with the high-frequency inertial tracking of the IMU.
* **Advantage:** This is typically the most robust and accurate mode for high-speed motion, drone flight, and complex environments because it has absolute scale (from the stereo pair) and continuous orientation tracking (from the IMU).

### 5. RGB-D Mode (Currently active in your workspace)
* **What it uses:** The RGB camera + the Depth map (calculated internally by the camera hardware using the IR stereo pair).
* **How it works:** The camera node aligns the depth image to the RGB image. The SLAM algorithm (like `ros2_rgbd.cpp` in your workspace) uses the RGB image to find visual landmarks and reads the depth values directly from the depth image.
* **Advantage:** Easier to process and visualize than raw stereo matching, as the depth computation is offloaded to the camera's onboard ASIC chip.

### 6. RGB-D-Inertial Mode
* **What it uses:** RGB camera + Depth map + IMU.
* **How it works:** Fuses the RGB-D visual tracking with IMU measurements.
* **Advantage:** Combines the convenience of pre-computed depth maps with the stability of IMU tracking.

---

### Relation to your Final SLAM Report:
Since you need to evaluate these modes with **live camera data** to produce results similar to the **EuRoC VIO SLAM ATE Results** template:
1. You can run ORB-SLAM3 in different configurations (e.g., Stereo, Stereo-Inertial, RGB-D) using the D435i.
2. We can record the trajectory outputs from these runs.
3. Compare the Absolute Trajectory Error (ATE) or drift for each mode to write your final comparative analysis report.

---
# 01-08-2026
---
Here is how the 6 SLAM modes map to your two recorded ROS Bags:

### 1. If you select the **RGB-D Bag** (Contains Color + Depth + IMU):
This bag is used to run the following **4 modes**:
* **RGB-D** (Requires Color + Depth)
* **RGB-D-Inertial** (Requires Color + Depth + IMU)
* **Monocular** (Requires only the Color stream, which is inside this bag)
* **Monocular-Inertial** (Requires Color + IMU, both inside this bag)

---

### 2. If you select the **Stereo Bag** (Contains Left IR + Right IR + Color + IMU):
This bag is used to run the following **4 modes**:
* **Stereo** (Requires Left IR + Right IR)
* **Stereo-Inertial** (Requires Left IR + Right IR + IMU)
* **Monocular** (Requires only the Color stream, which is inside this bag)
* **Monocular-Inertial** (Requires Color + IMU, both inside this bag)

---

### Summary Table for Benchmarking:

| SLAM Mode | Use RGB-D Bag? | Use Stereo Bag? |
| :--- | :---: | :---: |
| **Monocular** | **Yes** | **Yes** |
| **Monocular-Inertial** | **Yes** | **Yes** |
| **Stereo** | No | **Yes** |
| **Stereo-Inertial** | No | **Yes** |
| **RGB-D** | **Yes** | No |
| **RGB-D-Inertial** | **Yes** | No |

---
