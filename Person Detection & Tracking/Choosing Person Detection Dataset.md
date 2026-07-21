# Dataset Distribution for Person Detection

For your project (real-time indoor UAV person detection), each dataset serves a different purpose. The percentages I suggested are not arbitrary—they are intended to balance generalization, indoor adaptation, and domain-specific learning.

---

## 1. CrowdHuman (45%)

### Overview

CrowdHuman is one of the best human detection datasets ever created. It is specifically designed for detecting humans under challenging conditions such as crowds, heavy occlusion, and varying poses.

| Property | Details |
|---|---|
| Purpose | Human Detection |
| Images | 15,000 Train + 4,370 Validation + 5,000 Test |
| Human Boxes | ~470,000 |
| Annotation Types | Full Body, Visible Body, Head |
| Camera | Ground-level |
| Indoor | Partial |
| Outdoor | Yes |
| License | Research Use |

### Why it is valuable

Unlike COCO, where "person" is just one of 80 classes, CrowdHuman focuses entirely on people.

It teaches your model:

- Standing people
- Sitting people
- Walking people
- Running people
- People behind tables
- Partial humans
- Groups of people
- Different clothing
- Different body sizes

These features are critical for indoor UAV navigation.

### Advantages

| ✅ Advantages |
|---|
| Best human annotations |
| Heavy occlusions |
| Multiple bounding boxes per person |
| Large dataset |
| Widely used in state-of-the-art person detectors |

### Disadvantages

| ❌ Disadvantages |
|---|
| Camera viewpoint differs from FPV drones |
| Limited drone perspective |

### Why 45%?

This dataset forms the foundation of your detector. It teaches the model what a person looks like before adapting to the drone's viewpoint.

**Recommendation: ⭐⭐⭐⭐⭐ (Essential)**

---

## 2. COCO (Person Only) (25%)

### Overview

COCO is the most widely used object detection benchmark.

For your project, you should keep only the Person class.

| Property | Details |
|---|---|
| Images | ~118K Train |
| Classes | 80 |
| Person Images | Tens of thousands |
| Indoor | Yes |
| Outdoor | Yes |
| Annotation | Bounding Boxes, Segmentation, Keypoints |

### Why it is useful

COCO provides diversity:

- Homes
- Offices
- Kitchens
- Bedrooms
- Laboratories
- Hallways
- Shopping areas
- Different lighting
- Children
- Adults
- Elderly people

It prevents overfitting to CrowdHuman.

### Advantages

| ✅ Advantages |
|---|
| Huge variation |
| Indoor scenes |
| Everyday environments |
| Different camera types |

### Disadvantages

| ❌ Disadvantages |
|---|
| Not specialized for humans |
| Fewer occluded people |

### Why 25%?

It improves generalization while keeping the focus on person detection.

**Recommendation: ⭐⭐⭐⭐☆**

---

## 3. IndoorCrowd (15%)

### Overview

IndoorCrowd is a relatively new dataset focused specifically on indoor human detection, segmentation, and tracking.

Typical scenes include:

- Offices
- Universities
- Shopping malls
- Corridors
- Indoor public spaces

### Why it matters

This dataset resembles your deployment environment much more closely than CrowdHuman or COCO.

It teaches:

- Indoor lighting
- Indoor backgrounds
- Hallways
- Ceiling lights
- Walls
- Furniture
- Narrow spaces

### Advantages

| ✅ Advantages |
|---|
| Indoor only |
| Multiple indoor scenes |
| Modern annotations |

### Disadvantages

| ❌ Disadvantages |
|---|
| Smaller dataset |
| Less community adoption |

### Why only 15%?

IndoorCrowd is excellent, but it is not large enough to be the primary training dataset. It is best used for domain adaptation after the model has learned general person features.

**Recommendation: ⭐⭐⭐⭐⭐ (Excellent for indoor adaptation)**

---

## 4. Brainwash (5%)

### Overview

Brainwash is an indoor surveillance dataset collected in shopping malls.

| Property | Details |
|---|---|
| Environment | Indoor Shopping Mall |
| Camera | Ceiling-mounted |
| Focus | Human Detection |
| Occlusions | High |

### Why use it?

Although the camera angle differs from your FPV drone, it provides valuable examples of:

- Dense crowds
- Partial occlusions
- Indoor lighting
- Busy scenes

### Advantages

| ✅ Advantages |
|---|
| Indoor |
| Challenging occlusions |

### Disadvantages

| ❌ Disadvantages |
|---|
| Older dataset |
| Limited environment diversity |
| Surveillance viewpoint |

### Why only 5%?

It is used as a supplementary dataset to improve robustness to crowded indoor scenes without dominating the training distribution.

**Recommendation: ⭐⭐⭐☆☆**

---

## 5. PersonPath22 (Indoor Subset) (5%)

### Overview

PersonPath22 is designed for long-term person tracking across videos.

It contains:

- Indoor sequences
- Outdoor sequences
- Temporal annotations
- Person identities

### Why include it?

Even though your current task is detection, your long-term goal is person tracking on an autonomous UAV.

Training with selected indoor frames helps the detector handle:

- Motion blur
- Consecutive frames
- People entering/exiting the field of view
- Temporary occlusions

### Advantages

| ✅ Advantages |
|---|
| Video sequences |
| Temporal consistency |
| Motion information |

### Disadvantages

| ❌ Disadvantages |
|---|
| Primarily a tracking dataset |
| Indoor subset must be filtered manually |

### Why only 5%?

It is not intended as the main detection dataset but provides useful exposure to video-based scenarios that resemble FPV flight.

**Recommendation: ⭐⭐⭐⭐☆**

---

## Why these percentages?

| Dataset | Role in Training | Percentage |
|---|---|---|
| CrowdHuman | Learn robust human appearance and occlusions | 45% |
| COCO (Person only) | Increase diversity and improve generalization | 25% |
| IndoorCrowd | Adapt the model to indoor environments | 15% |
| Brainwash | Improve robustness to crowded indoor scenes | 5% |
| PersonPath22 (Indoor) | Prepare for video-based tracking and motion | 5% |
| Your own DJI O4 FPV dataset | Match the deployment domain exactly | 5–15% (recommended addition) |

---

## Recommendation specifically for your FPV UAV

Given the DJI O4 FPV footage you shared, I would make one important adjustment to the earlier mix. Once you have collected and labeled your own indoor drone footage, I would use:

| Dataset | Suggested Share |
|---|---|
| CrowdHuman | 35% |
| COCO (Person only) | 20% |
| IndoorCrowd | 15% |
| Brainwash | 5% |
| PersonPath22 (Indoor) | 5% |
| Your DJI O4 FPV Dataset | 20% |

This revised distribution is better because your own FPV data will have the exact camera optics, motion blur, field of view, perspective, and indoor environments that the UAV will encounter during deployment. In practice, a model fine-tuned on high-quality, representative FPV data often outperforms one trained solely on larger generic datasets, even if those public datasets contain many more images.
