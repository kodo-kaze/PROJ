# Algorithm Proposal: Multi-Stage Spatial-Temporal Crowd Pipeline

## 1. Problem Statement
This algorithm addresses the critical need to identify phase transitions in extreme-density crowds before they result in compressive asphyxia or bottleneck stampedes. It combines continuous density mapping, biomechanical force thresholding, and graph-based trajectory prediction into a unified pipeline to dynamically categorize hazard states and calculate collision-free egress routes.

## 2. Input and Output
* **Inputs:** 
  * `VideoStream S`: Continuous camera feed.
  * `RegionOfInterest ROI`: Spatial bounds of the monitored bottleneck.
  * `CriticalForceThreshold`: Limit where physical contact supersedes human routing ($\epsilon_{\text{force}}$).
* **Outputs:** 
  * `DensityMap D(x)`: Continuous density regression matrix.
  * `HazardLevel`: Classification state (`SAFE`, `TURBULENT`, `CRITICAL_CRUSH`).
  * `OptimalPaths Y`: Bivariate Gaussian trajectory coordinates for safe routing.

## 3. Pseudocode
Algorithm: ProcessSpatialTemporalCrowdState
Input: VideoStream S, RegionOfInterest ROI, CriticalForceThreshold
Output: HazardLevel, OptimalPaths Y

1. For each incoming frame F in VideoStream S:
2.      F_cropped = cropToROI(F, ROI)
3.      
4.      // Stage 1: Density Mapping 
5.      Extract pedestrian head coordinates {x_1, x_2, ..., x_N} from F_cropped
6.      Compute continuous density map D(x) using Gaussian kernel:
        $$ D(x) = \sum_{i=1}^N \delta(x - x_i) * G_{\sigma}(x) $$
7.      Calculate total crowd count C = Integrate(D(x))
8.      
9.      // Stage 2: Biomechanical Force Estimation
10.     Calculate driving forces (F_drv) and physical contact forces (F_c) for nodes
11.     If SUM(|F_c|) >> SUM(|F_drv|) OR SUM(|F_c|) >= CriticalForceThreshold Then:
12.         BroadcastMQTTAlert(ROI.id, HazardLevel.CRITICAL_CRUSH)
13.         Trigger Mitigation Signals (e.g., extend red lights for incoming flow)
14.         Return HazardLevel.CRITICAL_CRUSH, NULL
15.         
16.     // Stage 3: Trajectory Routing (If state is non-critical)
17.     Else:
18.         Initialize Virtual Graph V with nodes as pedestrians and edges as interactions
19.         For each node i, update hidden state aggregating spatial distance ||p_i - p_j||
20.         Predict bivariate Gaussian distribution for next coordinates:
            $$ \hat{Y} = \{\hat{p}_i^{(t+1)}, ..., \hat{p}_i^{(t+T_{\text{pred}})}\} $$
21.         Return HazardLevel.SAFE, \hat{Y}

## 4. Complexity Analysis
* **Time Complexity:** $O(N \log N + E)$ per frame, where $N$ is the number of detected pedestrians (for density mapping and Gaussian generation) and $E$ is the number of spatial interaction edges in the trajectory graph.
* **Space Complexity:** $O(W \times H)$ to store the pixel-wise continuous density map $D(x)$, plus $O(N)$ for the virtual graph node embeddings.

## 5. Research Justification
This multi-stage pipeline directly synthesizes three recent paradigms. The continuous density generation $D(x)$ utilizes geometry-adaptive synthetic benchmarking (IEEE T-ITS 2025). The transition to `CRITICAL_CRUSH` is dictated by the multiscale 3D mechanical force paradigm, where contact forces $|\vec{F}^{\text{c}}|$ override social forces (arXiv 2025). Finally, the safe routing generation utilizes the Virtual Graph Trajectory Expert (ViTE) to sample collision-free paths via GNNs (AAAI 2025).