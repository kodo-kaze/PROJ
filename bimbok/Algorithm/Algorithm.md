# Multi-Source Crowd Hazard Detection and Dynamic Evacuation Routing Algorithm (M-CHDER)

## 1. Problem Formulation

In dense urban thoroughfares and high-traffic transit nodes (e.g., Kolkata Metro hubs, crowded crossings, and narrow merging corridors), pedestrian movement suffers from two critical failure modes:

  

1. **False Anomaly Tripping vs. Bottleneck Blindness:** Standard surveillance either treats all fast running as a panic stampede or fails to detect static, high-density arching/clogging in encroached, narrow bottlenecks.
    
      
    
2. **Suboptimal Evacuation Eviction:** Typical shortest-path algorithms ($A^*$, Dijkstra) route crowds directly into intersecting or narrow choke points, ignoring junction merge angles and directional disorder (angular spread).
    
      
    

This algorithm provides a unified, two-part pipeline:

  

- **Perception & Hazard Engine:** Extracts appearance-based texture features (LBP, Fourier, GLCM) for motion-independent crowd segmentation, calculates velocity vectors, measures angular spread ($\theta_{\text{spread}}$), and determines whether a flow regime is in **FIF (Fast Egress)**, **Disordered**, or **FIS (Clogging/Stampede Hazard)** state.
    
      
    
- **Dynamic Egress Routing Engine:** Uses the Principle of Least Effort (PLE) combined with merge-angle penalties to balance pedestrian outflow away from saturated bottlenecks.
    
      
    

## 2. Mathematical & Theoretical Framework

### A. Feature Extraction & Spatial Crowd Mask

Following the Basalamah & Khan framework, the frame is partitioned into uniform $32 \times 32$ cells. For each cell $c$:

  

$$\mathbf{x}_c = \left[ \mathbf{f}_{\text{LBP}} \,\Vert{}\, \mathbf{f}_{\text{Fourier}} \,\Vert{}\, \mathbf{f}_{\text{GLCM}} \right] \in \mathbb{R}^{128}$$

$$\text{CrowdScore}(c) = \text{GaussianFilter}\left( \text{SVM\_Predict}(\mathbf{x}_c), K_{11 \times 11} \right)$$

### B. Angular Spread Metric (MIT Model)

Pedestrian directional vectors $\vec{v}_i$ extracted via sparse optical flow or tracking within a region of interest (ROI) have mean angle $\bar{\theta}$. The angular variance $\sigma_\theta$ determines organizational stability:

  

$$\theta_{\text{spread}} = \sqrt{\frac{1}{N} \sum_{i=1}^{N} (\theta_i - \bar{\theta})^2}$$

- If $\theta_{\text{spread}} \le 13^\circ$: **Stable Lane Formation / Ordered Stream**
    
      
    
- If $\theta_{\text{spread}} > 13^\circ$: **Disordered Movement / High Avoidance Drag**
    
      
    

### C. Dynamic Path Cost (Principle of Least Effort + Junction Optimization)

For a graph $G(V, E)$ modeling a corridor network, edge cost $C(u, v)$ between nodes $u$ and $v$ is weighted by distance $d_{uv}$, effective velocity $v_{\text{eff}}$, bottleneck width $W$, and merging angle $\varphi$:

  

$$C(u, v) = \frac{d_{uv}}{\max(v_{\text{eff}}, \epsilon)} + \alpha \cdot \rho_v + \beta \cdot \left( \frac{1}{\max(W_v, W_{\text{crit}})} \right) + \gamma \cdot \sin\left(\frac{\varphi_{uv}}{2}\right)$$

Where:

  

- $\rho_v$ is the real-time density in target node $v$.
    
      
    
- $\varphi_{uv}$ is the junction angle ($0^\circ \le \varphi \le 180^\circ$).
    
      
    
- $\sin(\varphi/2)$ penalizes abrupt right-angle or counterflow merges ($180^\circ$).
    
      
    

