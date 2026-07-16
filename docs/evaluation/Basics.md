# 📘 Beginner's Guide to Evaluation Terms

This section explains the most important terms used in this project. The explanations are written for beginners, so you don't need any prior knowledge of computer vision or artificial intelligence.

---

# 1️⃣ RF-DETR (Real-Time Detection Transformer)

## What is RF-DETR?

RF-DETR is an **Artificial Intelligence (AI) model** that looks at an image and finds objects inside it.

For example, if you give RF-DETR a street image, it can detect:

- 👤 Person
- 🚗 Car
- 🚲 Bicycle
- 🐕 Dog
- 🚌 Bus

and many other objects.

After finding an object, it draws a **bounding box** around it and tells what the object is.

---

### Example

Input Image

```
📷 Street Image
```

Output

```
Person
┌───────────┐
│           │
│  Person   │
│           │
└───────────┘

Confidence: 98%
```

---

### Think of it like...

Imagine you are playing **"Find the Object"** in a picture.

RF-DETR does exactly the same thing, but automatically and much faster.

---

### Why do we use it?

Because before measuring how far an object is, we first need to know:

- What is the object?
- Where is it?

RF-DETR answers both questions.

---

# 2️⃣ Depth Anything V2

## What is Depth Anything V2?

Depth Anything V2 is another AI model.

Instead of detecting objects, it estimates **how far every part of the image is from the camera.**

It creates something called a **Depth Map**.

---

### Example

Suppose this is your image:

```
Camera

🚶 Person

🚗 Car

🏢 Building
```

Depth Anything V2 estimates

```
Person     → 2.4 m

Car        → 8.6 m

Building   → 45 m
```

---

### Think of it like...

Imagine your eyes can instantly tell:

> "That person is close."

> "That building is very far."

Depth Anything V2 gives the computer this ability.

---

### Why do we use it?

Object detection only tells us:

> "There is a person."

Depth estimation tells us:

> "That person is 2.4 meters away."

Together they help the computer understand the world.

---

# 3️⃣ mAP (Mean Average Precision)

## What is mAP?

mAP measures **how good the object detector is.**

It checks whether RF-DETR:

- found the object
- classified it correctly
- drew the bounding box correctly

---

### Imagine This

Ground Truth

```
┌─────────┐
│ Person  │
└─────────┘
```

Prediction

```
┌─────────┐
│ Person  │
└─────────┘
```

Perfect match ✅

High mAP

---

Bad Prediction

```
┌────────────────────┐
│ Person             │
└────────────────────┘
```

Very loose box ❌

Low mAP

---

### Range

```
0 → Very Bad

1 → Perfect
```

Higher is always better.

---

### Think of it like...

Imagine drawing a box around a football.

The closer your drawing matches the football,

the higher your score.

That's exactly what mAP measures.

---

# 4️⃣ AbsRel (Absolute Relative Error)

## What is AbsRel?

AbsRel measures **how accurate the predicted distances are.**

It compares

Actual Distance

vs

Predicted Distance

---

### Example

Actual Distance

```
5 meters
```

Predicted

```
5.2 meters
```

Very small error

Low AbsRel ✅

---

Another example

Actual

```
5 meters
```

Predicted

```
10 meters
```

Huge error

High AbsRel ❌

---

### Range

```
0 = Perfect

Higher = Worse
```

Lower is always better.

---

### Think of it like...

If your friend is actually 5 meters away,

and you say

"He is 5.1 meters away,"

you are almost correct.

AbsRel measures this mistake.

---

# 5️⃣ RMSE (Root Mean Square Error)

## What is RMSE?

RMSE measures the **average distance error**.

Unlike AbsRel, RMSE measures the error directly in meters.

---

### Example

True Distance

```
10 m
```

Prediction

```
11 m
```

Error

```
1 meter
```

RMSE combines all such errors into one number.

---

### Think of it like...

Imagine measuring the height of many people.

If you are usually wrong by about

```
0.5 meter
```

RMSE will be around that value.

---

### Lower is better.

---

# 6️⃣ SqRel (Squared Relative Error)

## What is SqRel?

SqRel is similar to AbsRel,

but it gives **much bigger punishment** to large mistakes.

---

### Example

Prediction

```
5.1 m

Actual

5 m
```

Small mistake 🙂

Small penalty.

---

Prediction

```
30 m

Actual

5 m
```

Huge mistake 😬

Very large penalty.

---

### Why is this useful?

Sometimes a model is usually good,

but makes one or two terrible mistakes.

SqRel helps detect these big failures.

---

Lower is better.

---

# 7️⃣ FPS (Frames Per Second)

## What is FPS?

FPS tells us

**how many images the system can process every second.**

---

### Example

```
1 second

↓

30 images processed
```

FPS = 30

---

### Higher FPS means

✅ Faster

✅ Smoother

✅ Better for real-time systems

---

### Example

| FPS | Experience |
|------|------------|
| 5 | Very Slow |
| 15 | Acceptable |
| 30 | Smooth |
| 60 | Very Smooth |

---

Think of FPS like the refresh rate of a video.

The higher it is,

the smoother everything looks.

---

# 8️⃣ Recall

## What is Recall?

Recall measures

**how many real objects the detector successfully found.**

---

### Example

Actual Objects

```
👤 👤 👤 👤 👤
```

Detector found

```
👤 👤 👤 👤
```

Recall

```
4 / 5

= 80%
```

---

Higher Recall means

The detector misses fewer objects.

---

Think of it like...

Imagine taking attendance in a classroom.

If there are

30 students

but you only count

25,

your Recall is low.

---

# 9️⃣ Precision

## What is Precision?

Precision measures

**how many detected objects are actually correct.**

---

### Example

Detector says

```
Person

Person

Person

Person

Person
```

Reality

```
4 are Persons

1 is actually a tree
```

Precision

```
4 / 5

= 80%
```

---

Higher Precision means

The detector makes fewer false alarms.

---

### Think of it like...

Imagine a smoke detector.

If it rings every time someone cooks,

its Precision is poor because it keeps giving false alarms.

A good detector only rings when there is a real fire.

Object detectors work the same way.

---

# 📊 Quick Summary

| Term | What Does It Measure? | Better Value |
|--------|------------------------|--------------|
| RF-DETR | Finds objects | Higher Accuracy |
| Depth Anything V2 | Predicts object distance | Lower Error |
| mAP | Detection quality | Higher |
| AbsRel | Depth accuracy | Lower |
| RMSE | Average distance error | Lower |
| SqRel | Large distance mistakes | Lower |
| FPS | Processing speed | Higher |
| Recall | Objects found | Higher |
| Precision | Correct detections | Higher |