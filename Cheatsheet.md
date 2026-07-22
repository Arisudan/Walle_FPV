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
## . Ground Truth
Ground Truth refers to the verified, **accurate data** used as a *benchmark to train* and *evaluate algorithms*. 

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