## 3. Algorithm Pseudocode

### Module 1: Crowd State & Bottleneck Risk Classifier

```plaintext
Algorithm 1: EvaluateCrowdFlowRegime
Input:
    Frame F_t                            // Current surveillance video frame at time t[cite: 1]
    Frame F_{t-1}                        // Previous consecutive video frame at time t-1[cite: 1]
    CorridorWidth W                      // Measured physical width of the passageway/corridor (in meters)[cite: 1]
    CriticalWidth W_crit = 1.0 m         // Minimum corridor width below which physical clogging/arching occurs[cite: 1]
    CriticalDensity ρ_crit = 4.0 ped/m^2 // Empirical threshold for severe, dangerous crowd compaction[cite: 1]
    StallVelocity v_stall = 0.25 m/s     // Near-zero walking speed indicating gridlock/stalling[cite: 1]
    DisorderAngleLimit θ_limit = 13.0°   // MIT angular spread limit where organized lanes collapse into disorder[cite: 1]

Output:
    HazardState (NORMAL_FLOW, HIGH_THROUGHPUT_FIF, DISORDERED_STREAM, CRITICAL_CLOG_FIS)
    DensityMap D_t

1.  Divide F_t into grid of 32x32 pixel cells C
2.  For each cell c in C:
3.      f_lbp = ExtractLBP(c, P=8, R=1)                            // Extracts Local Binary Pattern texture histogram using 8 neighbors at radius 1
4.      f_fourier = ExtractFourierStatistics(c, cutoff=0.4)        // Computes frequency statistics (mean, variance, skewness, kurtosis) with 0.4 cutoff
5.      f_glcm = ExtractGLCMProperties(c, angles=[0, 45, 90, 135]) // Extracts GLCM texture features (entropy, energy, contrast, homogeneity) at 4 angles
6.      x_c = Concatenate([f_lbp, f_fourier, f_glcm])              // Combines appearance descriptors into a unified 128-element feature vector
7.      CellScore[c] = LinearSVM_Predict(x_c)                      // Classifies cell c as crowd or non-crowd confidence score using trained Linear SVM
8.  
9.  D_t = ApplyGaussianFilter2D(CellScore, 
								KernelSize=11,
								Sigma=1.5)  // Smooths cell classification grid using an 11x11 2D Gaussian kernel to generate spatial density map
10. CrowdCount = IntegrateDensity(D_t)      // Sums the continuous smoothed density values across the active crowd mask
11. ρ = CrowdCount / Area(ROI)              // Computes real-time crowd density (pedestrians per square meter) within the Region of Interest
12.
12. FlowVectors = ComputeOpticalFlow(F_{t-1}, F_t, Mask=D_t)
13. v_avg = MeanMagnitude(FlowVectors)
14. θ_spread = CalculateAngularSpread(FlowVectors)
16.
15. // Boundary logic based on empirical findings:
16. If W < W_crit AND ρ >= ρ_crit AND v_avg <= v_stall Then:
17.     Return (HazardState.CRITICAL_CLOG_FIS, D_t)
18. Else If ρ >= ρ_crit AND v_avg > v_stall AND θ_spread <= θ_limit Then:
19.     Return (HazardState.HIGH_THROUGHPUT_FIF, D_t)
20. Else If θ_spread > θ_limit AND ρ > 2.0 Then:
21.     Return (HazardState.DISORDERED_STREAM, D_t)
22. Else:
23.     Return (HazardState.NORMAL_FLOW, D_t)
```

### Module 2: Least-Effort Merge-Aware Evacuation Dispatcher

