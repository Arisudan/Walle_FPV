# Understanding Your Pipeline's Evaluation Metrics

*A plain-language guide to how we measure whether the detection + depth system is actually working*

---

## Before we start: what are we even measuring?

Your pipeline does two jobs on every video frame:

1. **RF-DETR** looks at the frame and draws a box around each object it finds (a person, a bag, whatever it's trained to detect).
2. **Depth Anything V2** looks at the same frame and figures out how far away every pixel is, in meters.

Then your system combines both: it takes the box RF-DETR drew, looks at the depth values inside that box, and shows something like **"person — 3.2m"** on screen.

Because there are two models doing two different jobs, we need two different sets of tests to check if each one is doing its job well — plus a third set of checks for whether they work well *together*. That's really all "evaluation metrics" means: a checklist of tests, each one telling you something specific about whether a piece of the system is trustworthy.

Think of it like a car inspection. You don't just ask "is the car good?" — you check the engine, the brakes, and the tires separately, because each one can fail independently, and knowing *which* one failed tells you what to fix.

---

## The 4 numbers that actually matter (read this part first)

If you remember nothing else from this document, remember these four. Everything after this section exists to help you understand *why* one of these four numbers looks bad, if it ever does.

### 1. mAP — "Is RF-DETR finding objects correctly?"

**In plain terms:** Imagine RF-DETR draws a box around something it thinks is a person. mAP is a score that checks: was there actually a person there, and was the box drawn tightly around them (not too loose, not cutting them off)?

- **Scale:** 0 to 1 (sometimes shown as 0% to 100%)
- **0** = the model is basically guessing randomly and getting nothing right
- **1** = perfect — every box is exactly where it should be, every time
- **What's realistic:** For a well-trained model on a reasonably hard task, anywhere from **0.35 to 0.55** is solid. Don't panic if you see numbers like 0.1–0.2 on a hard task (e.g. spotting a small object from far away) — that's common and expected, not necessarily broken.

**Why this one, and not a simpler "accuracy" number?** Because "correct" isn't just yes/no for object detection — the box also has to be positioned well. mAP checks box quality at several strictness levels internally, so it can't be fooled by a model that finds the right object but draws a sloppy, oversized box around it.

### 2. AbsRel — "Is Depth Anything V2 measuring distance correctly?"

**In plain terms:** For every pixel where we know the *true* distance (measured with a real sensor), AbsRel checks how far off the model's *guess* was — as a percentage of the true distance. Being off by 10cm matters a lot if the true distance is 50cm, but barely matters if the true distance is 10 meters. AbsRel accounts for that automatically.

- **Scale:** 0 upward, no fixed ceiling
- **0** = perfect depth prediction, every pixel exactly right
- **Higher = worse**, and there's no maximum — a badly broken model could be off by 300% or more
- **What's realistic:** For indoor scenes, a strong model typically lands around **0.04 to 0.10** (meaning it's typically off by 4–10%). Anything consistently above 0.15 is a sign something is wrong.

**Why this one, and not "meters of error"?** Because "off by half a meter" means something completely different up close versus far away. AbsRel gives you one number that's fair across the whole room, not just accurate near the camera.

### 3. End-to-End FPS — "Does the whole system run fast enough to actually be live?"

**In plain terms:** FPS (Frames Per Second) is simply how many video frames your *entire* pipeline — RF-DETR detecting, Depth Anything V2 estimating distance, and the boxes being drawn on screen — can process in one second.

- **Scale:** 0 upward, limited only by your hardware
- **Higher is always better**
- **What's realistic:** You want **at least 10–15 FPS** for something that feels reasonably live. **24+ FPS** feels smooth, like normal video.
- **The trap to avoid:** RF-DETR's own spec sheet might say "60 FPS" and Depth Anything V2's might say "40 FPS" — but running *both, one after another, on the same frame* is always slower than either number alone. Only trust a speed number that was actually measured with your full pipeline running end-to-end, not the marketing number from either model by itself.

### 4. Per-Object Distance Error — "Can I trust the '3.2m' label shown next to each box?"

**In plain terms:** This is AbsRel again, but calculated in a much more specific way — only using the depth values *inside each detected box*, and only comparing it to the true distance of *that specific object*. This is different from AbsRel above, which checks the whole scene at once.

- **Scale:** Same as AbsRel — 0 upward, lower is better
- **Target:** Try to keep this under **0.10** (roughly 10% error) for objects you're actively reporting distances for.

**Why is this different from AbsRel, and why do we need both?** This is the most important idea in this whole document, so here's an example:

