# Cheatsheet for Reference:
---
## 1. Multiple-Object Tracking Accuracy (MOTA):

  * A Tracking parameter that *assesses* **an object-tracking system’s overall accuracy**
### What it measures: 
Instead of just looking at isolated images, MOTA evaluates *how accurately* a tracker maintains the **identities**, **locations**, and **trajectories** of *multiple objects* 
(e.g., pedestrians in a self-driving car feed) over a sequence of frames.


<img width="370" height="82" alt="image" src="https://github.com/user-attachments/assets/049935ab-7e98-4ac3-b9f8-9e100b152511" />

where,
 * FN &rarr; False Negative
 * FP &rarr; False Positive
 * IDsw &rarr; Number of Identity Switches
 * GT &rarr; Number of Ground Truth Objects

----
**False Positives (FP)**: When the algorithm detects an object that isn't actually there.

**False Negatives (FN / Misses)**: When the algorithm fails to detect an object that is in the ground truth data.

**Identity Switches (IDSW)**: When the algorithm incorrectly swaps the ID of an object (e.g., confusing "Person A" with "Person B" during a crossover).

----

#### MOTA Score = **~1.0** (or 100%) &rarr; Highly Accurate Tracking System

---
## 2. Multiple-Object Tracking Precision (MOTP):

 * An evaluation metric that measures **how accurately a tracking algorithm localizes *multiple moving objects*** in a *video sequence*.


<img width="370" height="82" alt="image" src="https://github.com/user-attachments/assets/e3613a7f-dec9-493c-9d1d-cb7bc8447896" />

Where,
FA &rarr; Number of False Alarms 
TP &rarr; Number of True Positives
FP 7arr; False Positive

MOTP calculates the **average distance** or **bounding box mismatch** between your ***model's predicted object locations*** and the ***actual ground-truth objects***.

**Calculation**: Uses the ***Intersection-over-Union (IoU)*** metric or ***Pixel-level Euclidean distance*** for correctly matched pairs.

---
### MOTP Score &rarr; Lower Error (&darr;)/Higher IOU (&uarr;) &rarr; Highly Accurate Precision System
---
## MOTA vs MOTP:
| Metric | Full Name | Primary Focus | Issues Tracked | Ignored Issues |
| :--- | :--- | :--- | :--- | :--- |
| **MOTA** | Multiple Object Tracking Accuracy | Tracking failures | • False Positives<br>• False Negatives<br>• Identity Switches | • Exact geometric box accuracy |
| **MOTP** | Multiple Object Tracking Precision | Geometric accuracy | • Bounding box overlap (IoU)<br>• Pixel-level distance | • Identity switches<br>• Lost targets |

---
## 3. Identity Switch (IDsw/Mismatch):
 * An Identity Switch occurs when a **tracking algorithm** *incorrectly assigns* a ***different tracking ID*** to the *exact same physical object* ***over time***.
 
<img width="370" height="95" alt="image" src="https://github.com/user-attachments/assets/316abbea-7541-45b0-9f29-ff7e34c6e72e" />

Where,
* IDsw_rate &rarr; Rate of Identity Switches
* GT &rarr; ground truth

---
### Key Causes
* **Occlusions**: Objects temporarily blocking each other or being blocked by the environment.
* **Crowded Scenes**: Visually similar objects moving closely together.
* **Detection Failures**: The detector missing the object for a few frames, causing the tracker to treat it as a brand-new object when it reappears.

---
### Impact on Metrics
* **MOTA**: ID switches directly lower the MOTA score, as the metric penalizes every occurrence of a changed.
* **ID.IDF1**: This metric heavily punishes ID switches because it measures how consistently the system maintains correct IDs across the entire video.
* **MOTP**: ID switches do not affect the MOTP score, as MOTP only measures the physical overlapping accuracy of whatever box is currently predicted.

---
## 4. Precision:
* Measures the proportion of true positive predictions among all positive predictions made by a model.
* It quantifies how often the model is correct when it predicts a positive outcome.


<img width="245" height="95" alt="image" src="https://github.com/user-attachments/assets/8789911d-1e01-4929-91c5-5a3cced8d737" />

Where,
* TP &rarr; Number of correctly predicted Positive Instances
* FP &rarr; Number of incorrectly predicted Positive Instances.

---
## 5. Recall:
* Measures the proportion of true positive predictions among all actual positive instances in the dataset.
* It quantifies how well the model identifies all positive cases.

<img width="222" height="71" alt="image" src="https://github.com/user-attachments/assets/9a62af6d-58ce-44ed-85aa-a336d184dbae" />

Where,
* FN &rarr; Number of incorrectly predicted Negative Instances.

---
## . Ground Truth
 * Ground Truth refers to the verified, **accurate data** used as a *benchmark to train* and *evaluate algorithms*. 

It acts as the "answer key," typically created through *human annotation* or *domain expertise*, and represents exactly what an algorithm should output.

### Common forms of ground truth in image processing include:
* **Classification Labels**
* **Bounding Boxes**
* **Segmentation Masks** 
---
## . Classification Labels:
  * Predefined tags that identify the main subject of an image 
  * Example: categorizing an image as a "cat" or "dog".
---
## . Bounding Boxes:
  * Coordinates that pinpoint the exact location and extent of objects of interest.
  * These are essential for tasks like object detection in autonomous driving or medical imaging.
---
## . Segmentation Masks:
  *  Pixel-level maps that trace the exact boundaries of objects,
  *  Classifying every individual pixel as either part of the object or the background.
---
## . 