```plaintext
Algorithm 2: LeastEffortEvacuationRouter
Input:
    Facility Graph G(V, E)
    Source Node S
    Safe Exit Nodes Set X
    RealTimeDensity D_t
    RealTimeHazardStates H_t
    CorridorWidths W
    MergeAngles Φ

Output:
    OptimalPath P
    SignalAdjustmentMap S_adjust

1.  Initialize MinPriorityQueue Q
2.  Initialize CostMap dist with ∞ for all v in V; dist[S] = 0
3.  Initialize ParentMap parent
4.  Q.insert(S, priority=0)
5.
5.  While Q is not empty:
6.      u = Q.extractMin()
7.      
8.      If u in X:
9.         P = ReconstructPath(parent, u)
10.         Break
12.
11.     For each outgoing edge (u, v) in E:
12.         // Severe penalty for physical bottlenecks stuck in FIS regime
13.         If H_t[v] == CRITICAL_CLOG_FIS:
14.             EdgeWeight = ∞
15.         Else:
16.             v_eff = EstimateVelocity(v, D_t[v])
17.             AnglePenalty = sin(Φ[u][v] / 2.0)
18.             BottleneckPenalty = 1.0 / max(W[v], 0.8)
19.             EdgeWeight = (Distance(u, v) / max(v_eff, 0.1)) + (0.5 * D_t[v]) + (1.2 * BottleneckPenalty) + (0.8 * AnglePenalty)
22.
20.         If dist[u] + EdgeWeight < dist[v]:
21.             dist[v] = dist[u] + EdgeWeight
22.             parent[v] = u
23.             Q.insertOrUpdate(v, dist[v])
27.
24. // Signal adjustment for urban conflict crossings (e.g., Kolkata safety intervention)
25. For each crossing node k in P:
26.     If D_t[k] > 3.0:
27.         S_adjust[k].extendWalkPhaseBy = min(15.0, D_t[k] * 2.5) // seconds
32.
28. Return (P, S_adjust)
```

## 4. Complexity Analysis

- **Perception Stage (Feature Extraction + SVM):**
    
      
    
    $$\mathcal{O}(B \times C_{\text{cell}})$$
    
    Where $B$ is the number of $32 \times 32$ image blocks. Linear SVM evaluation over a fixed $128$-element vector executes in $\mathcal{O}(1)$ per cell, running comfortably in real time ($\ge 25\text{ FPS}$).
    
      
    
- **Routing & Dispatch Stage:**
    
      
    
    $$\mathcal{O}(\vert{}E\vert{} + \vert{}V\vert{} \log \vert{}V\vert{})$$
    
    Using a binary/Fibonacci heap for the priority queue across standard building/junction layout graphs.
    

## 5. Research Grounding & Justification

| **Research Aspect**                   | **Sourced File / Paper**                               | **How It Directly Shapes The Algorithm**                                                                                                                                                  |
| ------------------------------------- | ------------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Appearance Feature Vector (128-D)** | Basalamah & Khan (void 4)                              | Replaces fragile motion-differencing with LBP + Fourier + GLCM texture descriptors to detect stationary and moving dense crowds alike.                                                    |
| **FIF vs. FIS Classification**        | Merging Dynamics Study (void 1)                        | Prevents false alarms during high-speed evacuations; triggers critical warnings only when velocity drops to near-zero ($v \le 0.25\text{ m/s}$) at narrow choke points ($W < 1\text{m}$). |
| **Disorder Threshold ($13^\circ$)**   | MIT Crowd Logic Study (void 3)                         | Uses angular spread variance $\theta_{\text{spread}} > 13^\circ$ to detect breakdown of self-organized lanes into collision-heavy turbulent flow.                                         |
| **Least Effort & Merge Penalty**      | PLEdestrians (Day 2) & Self-Organized Dynamics (Day 3) | Replaces naive Euclidean shortest path with biomechanical delay and junction merge angle penalties ($\sin(\varphi/2)$).                                                                   |
| **Adaptive Signal Phasing**           | Kolkata Pedestrian Study (Day 3)                       | Dynamically allocates extended pedestrian crossing phases at saturated black-spot intersections.                                                                                          |