> Imagine the overall scene-wide depth accuracy (AbsRel) looks great — 0.06, very strong. But the box RF-DETR drew around a person is a little too wide, and it accidentally includes some wall or floor behind them. When we average the depth inside that box, we get a distance that's wrong — even though the *depth model itself* was accurate everywhere else in the scene.

Scene-wide AbsRel can hide this problem completely. Per-object distance error is the only metric that checks the exact number your user actually sees on screen. This is the number that tells you whether your product's core feature — "tell me how far away that object is" — is actually reliable.

---

## The supporting cast: other numbers you'll see, explained simply

These aren't as critical as the 4 above, but you'll run into them in logs, dashboards, or papers, so here's what they mean in plain language.

### For RF-DETR (detection)

| Term | Plain-language explanation |
|---|---|
| **AP50** | A looser version of mAP — a box only needs to overlap the real object by 50% to "count" as correct. Easier to score well on, so it's usually higher than mAP. |
| **Precision** | Out of everything the model flagged as an object, what percentage was actually right? Low precision = lots of false alarms. |
| **Recall** | Out of everything that was truly there, what percentage did the model actually catch? Low recall = the model is missing real objects. |
| **F1 Score** | One number that blends Precision and Recall together. Handy for a quick summary, but it can hide *which* of the two is the actual problem — so professionals usually check Precision and Recall separately instead of relying on F1 alone. |
| **FPS / Latency (RF-DETR only)** | How fast RF-DETR runs by itself, not counting the depth model. Useful for isolating whether a slowdown is coming from detection or from depth estimation. |

### For Depth Anything V2 (depth)

| Term | Plain-language explanation |
|---|---|
| **RMSE** | The average error in actual meters, not percentages. Easier to picture ("the model is off by about half a meter on average") but gets thrown off badly by a few very wrong pixels. |
| **δ1** | The percentage of pixels that landed within 25% of the correct distance. Think of it as a "pass/fail" grade at a fairly strict tolerance — higher percentage is better. |

---

## The "under the hood" numbers: only worth checking when something's wrong

These are diagnostic tools, not report-card numbers. Think of them like a mechanic's diagnostic scanner — you don't check every one of these every day, only when a headline number (mAP, AbsRel, FPS, or Per-Object Distance Error) looks off and you need to figure out *why*.

**If mAP looks low, check these:**

- **AP75** — a stricter version of mAP. If AP75 is much worse than AP50, it means RF-DETR *is* finding the right objects, but the boxes it draws are loose and imprecise — a "sloppy drawing" problem, not a "can't see the object" problem.
- **AR@1 / AR@10 / AR@100** — tests recall while allowing the model to make 1, 10, or 100 guesses per image. If letting it make more guesses doesn't improve the score, the model genuinely isn't seeing certain objects — it's not a confidence-tuning issue, it's a real blind spot.
- **AP/AR broken down by object size (small / medium / large)** — this is usually the most useful diagnostic of all. It's extremely common for a model to do great on big, close objects and fail badly on small, distant ones. This breakdown tells you exactly where the weakness is hiding.

**If AbsRel or RMSE looks low, check these:**

- **SqRel** — like AbsRel, but it reacts much more strongly to a few really bad pixels. If SqRel is unusually high while AbsRel looks okay, it means the model is mostly fine but has occasional big, ugly failures somewhere in the frame.
- **RMSE log** — a version of RMSE that treats near and far distances more fairly. Comparing it to regular RMSE tells you whether your errors are concentrated on far-away objects (which is common) or spread evenly across the whole scene.
- **δ2 and δ3** — looser versions of δ1 (using bigger tolerance windows instead of 25%). If δ1 is weak but δ2/δ3 are strong, the model's mistakes are small and "close but not quite." If all three are weak, the mistakes are large and something is more seriously wrong.

---

## Putting it all together: a simple mental checklist

When you evaluate a new version of the pipeline, work through it in this order:

1. **Check the 4 headline numbers first** — mAP, AbsRel, End-to-End FPS, Per-Object Distance Error.
2. **If all 4 look healthy** — you're done, the system is working well, no need to dig further.
3. **If one of the 4 looks bad** — go to the matching "under the hood" section above and use those numbers to figure out exactly what's broken and why.
4. **Fix, re-test, repeat.**

You don't need to memorize every term in this document. You need to recognize the 4 headline numbers, understand roughly what "good" looks like for each, and know that the rest of this document is there to help you dig deeper *only when something looks off.*
