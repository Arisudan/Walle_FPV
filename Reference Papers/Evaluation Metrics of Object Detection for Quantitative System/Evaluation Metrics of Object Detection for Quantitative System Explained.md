# Evaluation Metrics of Object Detection for Quantitative System-Level Analysis of Safety-Critical Autonomous Systems

---

# Abstract

This paper proposes **system-aware evaluation metrics** for object detection in autonomous vehicles. Traditional metrics such as Precision, Recall, and mAP evaluate the detector independently but fail to capture how detection errors affect the **overall safety of the autonomous system**.

To address this, the authors introduce two novel evaluation methods:

1. **Distance-Parameterized Confusion Matrix**
2. **Proposition-Labeled Confusion Matrix**

These metrics are integrated into a **Markov Chain** framework and evaluated using **Probabilistic Model Checking** to estimate the probability that an autonomous vehicle satisfies its safety specifications.

Unlike conventional object detection benchmarks, this work directly links perception performance with system-level safety.

---

# Analogy

> Imagine a self-driving car approaching a pedestrian crossing.
>
> Traditional evaluation counts **how many pedestrians are detected correctly**.
>
> This paper instead asks:
>
> **"Will the car still stop safely even if one pedestrian is missed?"**
>
> Instead of evaluating only the detector, the paper evaluates the **entire autonomous driving decision pipeline**.

---

> [!IMPORTANT]
> ## Paper Takeaways
>
> - Introduces **distance-aware object detection evaluation**.
> - Introduces **proposition-based confusion matrices** instead of object-class confusion matrices.
> - Integrates perception evaluation with **formal verification**.
> - Uses **Markov Chains** and **Linear Temporal Logic (LTL)**.
> - Demonstrates that traditional metrics are often overly conservative for system-level safety analysis.
> - Shows improved quantitative safety estimation using the proposed metrics.

---

# Core Concepts – The Glossary

| Term | Simple Definition | Why it Matters |
|------|-------------------|----------------|
| Object Detection | Detecting and classifying objects | Primary perception task |
| Confusion Matrix | Counts correct and incorrect predictions | Basis of evaluation metrics |
| Distance Parameterization | Evaluating detection accuracy at different distances | Detection quality changes with distance |
| Proposition-Labeled Confusion Matrix | Uses logical propositions instead of object classes | Better represents decision-making logic |
| Atomic Proposition | A logical statement that is either true or false | Used in temporal logic |
| Temporal Logic (LTL) | Logic for specifying system behaviors over time | Defines autonomous vehicle safety rules |
| Markov Chain | Probabilistic state-transition model | Computes safety probabilities |
| Probabilistic Model Checking | Mathematical verification technique | Quantifies probability of satisfying safety requirements |
| nuScenes | Autonomous driving dataset | Experimental benchmark |
| YOLOv3 | Object detector evaluated in the paper | Used to validate the proposed framework |

---

# How it Works

## Step 1 — Collect Dataset

The authors evaluate a pretrained **YOLOv3** detector using the **nuScenes** autonomous driving dataset.

The dataset contains:

- Camera images
- LiDAR information
- Object annotations
- Object distances
- Multiple traffic scenarios

---

## Step 2 — Run Object Detection

YOLOv3 predicts

- Bounding Boxes
- Object Classes

for every frame.

---

## Step 3 — Build Traditional Confusion Matrix

Normally,

```
Ground Truth
↓

Prediction

↓

Confusion Matrix
```

is generated.

However...

The paper argues that this ignores

- object distance
- control decisions
- safety specifications

---

## Step 4 — Build Distance-Parameterized Confusion Matrix

Instead of evaluating all detections equally,

objects are grouped into distance bins such as

- 0–10 m
- 10–20 m
- 20–30 m
- ...
- 90–100 m

Each distance interval gets its **own confusion matrix**.

This captures the fact that nearby objects are more critical than distant ones.

---

## Step 5 — Build Proposition-Labeled Confusion Matrix

Traditional confusion matrix

```
Pedestrian
Vehicle
Cyclist
Traffic Light
...
```

becomes

```
"There exists a pedestrian"

"There exists an obstacle"

"There exists a vehicle"
```

Instead of counting individual objects,

the framework evaluates whether the information required by the controller is correctly perceived.

Example

Traditional evaluation

```
3 pedestrians detected

vs

4 pedestrians detected
```

Controller decision

```
Stop vehicle
```

Both situations produce the same safe behavior.

Therefore,

counting every missed pedestrian may exaggerate system risk.

---

## Step 6 — Construct Markov Chain

The confusion matrices are converted into

Transition Probabilities

which become

```text
Current State
        │
        ▼
 Detection Outcome
        │
        ▼
 Controller Decision
        │
        ▼
 Next Vehicle State
```

The complete autonomous system is modeled as a

**Discrete-Time Markov Chain**

where transition probabilities come directly from the proposed confusion matrices.

---

## Step 7 — Perform Probabilistic Model Checking

The generated Markov Chain is analyzed using

- Storm Model Checker

to compute

```
P(System satisfies Safety Specification)
```

rather than simply reporting detection accuracy.

---

