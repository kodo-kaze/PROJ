# Archi370 - Literature Review & Research Findings

## Domain: Automated Computer Vision & Edge Systems for Crowd Safety

### 1. The Core Problem Statement
High-density pedestrian congestion in mass transit hubs, festival grounds, and urban crosswalks presents severe challenges for conventional surveillance systems. Manual monitoring by human operators is prone to cognitive fatigue, high latency, and severe blind spots, preventing timely interventions during sudden stampedes, arching, or bottleneck blockages.

---

### 2. Key Research & Algorithmic Solutions

#### A. Multi-Scale Pedestrian Detection & Occlusion Handling
* **Challenge:** In extreme crowd densities, traditional object detectors fail due to severe inter-pedestrian occlusion, scale variation, and perspective distortion.
* **Algorithmic Solutions:**
  - **YOLO-based Single-Stage Detectors:** Trained on dense datasets like **CrowdHuman** with customized bounding box regression loss functions that prioritize visible upper-body and head annotations.
  - **Cascade R-CNN Architectures:** Utilizing progressive IoU thresholds ($0.5 \to 0.6 \to 0.7$) to eliminate positive sample degradation in highly cluttered video frames.
  - **Density Map Regression (CSRNet / MCNN):** Transforming discrete point annotations into continuous spatial density distributions:
    $$D(x) = \sum_{i=1}^N \delta(x - x_i) * G_{\sigma_i}(x)$$

#### B. Edge Computing & Real-Time Alerting Pipelines
* **Edge Deployment:** Deploying quantized lightweight neural networks (TensorRT / ONNX) on edge devices (NVIDIA Jetson, edge servers) to achieve $<30\text{ms}$ per-frame inference latency.
* **Automated Anomaly Detection:**
  - Tracking optical flow vector divergence to identify sudden counterflow conflicts.
  - Dynamic thresholding on velocity-density ratios to trigger automated alarm dispatches before physical crowd crushing occurs.

---

### 3. Relevance to Final Year Project
- **Perception Subsystem:** Provides real-time crowd count, spatial heatmaps, and ROI segmentation masks to the central system dashboard.
- **Early Warning Logic:** Feeds density and velocity metrics directly into the project's state estimation and dynamic routing algorithms.
