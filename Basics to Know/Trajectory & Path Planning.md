# 🛣️ Path & Trajectory Planning

> **Path Planning determines *where* the drone should fly, while Trajectory Planning determines *how and when* it should fly.**

---

## 📊 Quick Comparison

| Path Planning | Trajectory Planning |
|---------------|---------------------|
| Plans the route | Plans the motion |
| Focuses on **Where?** | Focuses on **How & When?** |
| Spatial planning | Time-based planning |
| Generates waypoints | Generates smooth motion |

---

## 🚁 Overall Workflow

```mermaid
flowchart LR

A[Start Point]
--> B[Path Planning]
--> C[Waypoints]
--> D[Trajectory Planning]
--> E[Smooth Trajectory]
--> F[Flight Controller]
--> G[Drone Flight]
```

---

# 📍 Step 1: Path Planning

> Finds a **collision-free path** from the start to the destination.

### 🎯 Goal

- Generate safe route
- Avoid obstacles
- Produce waypoints

### 🧩 Common Algorithms

| Algorithm | Description |
|-----------|-------------|
| **A\*** | Finds the shortest path using graph search |
| **Dijkstra** | Computes the shortest path to all nodes |
| **RRT** | Randomly explores the environment to find a feasible path |
| **PRM** | Builds a roadmap of valid paths using random samples |

---

# ⏱️ Step 2: Trajectory Planning

> Converts waypoints into a **smooth, time-based flight path**.

### 🎯 Goal

- Assign timing
- Control speed
- Ensure smooth motion

### Considers

- Velocity
- Acceleration
- Jerk
- Drone dynamics

### 🧩 Common Methods

| Method | Purpose |
|---------|---------|
| **Polynomial Curves** | Smooth path generation |
| **B-Splines** | Continuous trajectory generation |

---

# 🎮 Step 3: Trajectory Tracking

> The flight controller follows the planned trajectory.

### Control Methods

| Controller | Function |
|------------|----------|
| **PID** | Corrects deviations using feedback |
| **MPC** | Predicts future motion and optimizes control |

---

## 📌 Key Points

- **Path Planning** → *Where should the drone fly?*
- **Trajectory Planning** → *How and when should the drone fly?*
- **Trajectory Tracking** → *Keeps the drone on the planned trajectory.*
- A complete autonomous flight consists of **Path Planning → Trajectory Planning → Tracking**.