## Overall Pipeline

```mermaid
flowchart LR

A[nuScenes Dataset]
-->|"Images + LiDAR + Labels"| B[YOLOv3 Detector]

B
-->|"Bounding Boxes"| C[Distance Binning]

C
-->|"0-10m, 10-20m, ... "| D[Distance-Parameterized Confusion Matrix]

D
-->|"Logical Propositions"| E[Proposition-Labeled Confusion Matrix]

E
-->|"Transition Probabilities"| F[Markov Chain Construction]

F
-->|"LTL Specifications"| G[Probabilistic Model Checking]

G
-->|"Safety Satisfaction Probability"| H[System-Level Evaluation]
```

> [!IMPORTANT]
>
> **Core Innovation**
>
> The key innovation is replacing traditional object detection evaluation with **system-aware evaluation metrics** that measure how perception errors influence the probability of satisfying formal safety requirements, rather than only measuring classification accuracy.

---

# Technical Architecture

## Major Components

- nuScenes Dataset
- YOLOv3 Detector
- Distance Binning Module
- Distance-Parameterized Confusion Matrix
- Proposition-Labeled Confusion Matrix
- Transition Probability Generator
- Markov Chain Constructor
- Linear Temporal Logic (LTL) Specification
- Storm Probabilistic Model Checker
- System-Level Safety Analyzer

---

## Architecture Table

| Module | Input | Operation | Output | Tensor / Data Shape |
|---------|-------|-----------|----------------|----------------|
| nuScenes Dataset | Camera + LiDAR | Data Collection | Annotated Frames | Image + Labels |
| YOLOv3 | RGB Image | Object Detection | Bounding Boxes + Classes | N × (Box + Class) |
| Distance Binning | Bounding Boxes | Group by Distance | Distance Buckets | Variable |
| Distance Confusion Matrix | Predictions | Compute Distance-wise Errors | Confusion Matrices | K × C × C |
| Proposition Generator | Object Labels | Convert to Logical Propositions | Proposition Sets | 2^P |
| Transition Generator | Confusion Matrix | Compute Transition Probabilities | Probability Matrix | S × S |
| Markov Chain | Transition Matrix | State Modeling | DTMC | Graph |
| LTL Module | Safety Rules | Formal Specification | Temporal Formula | Logical Expression |
| Storm Model Checker | DTMC + LTL | Probabilistic Verification | Satisfaction Probability | Scalar |

---

# Summary of Experimental Results

The authors evaluated a **pre-trained YOLOv3** object detector on the **nuScenes** autonomous driving dataset to compare the effectiveness of traditional confusion matrices against the proposed **distance-parameterized** and **proposition-labeled** confusion matrices. Rather than introducing a new detector, the focus was on demonstrating how different evaluation metrics influence **system-level safety verification**.

---

## Experimental Setup

| Parameter | Value |
|-----------|-------|
| Object Detector | YOLOv3 |
| Training Dataset | MS COCO |
| Evaluation Dataset | nuScenes |
| Sensor Used | Front Camera (CAM-FRONT) |
| Dataset Frames | First 85 scenes (20 seconds each) |
| Annotation Frequency | 2 Hz |
| Object Categories | Pedestrian (ped), Obstacle (obs), Empty (emp) |
| Distance Intervals | Every 10 m (0–100 m) |
| Verification Tool | Storm Probabilistic Model Checker |

---

## Evaluation Framework

The paper compares four different evaluation approaches.

| Evaluation Method | Description | Safety Awareness |
|-------------------|-------------|------------------|
| Traditional Class-Labeled Confusion Matrix | Standard object classification errors | ❌ Low |
| Distance-Parameterized Class Confusion Matrix | Considers object distance | ✅ Medium |
| Proposition-Labeled Confusion Matrix | Uses logical propositions instead of classes | ✅ High |
| Distance + Proposition-Labeled Matrix | Combines both approaches | ⭐ Highest |

---

## Example Confusion Matrix (Objects Within 10 m)

### Traditional Class-Labeled Matrix

| Predicted | Pedestrian | Obstacle | Empty |
|-----------|-----------:|---------:|------:|
| Pedestrian | 31 | 0 | 0 |
| Obstacle | 0 | 165 | 0 |
| Empty | 121 | 665 | 2722 |

This matrix evaluates every object independently, regardless of its impact on vehicle behavior. :contentReference[oaicite:0]{index=0}

---

### Proposition-Labeled Matrix

| Predicted | Pedestrian | Obstacle | Pedestrian + Obstacle | Empty |
|-----------|-----------:|---------:|----------------------:|------:|
| Pedestrian | 22 | 0 | 5 | 0 |
| Obstacle | 0 | 158 | 4 | 0 |
| Pedestrian + Obstacle | 0 | 0 | 0 | 0 |
| Empty | 59 | 310 | 11 | 2722 |

Instead of evaluating each object individually, the matrix evaluates whether the **required environmental condition** was correctly perceived by the controller. :contentReference[oaicite:1]{index=1}

---

## Key Findings

