# Literature Review: Pedestrian Crowd Problem & Algorithmic Solutions

Research on the pedestrian crowd problem focuses on resolving congestion, predicting flow dynamics, and generating accurate density maps to prevent bottlenecks in high-traffic environments. The transition from traditional computer vision to deep spatial-temporal modeling is critical for handling heavy occlusions and non-linear movement.

[[void 1]]

### Vite: Virtual Graph Trajectory Expert Router for Pedestrian Trajectory Prediction (2025)

**Bibliography:** 2025 - AAAI Conference on Artificial Intelligence.

* **Problem Statement:** Traditional trajectory prediction models struggle with "social compliance"—understanding how humans subconsciously alter their paths to avoid others in dense, shared spaces where standard linear projections fail.
* **Architecture & Methodology:** 
  * The paper introduces a Virtual Graph Trajectory Expert Router utilizing Graph Neural Networks (GNNs). 
  * **Feature Engineering:** Pedestrians are treated as "nodes" and their social interactions (distance, velocity alignment) are mapped as "edges" within a spatial-temporal graph.
  * **Routing Mechanism:** Instead of predicting a single deterministic path, the network acts as an "expert router," dynamically weighing multiple potential trajectories based on the real-time movement vectors of surrounding nodes.
* **How it Overcomes the Crowd Problem:** By mapping the environment as an interactive graph rather than isolated moving pixels, the system accurately predicts sudden directional shifts and collision avoidance maneuvers, providing a highly reliable backend for automated surveillance and early warning systems.

[[void 2]]

### Evaluating Crowd Flow Forecasting Algorithms for Indoor Pedestrian Spaces: A Benchmark Using a Synthetic Dataset (2025)

**Bibliography:** 2025 - IEEE Transactions on Intelligent Transportation Systems.

* **Problem Statement:** Training robust predictive models for indoor crowd flow is hindered by severe data sparsity and class imbalance. Real-world datasets rarely capture extreme edge cases (like panic evacuations) without heavy background noise.
* **Architecture & Methodology:**
  * Proposes a comprehensive benchmarking pipeline driven by synthetic dataset generation.
  * **Simulation Engine:** Uses simulation software to generate realistic, high-fidelity indoor crowd scenarios, specifically engineering datasets that balance sparse normal conditions with dense bottleneck anomalies.
  * **Evaluation Metric:** Establishes a standardized evaluation pipeline to test various data-driven flow forecasting algorithms (like LSTMs or spatial-temporal CNNs) against these synthetic edge cases.
* **How it Overcomes the Crowd Problem:** Overcomes the "cold start" and data deficiency problems in crowd ML. By training on synthetically balanced data, models can be deployed into real-world indoor spaces (like malls or transit hubs) with a much higher baseline accuracy for evacuation routing.

[[void 3]]

### Exploring Dense Crowd Dynamics: State of the Art and Emerging Paradigms (2025)

**Bibliography:** 2025 - arXiv (Physics and Society) / Crowd Dynamics, Volume 5.

* **Problem Statement:** Legacy 2D collision avoidance models treat pedestrians as simple 2D bounding boxes. In extreme-density crowds (over 5-6 persons per square meter), movement is dictated by physical force transmission, not independent walking decisions.
* **Architecture & Methodology:**
  * Proposes a paradigm shift to 3D multiscale modeling.
  * **Biomechanical Mapping:** The model simulates physical force transmissions—such as body wedging, compression, and balance recovery. 
  * It maps the transition from "free flow" (where individuals make routing choices) to "turbulent flow" (where the crowd moves as a single fluid mass due to physical contact).
* **How it Overcomes the Crowd Problem:** By explicitly tracking physical forces rather than just visual trajectories, this framework can mathematically predict the onset of spontaneous oscillatory motion (crowd quakes) or crowd collapses, allowing for physical architectural interventions before fatal bottlenecks occur.
