# Day 1 Research: Problem Formulation & Core Literature Review

## 1. Problem Formulation: The Pedestrian Crowd Problem
A pedestrian crowd problem occurs when a high density of individuals moves through constrained spatial environments (railway stations, metro hubs, stadiums, crosswalks, festivals, and narrow corridors) such that collective movement becomes difficult to manage.

A crowd is not inherently dangerous simply due to high population count. Risk severity depends critically on the non-linear coupling of:
- **Spatial Density ($\rho$):** Number of individuals per unit area ($\text{persons/m}^2$).
- **Available Usable Width ($W$):** Corridors, pinch points, and bottleneck geometries.
- **Velocity Vector Distribution ($\vec{v}$):** Speed variance and angular spread.
- **Physical & Social Interactions:** Collision anticipation, compression forces, and personal space violations.

---

## 2. Research Literature Analysis

### A. Safety & Accident Prevention Modeling (2026)
* **Source:** [Accident Analysis & Prevention (2026)](https://www.sciencedirect.com/science/article/abs/pii/S0001457526000916?via%3Dihub)
* **Core Takeaways:** 
  - Analyzes risk management, infrastructure layout, and human behavioral psychology at transit intersections and crosswalks.
  - Translates behavioral theories into actionable accident prevention frameworks to mitigate collision hazards and evacuation failures.

### B. Foot-Tracking & Jamming Phase Transitions (2025)
* **Source:** [Science Advances (2025)](https://doi.org/10.1126/sciadv.adw2688)
* **Core Takeaways:**
  - Deploys bottom-up transparent floor tracking to measure individual walking speeds, step lengths, and step frequencies.
  - Identifies three dynamical regimes: **Free** ($\rho < 0.75\text{ p/m}^2$), **Slow-Moving** ($0.75 \le \rho \le 1.80\text{ p/m}^2$), and **Jammed** ($\rho > 1.80\text{ p/m}^2$).
  - Demonstrates that spontaneous movement synchronization peaks at $\rho \approx 1.80\text{ p/m}^2$ (the critical jamming threshold), providing a predictive leading indicator for stampede risk.

### C. Dimensionless Numbers in Crowd Physics (2024)
* **Source:** [PNAS Nexus (2024)](https://academic.oup.com/pnasnexus/article/3/4/pgae120/7632022)
* **Core Takeaways:**
  - Introduces fluid-dynamics-inspired dimensionless numbers to classify crowd flow regimes mathematically:
    1. **Intrusion Number ($In$):** Quantifies psychological personal space preservation.
    2. **Avoidance Number ($Av$):** Measures physical collision anticipation.
  - Enables perturbative expansions of pedestrian dynamics to rigorously validate simulation engines.

---

## 3. Computer Vision Dataset Review: CrowdHuman
* **Source:** [CrowdHuman Kaggle Dataset](https://www.kaggle.com/datasets/menhari/crowd-human-crowd-detection)
* **Dataset Scope:** ~15,000 high-density images curated for training machine learning models to detect humans under extreme occlusion.
* **Annotation Format:** Pre-annotated bounding boxes in YOLO (You Only Look Once) format focusing on the "person" class.
* **Application in our CS Architecture:**
  - Trains real-time object detection models to count and localize pedestrians in crowded surveillance scenes.
  - Feeds bounding box tracking directly into density estimation and bottleneck alert modules.