| Observation | Result |
|-------------|--------|
| Distance-aware evaluation | Higher system-level safety probability |
| Proposition-based evaluation | Less conservative than traditional metrics |
| Traditional confusion matrix | Overestimates safety risk |
| Proposed framework | Better reflects controller behavior |
| Higher vehicle speed | Lower probability of satisfying safety requirements |

---

## Safety Analysis

The Markov Chain generated from the proposed confusion matrices was verified using **Linear Temporal Logic (LTL)** specifications.

The probability computed was:

```text
P(System satisfies Safety Specification)
```

instead of simply measuring

```text
Detection Accuracy
```

This directly links perception quality with autonomous vehicle safety. :contentReference[oaicite:2]{index=2}

---

## Main Observations

### Traditional Evaluation

- Counts every missed object equally.
- Ignores controller behavior.
- Ignores object distance.
- Produces conservative safety estimates.

---

### Proposed Evaluation

- Considers object distance.
- Considers logical safety conditions.
- Evaluates controller decisions.
- Produces more realistic safety estimates.

---

## Performance Comparison

| Criterion | Traditional Metrics | Proposed Metrics |
|-----------|---------------------|------------------|
| Object-Level Accuracy | ✅ | ✅ |
| Distance Awareness | ❌ | ✅ |
| Decision Awareness | ❌ | ✅ |
| Temporal Logic Support | ❌ | ✅ |
| Markov Chain Integration | ❌ | ✅ |
| System-Level Safety Evaluation | ❌ | ✅ |

---

> [!TIP]
>
> ## The Bottom Line
>
> The proposed **distance-parameterized** and **proposition-labeled confusion matrices** provide a more meaningful assessment of autonomous driving safety by evaluating how perception errors affect **vehicle decisions**, not just detection accuracy.

---

# Why This Matters (Impact Analysis)

Traditional object detection metrics such as **Precision**, **Recall**, and **mAP** evaluate perception independently. However, autonomous vehicles make decisions based on **high-level environmental understanding**, not merely on object counts.

This paper bridges the gap between **computer vision** and **formal verification**, enabling engineers to quantify the probability that an autonomous system will satisfy its safety requirements.

### Real-World Applications

- Autonomous Vehicles
- Mobile Robots
- Warehouse Automation
- Delivery Robots
- Industrial Inspection
- Safety-Critical AI Systems

---

## Actionable First Project

Implement a **distance-aware evaluation pipeline** for a YOLO-based detector:

1. Train YOLOv11 on the nuScenes dataset.
2. Divide detections into 10 m distance intervals.
3. Generate distance-parameterized confusion matrices.
4. Construct a Markov Chain from the confusion matrices.
5. Verify safety properties using a probabilistic model checker (e.g., Storm).

---

# Learning Path – How to Replicate

## Module 1 – Object Detection Fundamentals

- CNNs
- YOLO Architecture
- Precision
- Recall
- mAP
- IoU
- Confusion Matrix

---

## Module 2 – Formal Verification

- Finite State Machines
- Markov Chains
- Transition Probability
- Linear Temporal Logic (LTL)
- Probabilistic Model Checking
- Storm Model Checker

---

## Module 3 – Safety-Critical Autonomous Systems

- Autonomous Driving Stack
- Decision Making
- Motion Planning
- Sensor Fusion
- Safety Specifications
- Closed-Loop Verification

---

> [!WARNING]
>
> ## Limitations
>
> - Evaluates only a **pre-trained YOLOv3** model.
> - Uses a detector trained on **MS COCO**, not specifically optimized for **nuScenes**.
> - Assumes a **static environment** and **perfect object localization**.
> - Does not model object tracking across multiple frames.
> - Focuses on high-level decision logic rather than low-level vehicle dynamics.
> - Weather, sensor failures, and dynamic uncertainties are not extensively analyzed.
> - Future work includes multi-modal perception, belief updates for partially observable environments, and hardware validation. :contentReference[oaicite:3]{index=3}

---

# Quick Reference – Key Terms

| Acronym | Meaning |
|----------|---------|
| AP | Atomic Proposition |
| LTL | Linear Temporal Logic |
| DTMC | Discrete-Time Markov Chain |
| CM | Confusion Matrix |
| TPR | True Positive Rate |
| FPR | False Positive Rate |
| mAP | Mean Average Precision |
| IoU | Intersection over Union |
| YOLO | You Only Look Once |
| COCO | Common Objects in Context |
| nuScenes | Multimodal Autonomous Driving Dataset |
| RSS | Responsibility-Sensitive Safety |

---

# Final Conclusion

The paper redefines how object detection should be evaluated for **safety-critical autonomous systems**. Instead of asking **"How accurate is the detector?"**, it asks **"How likely is the autonomous system to behave safely given the detector's performance?"**

By introducing **distance-parameterized** and **proposition-labeled confusion matrices**, and integrating them into **Markov Chain-based probabilistic verification**, the authors establish a direct connection between perception quality and system-level safety. This framework provides a more realistic and actionable assessment of autonomous driving performance than conventional object detection metrics.

---

<div align="center">

![Prepared by Arisudan](https://img.shields.io/badge/Prepared%20by-Arisudan-blue?style=for-the-badge)

</div>
