# Executive Research Summary: The Pedestrian Crowd Problem

### 1. Problem Statement
The **Pedestrian Crowd Problem** arises in high-density transit hubs, stadiums, and urban bottlenecks where extreme density ($\rho$), narrow geometries ($W$), and turbulent counterflows trigger sudden phase transitions from fluid movement to arching, gridlock ("freezing by heating"), and life-threatening compressive asphyxia.

---

### 2. Core Scientific Findings & Research Articles

* **Flow Mechanics & Self-Organization:**
  * **[Social Force Model & Self-Organization](https://itp.uni-frankfurt.de/~gros/StudentProjects/Applets_2014_PedestrianCrowdDynamics/PedestrianApplet.html):** Pedestrians follow Newtonian driving/repulsive potentials with forward-cone bias ($\lambda = 0.75$). Streams naturally form alternating lanes ($180^\circ$) or diagonal stripes ($0^\circ - 180^\circ$), but collapse into total gridlock ("freezing by heating") under extreme panic or excessive desired speed.
  * **[MIT 13° Angular Spread Threshold (PNAS 2025)](https://news.mit.edu/2025/mathematicians-uncover-logic-behind-how-crowds-walk-0324):** Directional spread $\theta_{\text{spread}} < 13^\circ$ sustains self-organized lanes; $\theta_{\text{spread}} \ge 13^\circ$ triggers turbulent evasive maneuvers and drastic throughput loss.
  * **[Footstep Kinematics & Jamming (Science Advances 2025)](https://doi.org/10.1126/sciadv.adw2688):** Crowds exhibit 3 regimes: Free ($<0.75$), Slow-Moving ($0.75-1.80$), and Jammed ($>1.80\text{ p/m}^2$). In-phase footstep synchronization peaks at $\approx 1.80\text{ p/m}^2$, serving as a leading indicator for jamming.
  * **[Dimensionless Numbers (PNAS Nexus 2024)](https://academic.oup.com/pnasnexus/article/3/4/pgae120/7632022):** Defines *Intrusion Number* (psychological personal space) and *Avoidance Number* (collision deceleration) to classify crowd risk mathematically.
  * **[3D Granular & Contact Dynamics (arXiv 2025)](https://arxiv.org/abs/2505.05826):** At extreme density ($\rho > 6\text{ p/m}^2$), 2D social models fail; physical contact forces ($\sum \vec{F}^c \gg \vec{F}^{\text{drv}}$) govern the crowd, triggering pressure waves ("crowd quakes") and crush asphyxia.
  * **[Faster-is-Slower vs Faster-is-Faster (Physica A)](https://www.sciencedirect.com/science/article/abs/pii/S0378437117308956):** Higher velocity increases throughput in merging corridors ("Faster-is-Faster"); clogging ("Faster-is-Slower") triggers only at severe physical choke points ($W < 0.8\text{ m}$).

* **Biomechanical Simulation:**
  * **[PLEdestrians: Principle of Least Effort (ACM SIGGRAPH 2010)](http://gamma.cs.unc.edu/PLEdestrians/) ([Video Demo](https://youtu.be/hpYdjHzHTkY?si=UllOyl_5U_O6Unr2)):** Agents optimize total metabolic locomotion cost ($P(v) = m(\alpha + \beta v^2)$), naturally generating bottleneck arching, edge effects, and proactive detours around congestion in real-time.

* **Computer Vision, Perception & Datasets:**
  * **[Multi-Source Feature Fusion (IJACSA 2020)](https://thesai.org/Publications/ViewPaper?Volume=11&Issue=1&Code=IJACSA&SerialNo=87):** Combines LBP (texture), GLCM (contrast/entropy), and 2D Fourier FFT gradients into a 128-element vector $\to$ Linear SVM ($\text{AUC} = 0.63$ on UCF CC 50) for static crowd segmentation without motion cues.
  * **[Cascade R-CNN Pedestrian Detection (ACM ICCSIE 2022)](https://doi.org/10.1145/3558819.3565151):** Progressive IoU thresholds ($0.5 \to 0.6 \to 0.7$) to avoid sample degradation in dense scenes.
  * **Datasets:** [CrowdHuman (Kaggle)](https://www.kaggle.com/datasets/menhari/crowd-human-crowd-detection) (YOLO occluded person detection) and [ShanghaiTech (Kaggle)](https://www.kaggle.com/datasets/tthien/shanghaitech-with-people-density-map) (geometry-adaptive Gaussian density maps $D(x) = \sum \delta(x - x_i) * G_{\sigma_i}(x)$).

* **Spatial-Temporal AI & Trajectory Routing:**
  * **[ViTE: Virtual Graph Trajectory Expert Router (AAAI 2026)](https://github.com/Carrotsniper/ViTE):** Dynamic virtual graph super-nodes + Mixture-of-Experts (MoE) GNN predicting bivariate Gaussian collision-free paths.
  * **[Indoor Crowd Flow Forecasting (IEEE T-ITS 2025)](https://doi.org/10.1109/TITS.2025.3559353):** Synthetic benchmarking proving that standard time-series ML fails on unseen emergency data, necessitating physics-simulation hybrids.

* **Urban Safety & Infrastructure Context:**
  * **[Kolkata Road Safety / MoRTH Data (Times of India)](https://timesofindia.indiatimes.com/city/kolkata/kolkatas-road-safety-push-must-begin-with-pedestrians-iit-transport-expert/articleshow/133293532.cms):** Pedestrians account for $>50\%$ of road fatalities in West Bengal; justifies CV sidewalk spillover tracking and dynamic crossing signal phasing.
  * **[Accident Analysis & Prevention (2026)](https://www.sciencedirect.com/science/article/abs/pii/S0001457526000916?via%3Dihub)** & **[AI/CV in Crowd Safety Special Issue](https://www.sciencedirect.com/special-issue/100PZRGHRL6)**.

---

### 3. Unified Computer Science Solution (5-Layer Pipeline)

```text
[CCTV Video] ──► 1. Multi-Scale Perception (YOLO / CSRNet Density Maps / LBP+FFT SVM)
                       │
                       ▼
                 2. Physics State Estimation (Angular Spread >13° / Footstep Sync / Contact Force)
                       │
                       ▼
                 3. AI Spatial-Temporal Forecasting (ViTE GNN Mixture-of-Experts Router)
                       │
                       ▼
                 4. Digital Twin Simulation (PLEdestrians Least-Effort & Social Force Loop)
                       │
                       ▼
                 5. Actuation & Safety Dashboard (Dynamic A* Egress Routing / Adaptive Signals)
```

1. **Perception Layer:** Edge CCTV inference running YOLO detection and CSRNet density regression to track real-time crowd count, spatial heatmaps, and sidewalk spillover tripwires.
2. **State Estimation Layer:** Computes optical flow vector variance (triggering alarms if angular spread $\ge 13^\circ$), footstep synchronization at $\rho \approx 1.80\text{ p/m}^2$, and flow state $\text{State} = f(\rho, v, \gamma)$ (distinguishing safe fast egress from bottleneck stampedes).
3. **Forecasting Layer:** ViTE GNN router predicts agent trajectory distributions $5 - 30\text{ s}$ ahead to identify downstream conflicts.
4. **Digital Twin Layer:** Real-time PLEdestrians simulation continuously evaluates "what-if" capacity scenarios using metabolic energy minimization.
5. **Actuation Layer:** Dynamically calculates weighted evacuation escape paths ($\text{Edge Cost} = \frac{\text{Distance}}{v_{\text{eff}}} + \text{Penalty}(\theta_{\text{merge}}, W)$), extends pedestrian green-time at traffic signals, and broadcasts live alerts to a Web GIS emergency dashboard.
