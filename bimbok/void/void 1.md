# Pedestrian crowd dynamics in merging sections: Revisiting the “faster-is-slower” phenomenon
### 1. Alerting Logic & Anomaly Detection (Computer Vision / Backend)

In real-time crowd surveillance systems, setting naive alarm rules (e.g., _"alarm whenever running is detected"_) creates high false-positive rates. This research defines how to distinguish **efficient evacuation** from a **dangerous crush**:

- **Flow State Classification Rule:**          $$\text{State} = f(\text{Density } \rho, \text{Velocity } v, \text{Bottleneck Ratio } \gamma)$$
    - **Normal / Fast Egress (exit) (FIF Zone):** High density + High velocity in wide/merging channels $\rightarrow$ **Safe / Rapid Clearing** (No alarm; system tracks discharge throughput).
    - **Clogging / Stampede Hazard (FIS Zone):** High density + Near-zero velocity ($v \to 0$) at severe physical choke points $\rightarrow$ **Critical Alarm** (triggers bottleneck warning, automated route-divert alerts).
### 2. Calibrating Simulation Engines (Agent-Based Models / Digital Twins)

If your project includes a simulation component (e.g., in Python via Pygame/Mesa, Unity, or NetLogo using Helbing’s **Social Force Model**):

- **Bug Prevention:** Standard Social Force formulas aggressively increase tangential friction forces ($\kappa \cdot g$) when desired velocity ($v_0$) increases, artificially causing severe gridlock at all junctions.    
    
- **Algorithm Refinement:**
    - In open and merging sections: Keep repulsive and friction coefficients low so agents discharge faster when $v_0$ is elevated.
    - Only scale up friction/jamming coefficients when the corridor width $W$ drops below a critical threshold $W_{\text{crit}}$ (e.g., $W < 0.8\text{m}$).
### 3. Dynamic Routing & Evacuation Dispatch Algorithms

If your platform suggests escape paths or controls automated exit gates:

- **Weighted Graph / Shortest Path Optimization ($A^*$, Dijkstra):**
    
    Instead of routing evacuees solely by shortest physical distance, calculate dynamic edge weights:
    $$\text{Edge Cost} = \frac{\text{Distance}}{v_{\text{eff}}} + \text{Penalty}(\text{Angle}, \text{Width Ratio})$$
    
- **Merge-Aware Load Balancing:** Route traffic toward corridor junctions with optimized merge angles rather than abrupt perpendicular intersections, maximizing aggregate throughput.