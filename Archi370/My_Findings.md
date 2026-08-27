ALGORITHM: MK-AGD_Evacuation_Engine

INPUTS:
  - video_stream: Real-time CCTV stream of ROI
  - tracking_model: Combined YOLOv9 + ByteTrack detector
  - facility_graph: NetworkX Directed Graph G(V, E) of facility nodes and corridor edges
  - edge_properties: Dictionary containing geometric features {length, width, has_pillar, is_funnel}
  - safe_exits: Set of target destination nodes

OUTPUTS:
  - dynamic_hazard_state: Enum [FREE_FLOW, SLOW_REGIME, TURBULENT_CRUSH_RISK]
  - optimal_evacuation_path: Node sequence array leading to safe egress

BEGIN PIPELINE

  Initialize previous_bounding_boxes to NULL
  Initialize frame_history_buffer to List[]

  FOR EACH frame F IN video_stream DO:
     # =========================================================================
      # STAGE 1: VISION PROCESSING & KINEMATIC FEATURE EXTRACTION
      # =========================================================================
      # RESEARCH JUSTIFICATION: Counting alone fails to predict crowd crushes. We extract
      # precise 2D foot-level coordinates to observe micro-movement anomalies (e.g., foot-sway
      # synchronization) that occur before macro-forward motion drops to zero.
      
   detected_pedestrians = DETECT_HUMANS(F, tracking_model)
      pedestrian_count = LENGTH(detected_pedestrians)
      density_rho = pedestrian_count / AREA(ROI)

  IF previous_bounding_boxes IS NOT NULL THEN
          # Track individual optical flow vectors (v_x, v_y) for each detected person
          velocity_vectors = COMPUTE_TRACKED_VELOCITIES(detected_pedestrians, previous_bounding_boxes)
          avg_velocity = MEAN(velocity_vectors)
          vel_variance = VARIANCE(velocity_vectors)

  # Extract foot-level lateral movement (X-axis displacement history)
  frame_history_buffer.APPEND(GET_ANKLE_KEYPOINTS(detected_pedestrians))
          
  # Calculate Kuramoto Order Parameter (S_phase) for micro-sway phase locking
  # S_phase measures synchronization of lateral body movements (0 = uncorrelated, 1 = total lock-step)
  step_sync_S = COMPUTE_HILBERT_PHASE_SYNCHRONIZATION(frame_history_buffer)
      END IF

  # =========================================================================
  # STAGE 2: DIMENSIONLESS COGNITIVE & TURBULENCE METRICS
  # =========================================================================
  # RESEARCH JUSTIFICATION: Crowds experience physical "phase changes" similar to fluid turbulence.
  # Crowd Pressure models physical force transferred through human contact.
  # Avoidance (A_num) and Intrusion (I_num) measure psychological stress as personal space collapses.
      
  # 1. Physical Dynamic Crowd Pressure (P_crowd)
  crowd_pressure = density_rho * vel_variance

  # 2. Cognitive Intrusion Index (I_num)
  # Ratio of physical spatial occupancy against velocity decay (Personal space collapse)
  normalized_speed = MAX(avg_velocity / DESIRED_WALKING_SPEED, 0.01)
      intrusion_num = density_rho / (normalized_speed^2)

  # 3. Kinematic Avoidance Index (A_num)
  # Measures lateral velocity deviation forced by localized spatial collisions
  avoidance_num = COMPUTE_LATERAL_FLUID_DEVIATION(velocity_vectors)

  # =========================================================================
  # STAGE 3: TRI-PHASE HAZARD STATE CLASSIFICATION
  # =========================================================================
  # RESEARCH JUSTIFICATION: High foot-step synchronization (S_phase >= 0.75) combined 
  # with high lateral avoidance indicates pedestrians are stumbling and pushing laterally. 
  # This provides an early warning prior to total mechanical clogging.

  IF step_sync_S >= 0.75 OR crowd_pressure >= 4.5 OR (avoidance_num >= 0.8 AND density_rho > 3.5) THEN
          dynamic_hazard_state = "TURBULENT_CRUSH_RISK"
      ELSE IF intrusion_num > 4.0 AND avg_velocity < 0.6 THEN
          dynamic_hazard_state = "SLOW_REGIME"
      ELSE
          dynamic_hazard_state = "FREE_FLOW"
      END IF

  # =========================================================================
  # STAGE 4: ARCHITECTURE-AWARE GRAPH WEIGHTING & ROUTING
  # =========================================================================
  # RESEARCH JUSTIFICATION: Simple shortest-path algorithms (like raw Dijkstra) push crowds
  # into already congested corridors. By applying a non-linear weight function, we penalize 
  # bottleneck pressure while prioritizing architectural geometries that naturally absorb shock waves.

  IF dynamic_hazard_state != "FREE_FLOW" THEN
          
  FOR EACH edge(u, v) IN facility_graph DO:
              props = edge_properties[edge]
              
  # Base Traversal Cost (Length-to-Width ratio)
  base_cost = props.length / MAX(props.width, 0.5)

  # ARCHITECTURAL RELIEF FACTORS:
  # - Funnel geometries reduce compression shockwaves at bottlenecks.
  # - Asymmetric pillars placed near exits deflect crowd pressure forces,
  #   preventing physical arching (clogging) above critical bottlenecks.
  arch_modifier = 1.0
              IF props.is_funnel_shaped IS TRUE THEN
                  arch_modifier = arch_modifier * 0.75
              END IF
              IF props.has_asymmetric_pillar IS TRUE THEN
                  arch_modifier = arch_modifier * 0.60
              END IF

  # DYNAMIC KINEMATIC PENALTY:
  # Exponential friction penalty based on local physical pressure and stress metrics
  dynamic_penalty = (1.0 + (3.5 * density_rho^2)) * (1.0 + (2.0 * intrusion_num))

  # Compute final dynamic weight for graph edge
  edge_weight = base_cost * arch_modifier * dynamic_penalty
              UPDATE_GRAPH_EDGE_WEIGHT(facility_graph, edge(u, v), edge_weight)
          END FOR

  Route crowd via shortest path over dynamically weighted graph
          optimal_evacuation_path = DIJKSTRA_SHORTEST_PATH(facility_graph, START_NODE=ROI, TARGET_NODES=safe_exits)
          
  BROADCAST_GUIDANCE(optimal_evacuation_path, dynamic_hazard_state)
      END IF

  previous_bounding_boxes = detected_pedestrians
  END FOR

END PIPELINE
