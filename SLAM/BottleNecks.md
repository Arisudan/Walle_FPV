# This is small module of 2D Map Sparsing using a Monocular Camera:

----
# As on 22-07-2026, I faced the following bottle necks which i have listed them below:
----
## Bottleneck 1: CPU Processing Speed (The Frame Rate Bottleneck)

**The Issue**: The Depth Anything V2 neural network is currently running on your CPU ([DepthEstimator] Using device: cpu).

**Why it's a bottleneck**: 

* Even though we are using the lightweight "Small" version of the model, running a deep learning vision transformer on a CPU takes between 300ms to 1000ms per frame. 
* This limits your SLAM pipeline to about 1 to 3 FPS (Frames Per Second).

**Impact on Drones**: For safe drone navigation or real-time obstacle avoidance, a SLAM system must run at least at 15 to 20 FPS.

**The Solution**: You need a CUDA-capable GPU. Running the PyTorch depth model on an Nvidia GPU (via CUDA) drops the inference time to under 30ms, allowing the SLAM dashboard to run at a smooth 30 FPS.

----
## Bottleneck 2: Pure Rotation Degeneracy (The Stationary Desk Test Bottleneck)
**The Issue**: Monocular visual tracking mathematically requires translation (moving physically through space left/right, up/down, forward/backward) to calculate depth and perspective.

**Why it's a bottleneck**: 

* When testing a laptop webcam or stationary camera on a desk, you mostly just rotate the camera (panning or tilting it).
* In pure rotation, there is zero parallax (no relative depth changes). The math behind the Essential Matrix becomes degenerate, causing the tracker to fail, lose features ([MVO] Features dropped... Re-detecting), and drift along a single axis (which is why your path line only extended along the X-axis).

**Impact**: Monocular SLAM cannot be tested successfully while sitting stationary; it requires the camera to physically travel through space.

----
## Bottleneck 3: Accumulating Trajectory Drift (The Loop Closure Bottleneck)

**The Issue**: Our Python Visual Odometry tracks pose incrementally from frame to frame.

**Why it's a bottleneck**: 
* Every frame, a tiny calculation error is introduced. 
* Because our lightweight system is frame-to-frame and does not have Bundle Adjustment or Loop Closure (unlike heavy C++ ORB-SLAM3 or ROS RTAB-Map), these errors add up over time (drift).

> If your drone flies a 50-meter loop and returns to the exact starting point, the SLAM path might show it is 2 meters away from the start because there is no optimization module to detect the "loop" and snap the path back into place.

----
## Bottleneck 4: Monocular Scale Ambiguity (The Real-World Metric Bottleneck)

**The Issue**: A single camera has no physical baseline (like a stereo camera) to know if a wall is 1 meter away or 10 meters away.

**Why it's a bottleneck**:

* We solved this by scaling the depth map so that the average indoor depth is assumed to be 2.0 meters (median_depth = 2.0). 
* While this works great for testing in a single room, if you move the camera closer to a wall or step into a massive warehouse, the scale factor will become incorrect, causing the metric path drawing to shrink or stretch.

---
# As on 23-07-2026 I did the following process:

I cloned the mast3r SLAM Repository [Link to access that Repo - Click Me!](https://github.com/naver/mast3r) from the internet and planning to integrate them with the existing workflow in the lingbot-map folder.

At the end of the day I placed mast3r SLAM Repository and tried to integrate them to the existing files of the lingbot map file.

Also i have mapped the list of work done for the day and that files can be accessed by &&rarr; [Open Document 1](Phase1.txt) and [Open Document 2](Phase2.txt)


---
# As on 24-07-2026 I faced the following errors:

### This below error occured because of the mismatch in the versions and the python getting a bigger conflict with the C++

### This current folder is fully handled with python and bringing in SLAM imported to run here in this project folder causes conflicts and gives out major errors while compiling the files.

### For initilaizing the SLAM into my folder I cloned the repository from the mast3r SLAM Repository [Link to access that Repo - Click Me!](https://github.com/naver/mast3r)


**Error**:
  
>  (venv) ndrone@pop-os:~/Desktop/SLAM$ python scripts/mast3r_server.py
Traceback (most recent call last):
  File "/home/ndrone/Desktop/SLAM/scripts/mast3r_server.py", line 18, in <module>
    from mast3r_slam.frame import Mode, SharedKeyframes, SharedStates, create_frame
  File "/home/ndrone/Desktop/SLAM/MASt3R-SLAM/mast3r_slam/frame.py", line 6, in <module>
    from mast3r_slam.mast3r_utils import resize_img
  File "/home/ndrone/Desktop/SLAM/MASt3R-SLAM/mast3r_slam/mast3r_utils.py", line 11, in <module>
    import mast3r_slam.matching as matching
  File "/home/ndrone/Desktop/SLAM/MASt3R-SLAM/mast3r_slam/matching.py", line 5, in <module>
    import mast3r_slam_backends
ImportError: /home/ndrone/Desktop/SLAM/MASt3R-SLAM/mast3r_slam_backends.cpython-312-x86_64-linux-gnu.so: undefined symbol: _Z19refine_matches_cudaN2at6TensorES0_S0_ii

---
# As on 27-07-2026 I faced the following bottleneck:

Here are the 5 bottlenecks we faced:

1. **O(N²) CPU Rendering:** The Python server recalculated the entire history of the map from scratch on every single frame, causing the CPU to choke and freeze as the video got longer.
2. **Network Request Spam:** The React frontend blindly spammed the server with 30 image requests a second without waiting for them to download, creating a massive traffic jam that broke the video sync.
3. **Z-Axis Floor Noise:** Flattening 3D point clouds directly to 2D caused floor and ceiling points to render as solid blocks, completely covering up the actual walls.
4. **Visual UI Flickering:** The React dashboard was forcefully bypassing the browser cache for every frame, causing the map to violently flicker and ghost instead of streaming smoothly.
5. **Desynchronized Codebases:** The custom FastAPI backend and the default Viser 3D dashboard were running two separate, buggy copies of the 2D mapping code, requiring double the work to fix bugs.

### Solution:
Here are the exact solutions we applied to solve those bottlenecks, in short:

1. **O(1) Incremental Caching:** Instead of recalculating history, we forced the Python server to save the map in memory and only calculate the single newest frame (the delta) on top of it. This dropped rendering time from seconds to milliseconds.
2. **Smart Sync Debouncing:** We added a strict lock (`isImageLoading`) to the React frontend. The timeline now refuses to advance until the previous map image has successfully downloaded, eliminating network traffic jams.
3. **1-Meter Height Band Filter:** We added mathematical logic to strictly delete any 3D point cloud data that is higher or lower than camera level, instantly isolating the walls and removing floor noise.
4. **Smooth Image Hot-Swapping:** We removed the forceful cache-busting timestamp (`?t=...`) from the React code, allowing the browser to smoothly hot-swap the map frames in-place without any flickering.
5. **Unified Rendering Logic:** We manually synchronized the logic inside both `server.py`/`point_cloud_viewer.py` and `viser_2d_wrapper.py` so that both dashboards run the exact same optimized, bug-free mapping code.

