# Walle_FPV

Stage 1 (Completed) ✅
--------------------
✔ COCO
✔ CrowdHuman
✔ Dataset Merger

Stage 2 (Next)
--------------
⬜ Download VisDrone
⬜ Convert → YOLO
⬜ Verify labels
⬜ Merge into final dataset

Stage 3
--------
⬜ Dataset statistics
⬜ Create dataset.yaml
⬜ Train YOLO11s baseline

Stage 4
--------
⬜ Evaluate
⬜ Error analysis
⬜ Improve dataset

Stage 5
--------
⬜ ByteTrack / BoT-SORT
⬜ Person re-identification (ReID)
⬜ Target selection
⬜ FPV follow-me logic

Stage 6
--------
⬜ Raspberry Pi optimization
⬜ Real-time inference
⬜ Drone integration

-------------------------------------------

# Custom Agent Rules

## IEEE Paper Summarization Guidelines
Whenever the user uploads an IEEE research paper for summarization, format the output as a highly professional, academic, and technical Markdown (.md) file using this exact structure:

1. **Title**: `# [Paper Title]`
2. **Abstract**:
   - A short technical summary of the problem and solution.
   - **Analogy**: A blockquote or alert box using a clear, real-world analogy.
   - **Takeaways**: A `> [!IMPORTANT]` alert box outlining what the paper accomplishes.
3. **Core Concepts: The Glossary**: A table with:
   | Term | Simple Definition | Why it matters |
4. **How it Works**:
   - A step-by-step description of the data pipeline.
   - A Mermaid flowchart representing the visual pipeline flow and tensor transformations. All transition labels containing parentheses or special characters must be enclosed in double quotes (e.g., `-->|"label"|`) to prevent parsing errors.
   - A `> [!IMPORTANT]` alert box highlighting the core technical innovation.
5. **Technical Architecture**:
   - A bulleted list of key architectural components.
   - A table mapping each module/layer's inputs, core operations, outputs, and tensor shapes.
6. **Summary of Experimental Results**:
   - A performance comparison table showing datasets, metrics, baseline models, and the paper's model.
   - A `> [!TIP]` alert box summarizing the key performance gains ("The Bottom Line").
7. **Why This Matters (Impact Analysis)**: Real-world impact and an actionable onboarding project first step.
8. **Learning Path: How to Replicate**: 3 foundational study modules.
9. **Where It Falls Short (Limitations)**: A `> [!WARNING]` alert box detailing key technical limitations.
10. **Quick Reference: Key Terms**: A list of acronym definitions.
11. **Sign-off**: A centered Shields.io badge at the very bottom:
    <p align="center">
      <img src="https://img.shields.io/badge/Prepared%20by-Arisudan-blue?style=flat-square&logo=markdown" alt="Prepared by Arisudan">
    </p>

Keep all descriptions lightweight, concise, technical, and highly readable.
------------------------------------------------------
4A FULLY AUTNOMOUS IN SSTRUCTURED ENVIRONMENT
4B FULLY AUTONOMOUS IN GPS-DENIED ENVIRONMENTS
4C FULLY AUTONOMOUS WITH DYNAMIC OBSTACLE AVAOIDANCE, ONLINE REPLANNING, AND PERCEPTION-DRIVEN DECISION MAKING

--------------
the working flow i needed:
live camera frame/video upload/video dataset only
|
Object Detcetion
|
RF-DETR and Depth Anything V2 Mteric Indoor
|
draw boxes on the video frame
|
show on the screen

now also to this i needed a simple explained .csv/Excel sheet containing the documenattion of the following Evaluation criterias for this object detcetion:
1. Precision
2. mAP@0.5 and mAP@0.5:0.95
3. Mean IoU
4. FPS
5. Inference Latency
6. Recall
7. F1 Score
8. Absolute Relative Error
9. Root Mean Squared Error
10. as
----------
