# KCAD-DR Algorithm Proposal

## 1. Problem Statement
This algorithm addresses the transition of pedestrian crowds from free-flowing movement to life-threatening jammed states by detecting early microscopic warning signs. It utilizes YOLO-based computer vision for dense crowd counting, analyzes foot-level kinematics to detect movement synchronization, and computes dimensionless psychological metrics (Avoidance and Intrusion Numbers). When these factors indicate an impending crush[cite: 2], it dynamically recalculates evacuation routes to funnel traffic through pressure-relieving architectural geometries[cite: 1].

## 2. Input and Output
*   **Inputs:** `video_stream` (real-time CCTV), `yolo_model` (pretrained CrowdHuman detector), `facility_graph` (routing nodes/edges), and `edge_props` (dictionary of corridor features).
*   **Outputs:** `hazard_state` (SAFE, SLOW_REGIME, CRITICAL_JAM) and `optimal_path` (dynamically updated route).

## 3. Pseudocode
```
    Algorithm: KCAD-DR_Pipeline
    
    Input: video_stream, yolo_model, facility_graph, edge_props, safe_exits
    Output: hazard_state, optimal_path
    
    Initialize previous_frame to NULL
    
    FOR EACH frame F IN video_stream:
        # STAGE 1: AI Vision Processing & Microscopic Feature Extraction
        pedestrians = DETECT humans in F using yolo_model
        density_rho = COUNT(pedestrians) / Area of ROI
        
        IF previous_frame is NOT NULL:
            flow_vectors = COMPUTE optical flow between previous_frame and F
            avg_vel = CALCULATE mean of flow_vectors
            vel_variance = CALCULATE variance of flow_vectors
            step_sync = COMPUTE foot phase synchronization
            
            # STAGE 2: Hazard Classification via Dimensionless & Cognitive Metrics
            crowd_pressure = density_rho * vel_variance
            intrusion_num = CALCULATE intrusion based on density_rho and avg_vel
            avoidance_num = CALCULATE avoidance from flow_vectors
            
            IF step_sync >= SYNC_THRESH AND avoidance_num >= CRIT_AVOID:
                hazard_state = "CRITICAL_JAM"
            ELSE IF intrusion_num > MOD_INTRUSION AND avg_vel < 0.6:
                hazard_state = "SLOW_REGIME"
            ELSE:
                hazard_state = "SAFE"
                
            # STAGE 3: Risk Management & Graph-Based Routing
            IF hazard_state == "CRITICAL_JAM":
                TRIGGER safety protocol for ROI
                
                FOR EACH edge IN facility_graph connected to ROI:
                    base_cost = edge.length / edge_props[edge].width
                    
                    IF edge_props[edge].is_funnel_shaped is TRUE:
                        base_cost = base_cost * 0.5
                    IF edge_props[edge].has_pillars is TRUE:
                        base_cost = base_cost * 0.65
                        
                    UPDATE graph weight for edge to (base_cost * crowd_pressure)
                    
                optimal_path = COMPUTE shortest path using Dijkstra algorithm to safe_exits
                BROADCAST optimal_path
                
        previous_frame = F
```
        


## 4. Complexity Analysis
*   **Time Complexity:** O(N^2) for vision grid processing and optical flow per frame; O(V log V + E) for Dijkstra’s priority queue pathfinding.
*   **Space Complexity:** O(V + E) for maintaining the dynamic facility graph alongside O(N) for real-time bounding box arrays and frame buffers.

## 5. Cross-Disciplinary Research Justification
*   **AI & Kinematics:** Spontaneous foot-movement synchronization acts as a critical predictive indicator of a jammed regime before physical crushing occurs.
*   **Dimensionless Classification:** The Intrusion and Avoidance numbers successfully delineate distinct crowd flow regimes mathematically, moving away from purely density-based triggers.
*   **Cognitive Failure Mitigation:** By applying visual heuristics[cite: 2], the graph actively penalizes standard bottlenecks and routes traffic toward geometries proven to relieve crowd turbulence, like funnels and pillars[cite: 1].