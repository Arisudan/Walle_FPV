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
**Why it's a bottleneck**: When testing a laptop webcam or stationary camera on a desk, you mostly just rotate the camera (panning or tilting it).
In pure rotation, there is zero parallax (no relative depth changes). The math behind the Essential Matrix becomes degenerate, causing the tracker to fail, lose features ([MVO] Features dropped... Re-detecting), and drift along a single axis (which is why your path line only extended along the X-axis).
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
