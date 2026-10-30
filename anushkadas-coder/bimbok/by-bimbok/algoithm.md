
```
ALGORITHM Adaptive_Robot_Lane_Formation

INPUT:
    Pedestrian positions and velocities
    Robot positions
    Exit geometry
    Walkable area

INITIALIZE:
    Robot positions
    Density threshold
    Arch detection threshold
    Safety constraints
    Best observed configuration

WHILE evacuation is not complete:

    1. OBSERVE CROWD
       Get pedestrian positions and velocities
       Get current robot positions

    2. ESTIMATE CROWD CONDITIONS
       Calculate local density using Voronoi cells
       Calculate pedestrian flow near the exit
       Detect possible arch formation
       Identify congested regions

    3. DECIDE ROBOT ACTIONS

       IF crowd density is low AND flow is stable:
           Maintain current robot positions

       ELSE:
           Generate candidate robot movements
           FOR each candidate movement:
               Estimate its effect on:
                   Crowd density
                   Exit flow
                   Arch formation
                   Robot obstruction
                   Safety risk

           Select the candidate with the lowest
           predicted congestion cost

    4. SAFETY CHECK
       IF selected movement violates safety constraints:
           Reject movement
           Choose a safe alternative or hold position
       ELSE:
           Execute the selected movement

    5. EVALUATE OUTCOME
       Measure the new crowd conditions
       Compare them with previous conditions

       IF congestion decreases AND safety is maintained:
           Keep the improved configuration
       ELSE:
           Reconsider the robot positions

    6. UPDATE KNOWLEDGE
       Store observed state, action, next state,
       and resulting cost for future improvement

END WHILE

OUTPUT:
    Evacuation time
    Average pedestrian throughput
    Arch frequency and duration
    Peak crowd density
    Safety violations
```




```py
import numpy as np def bottleneck_guard_control_loop(): """ Main real-time control loop for the exit-guard robot combining Voronoi density monitoring, clogging detection, MAIRL intent prediction, and SICNav bilevel MPC with transverse oscillation. """ # Initialize system parameters We = 1.0 # Exit width (meters)[cite: 28] Wh = 0.35 # Average human width (meters) t_min = 1.0 # Clog time cutoff (seconds) last_crossing_time = current_timestamp() while robot_system.is_active(): # 1. Perception & State Estimation crowd_state = overhead_camera.get_tracked_agents() #[cite: 31] Voronoi_cells = compute_voronoi_diagram(crowd_state) #[cite: 29, 30] local_density = compute_voronoi_density(crowd_state, Voronoi_cells) #[cite: 30] # 2. Check Clog Status at Exit Line current_time = current_timestamp() tau = current_time - last_crossing_time if agent_crossed_exit_line(): last_crossing_time = current_time tau = 0.0 arch_detected = (tau > t_min) or (local_density > D_crit) # 3. Predict Pedestrian Intent (MAIRL / anushka3) predicted_human_trajectories = mairl_predict_intent(crowd_state) #[cite: 1] # 4. Compute Robot Control (SICNav Bilevel MPC + Oscillation) if arch_detected: # Engage active defense: Position at exit threshold with lateral oscillation target_velocity = compute_oscillating_guard_trajectory( crowd_state, predicted_human_trajectories, amplitude=0.4, frequency=1.2 ) #[cite: 31] else: # Standard smooth transit or holding pattern target_velocity = compute_nominal_mpc_plan(crowd_state, predicted_human_trajectories) # Execute control command via low-level controller robot_system.execute(target_velocity) def compute_voronoi_density(agents, cells): """Computes local density Dv = N / sum(|Ai|)[cite: 30].""" N = len(agents) total_area = sum(cell.area for cell in cells) return N / max(total_area, 1e-5)
```



You're absolutely right. The previous pseudocode described what the system should do, but functions such as calculate_density_near_exit(), analyze_crowd_geometry_and_motion(), and choose_tested_arch_prevention_action() need concrete definitions before anyone can implement the algorithm. Let's make the algorithm implementable, step by step, using your uploaded document as the foundation. I'll distinguish what your document specifies from the additional formulas and rules proposed here, so we don't accidentally present our design choices as findings from your reference papers. arch-research.pdf 1. Define the system's inputs first Your overhead camera provides the pedestrian positions; the exit geometry and robot positions are known or estimated. At each observation step, represent every pedestrian as a point: \[ P_i=(x_i,y_i) \] The complete set of positions at time \(t\) is: \[ P(t)=\{P_1,P_2,\ldots,P_N\} \] Track the same pedestrians between consecutive observations so their movement can be estimated. Also record the exit width \(W_e\), observation interval \(\Delta t\), and robot positions. Your notes mention average pedestrian width \(w_h\), a congestion threshold, throughput, Voronoi cells, and robot oscillation. They do not provide a complete formula for every function below, so those formulas are proposed implementation choices. 2. Function-by-function definitions Function 1 — detect_and_track_pedestrians() Purpose: Detect pedestrians in the camera image and maintain their positions over time. Working: 1. Receive the latest camera frame. 2. Detect each pedestrian's bounding box or centre point. 3. Convert image coordinates into floor coordinates using camera calibration. 4. Match each detection with a pedestrian from the previous frame. 5. Return each pedestrian's position and track ID. Output: pedestrians = [ {"id": 1, "x": 2.1, "y": 3.4}, {"id": 2, "x": 2.5, "y": 3.8},] The numbers above are illustrative, not values from your paper. Important: Your document specifies an overhead camera, but does not specify a pedestrian-detection or tracking model. That model must be selected and tested separately. Function 2 — construct_voronoi(pedestrian_positions) Purpose: Estimate the local space associated with each pedestrian, as illustrated on page 4 of your document. arch-research.pdf A Voronoi cell for pedestrian \(i\) consists of points closer to pedestrian \(i\) than to any other pedestrian. Mathematically, \[ V_i=\{x:\|x-P_i\|\leq\|x-P_j\|, \ \forall j\ne i\} \] In practice, calculate these cells inside a bounded observation region. Clip each cell to that region; otherwise, cells at the edge may extend indefinitely. def construct_voronoi(positions, observation_region): cells = compute_voronoi_tessellation(positions) clipped_cells = [] for cell in cells: clipped_cells.append( intersection(cell, observation_region) ) return clipped_cells Here, compute_voronoi_tessellation() and intersection() are geometric operations that must be implemented using a geometry library or equivalent code. Function 3 — calculate_density_near_exit(voronoi_cells) Purpose: Determine whether the crowd is becoming densely packed near the exit. Define a fixed observation region \(R_e\) immediately upstream of the exit. Let: - \(N_e\) = number of pedestrians inside \(R_e\) - \(A_e\) = area of \(R_e\), in square metres Then the regional density is \[ \boxed{\rho_e=\frac{N_e}{A_e}} \] The output unit is pedestrians per square metre. Voronoi cells can also provide individual density estimates: \[ \rho_i=\frac{1}{|V_i\cap R_e|} \] provided the intersection has positive area and the pedestrian is associated with the region. def calculate_density_near_exit(positions, exit_region): pedestrians_inside = [ p for p in positions if exit_region.contains(p) ] area = exit_region.area if area <= 0: return None return len(pedestrians_inside) / area This implementation calculates regional density directly. The Voronoi calculation remains useful for understanding local crowd spacing and identifying unusually compressed regions. How do we decide whether density is high? Use a threshold \(\rho_{\text{high}}\), calibrated through experiments or simulation. Your notes mention a threshold, but do not supply its numerical value. Do not choose a threshold arbitrarily and claim that it comes from the paper. Function 4 — count_exit_crossings() Purpose: Count the pedestrians who have passed through the exit. Define a virtual line across the exit. For each pedestrian, compare their position at the current observation with their position at the previous observation. Count a crossing only when the pedestrian moves from the inside side of the line to the outside side, with the crossing occurring within the usable exit opening. def count_exit_crossings(previous, current, exit_line): crossings = 0 for person_id in common_ids(previous, current): old_position = previous[person_id] new_position = current[person_id] if crossed_outward( old_position, new_position, exit_line ): if crossing_is_within_exit(new_position): crossings += 1 return crossings The functions crossed_outward() and crossing_is_within_exit() are geometric checks, not built-in Python functions. To calculate throughput over a time interval: \[ \boxed{T=\frac{q}{\Delta t}} \] where \(q\) is the number of unique outward crossings in the interval. For example, if 12 pedestrians cross in 4 seconds, the measured throughput is 3 pedestrians per second. This is just an example. Function 5 — throughput_is_decreasing(throughput_history) Purpose: Identify a sustained reduction in exit flow. A single low measurement may be caused by normal variation. Therefore, compare throughput over two recent windows. Let \(T_{\text{recent}}\) be the mean throughput in the recent window and \(T_{\text{previous}}\) the mean in the preceding window: \[ T_r=\frac{1}{m}\sum_{j=1}^{m}T_j \] Declare a decline if \[ T_r < T_p-\delta_T \] where \(\delta_T\) is a minimum meaningful decline selected during calibration. def throughput_is_decreasing(history, window, delta): if len(history) < 2 * window: return False previous = history[-2 * window:-window] recent = history[-window:] previous_mean = mean(previous) recent_mean = mean(recent) return recent_mean < previous_mean - delta The history must use consistent time intervals. If very few pedestrians cross during a short window, throughput measurements may be noisy; increase the window or account for measurement uncertainty. Function 6 — analyze_crowd_geometry_and_motion() Purpose: Determine whether a compact arch is developing rather than relying on density alone. This is the most important missing function in the original pseudocode. A dense crowd is not necessarily an arch, so the function should analyse both geometry and movement. A practical first version can calculate three features: 1. Arch geometry: How closely the positions of pedestrians near the exit fit a curved arrangement spanning the exit approach. 2. Compactness: How close those pedestrians are to one another. 3. Movement slowdown: Whether their speeds have remained low for a sustained interval. Estimate each pedestrian's speed from successive positions: \[ v_i(t)= \frac{\|P_i(t)-P_i(t-\Delta t)\|}{\Delta t} \] For geometry, select pedestrians in a defined band upstream of the exit, fit a candidate arc or curve to their positions, and measure the fitting error. A low error alone is not sufficient: the candidate curve must also span a meaningful part of the exit approach and represent a plausible arch configuration. A possible output is: { "arc_fit_error": ..., "arch_span_ratio": ..., "mean_speed": ..., "compactness": ...} These features are proposed; your document does not provide their equations or validated cut-offs. Function 7 — classify_arch_risk(arch_features) Purpose: Convert the measured features into a risk category. For a first prototype, use a transparent rule-based classifier instead of immediately training a neural network. For example: def classify_arch_risk(features, thresholds): geometry_matches = ( features["arc_fit_error"] <= thresholds["max_arc_error"] and features["arch_span_ratio"] >= thresholds["min_arch_span"] ) crowd_is_slow = ( features["mean_speed"] <= thresholds["max_mean_speed"] ) crowd_is_compact = ( features["compactness"] >= thresholds["min_compactness"] ) if geometry_matches and crowd_is_slow and crowd_is_compact: return "HIGH" if geometry_matches and ( crowd_is_slow or crowd_is_compact ): return "MEDIUM" return "LOW" The thresholds must be calibrated using annotated video or simulated scenarios. Also require the condition to persist over multiple observations, so the robot does not react to a single misleading frame. Research caution: This is an initial engineering heuristic, not a validated arch-detection method from the papers in your notes. Function 8 — choose_tested_arch_prevention_action() Purpose: Select a robot movement that discourages compact arch formation without obstructing the exit. The robot should not move randomly. Define a set of candidate actions that have already been tested in simulation, such as: - Hold its current position. - Move a small distance laterally to the left. - Move a small distance laterally to the right. - Perform a bounded lateral oscillation. For each candidate, estimate the effect on crowd flow and arch risk using a validated simulator. Reject any action that violates the robot's movement limits or blocks the required exit clearance. A simple initial selection rule is: def choose_tested_arch_prevention_action( state, candidate_actions, simulator): best_action = "HOLD" best_score = float("-inf") for action in candidate_actions: if not satisfies_safety_constraints(state, action): continue predicted = simulator.predict(state, action) score = ( predicted.throughput_score - predicted.arch_risk_penalty - predicted.crowding_penalty ) if score > best_score: best_score = score best_action = action return best_action The simulator and scoring weights must be developed and validated. This function is a proposed action-selection method, not an algorithm stated in your document. The most important constraint is that the robot must not simply force pedestrians into a different bottleneck or cause them to collide with one another. A movement that reduces the arch score but reduces throughput or creates a safety hazard should not be considered successful. Function 9 — choose_small_controlled_repositioning() Purpose: Act before a compact arch has fully formed, if density is high and throughput is falling. A conservative initial rule is to choose a small movement from the same pre-tested candidate set. The robot should not attempt a large repositioning while pedestrians are close to it. def choose_small_controlled_repositioning(state): if not robot_has_safe_clearance(state): return "HOLD" if not candidate_positions_are_safe(state): return "HOLD" return select_prevalidated_small_movement(state) The clearance and safe-position checks require the robot dimensions, pedestrian positions, robot dynamics and exit geometry. These parameters are not supplied in the six-page document. Function 10 — safety_constraint_violated() Purpose: Prevent the controller from choosing a dangerous movement. Check, at minimum: - Whether the robot would collide with a pedestrian or another robot. - Whether the robot would obstruct the usable exit. - Whether the proposed movement exceeds the robot's permitted speed or acceleration. - Whether camera tracking has become unreliable. - Whether an emergency stop has been activated. def safety_constraint_violated(state, action=None): if emergency_stop_active(): return True if pedestrian_tracking_unreliable(state): return True if action is not None: if predicts_collision(state, action): return True if blocks_required_exit_clearance(state, action): return True if exceeds_robot_limits(action): return True return False These are proposed safety checks. A real implementation needs an independent safety controller; the arch-prevention algorithm itself should never be the only safeguard. Function 11 — update_policy_using_validated_learning_method() Purpose: Improve the controller based on its observed performance. Your document mentions DRL and ADP on page 5, but does not define the learning algorithm. Therefore, we should not claim that a particular DRL architecture or ADP update equation comes from your source. arch-research.pdf A possible DRL formulation would use: - State: pedestrian positions and velocities, local density, throughput history, arch-risk features and robot positions. - Action: one of the pre-approved robot movements. - Reward: higher throughput and lower arch risk, with penalties for congestion and unsafe actions. An illustrative reward function is \[ r_t = \alpha \,\widetilde T_t -\beta \,A_t -\gamma \,\rho_t -\lambda \,S_t \] where: - \(\widetilde T_t\) is normalised throughput. - \(A_t\) is the measured arch risk. - \(\rho_t\) is local density. - \(S_t\) indicates a safety violation. - \(\alpha,\beta,\gamma,\lambda\) are weights that must be calibrated. This is a proposed reward function, not one given in your notes. Safety violations should be prevented by hard constraints, not merely discouraged by a reward penalty. For initial development, train and evaluate in simulation. Do not let an untrained learning agent experiment freely with a real crowd. 3. How the functions work together Here is the complete control flow with the functions now defined at a practical level. def robot_arch_prevention(): initialize_camera_tracking() initialize_exit_geometry() initialize_robot_controller() while evacuation_is_active(): # 1. Detect and track pedestrians positions = detect_and_track_pedestrians() if positions is None or not tracking_is_reliable(): command_safe_robot_state() continue # 2. Estimate crowd density cells = construct_voronoi( positions, observation_region ) density = calculate_density_near_exit( positions, exit_region ) # 3. Measure exit flow crossings = count_exit_crossings( previous_positions, positions, exit_line ) update_throughput_history(crossings) # 4. Detect potential arch formation features = analyze_crowd_geometry_and_motion() This is a structural prototype: functions such as initialize_camera_tracking(), execute_safe_robot_action() and record_throughput() still require implementation, but their responsibilities are now clear. The density, throughput and arch-detection functions also need their thresholds calibrated before the algorithm can be tested meaningfully. 4. One detail from your notes that should be incorporated On pages 2–3, your document relates the exit width \(w_e\) to average pedestrian width \(w_h\), assumes the robot width \(w_r\) is approximately the same as the pedestrian width, and derives a quantity resembling \[ t_i=\frac{w_e}{w_h}, \qquad \lambda_A\approx\frac{w_e}{2w_h}. \] arch-research.pdf This appears to be an initial estimate for the number or arrangement of robots relative to the exit width. The notes do not define the notation completely enough to establish exactly how \(\lambda_A\) should be used in the controller. I would confirm its intended meaning before incorporating it as a fixed robot-placement formula. My recommendation: Implement the camera tracking, density estimation, throughput calculation and arch detection first. Test them without moving robots. Once you can reliably identify a developing arch, add a threshold-based robot controller, and only then investigate DRL or ADP. That sequence will let you demonstrate which part of the system actually improves pedestrian flow.



```
ALGORITHM: BOTTLENECK GUARD CONTROL

INITIALIZE:
    Exit width
    Average pedestrian width
    Minimum clogging time
    Last crossing time

WHILE robot system is active:

    1. PERCEPTION AND STATE ESTIMATION
       Get tracked pedestrian positions and velocities
       Compute Voronoi cells
       Calculate local crowd density

    2. CLOGGING DETECTION
       Calculate time since the last pedestrian crossed the exit

       IF a pedestrian crosses the exit:
           Update last crossing time
           Reset clogging timer

       IF time since last crossing > minimum clogging time
          OR local density > critical density:
           Set arch_detected = TRUE
       ELSE:
           Set arch_detected = FALSE

    3. PREDICT PEDESTRIAN INTENT
       Predict future pedestrian trajectories
       using the BELLMAN EQUATION intent prediction model

    4. COMPUTE ROBOT CONTROL

       IF arch_detected = TRUE:
           Compute a safe oscillating guard trajectory
           using pedestrian states and predicted trajectories
           Set target velocity from this trajectory

       ELSE:
           Compute the nominal smooth motion plan
           using model predictive control (MPC)
           Set target velocity from this plan

    5. EXECUTE CONTROL
       Send target velocity to the robot's
       low-level controller

END WHILE


FUNCTION COMPUTE_VORONOI_DENSITY(agents, cells):

    N = number of pedestrians
    total_area = sum of Voronoi cell areas

    density = N / maximum(total_area, small_positive_value)

    RETURN density

```


```py
ALGORITHM: ADAPTIVE MULTI-ROBOT CROWD FLOW CONTROL

INPUT:
    Exit width We
    Walkable area and exit geometry
    Live pedestrian tracks from CCTV camera
    Available robots and their movement limits

INITIALIZE:
    Central database
    Independent ADP learning controller for each robot
    Minimum clogging time t_min
    Critical density D_crit
    Robot safety constraints
    Last exit-crossing time
    Previous crowd measurements

WHILE evacuation is active:

    # 1. COLLECT CAMERA AND ROBOT DATA
    pedestrians = camera.get_tracked_pedestrians()
    robot_states = central_system.get_robot_feedback()

    central_database.store(pedestrians, robot_states)

    N = count(pedestrians)

    # 2. ESTIMATE AVERAGE PEDESTRIAN WIDTH
    IF N > 0:
        average_person_area = sum(person_areas) / N

        # Approximate each person's occupied area as a circle.
        equivalent_radius = sqrt(average_person_area / pi)
        average_pedestrian_width = 2 * equivalent_radius

        # Estimate how many pedestrian widths fit across the exit.
        S_ratio = exit_width / average_pedestrian_width
        people_across = floor(S_ratio)

    ELSE:
        average_pedestrian_width = unavailable
        S_ratio = unavailable
        people_across = 0

    # 3. CALCULATE VORONOI DENSITY
    Voronoi_cells = compute_voronoi_diagram(pedestrians)
    Voronoi_cells = clip_cells_to_walkable_area(Voronoi_cells)

    total_cell_area = sum(area(cell) for cell in Voronoi_cells)

    IF total_cell_area > 0:
        density = N / total_cell_area
    ELSE:
        density = unavailable

    # 4. CALCULATE EXIT THROUGHPUT
    current_time = get_current_time()
    crossing_count = count_exit_crossings(previous_time, current_time)
    delta_t = current_time - previous_time

    IF delta_t > 0:
        throughput = crossing_count / delta_t
    ELSE:
        throughput = 0

    # 5. DETECT CLOGGING
    IF pedestrian_crossed_exit:
        last_crossing_time = current_time

    time_since_crossing = current_time - last_crossing_time

    IF time_since_crossing > t_min OR density > D_crit:
        clog_detected = TRUE
    ELSE:
        clog_detected = FALSE

    # 6. DECIDE WHETHER ROBOTS ARE NEEDED
    IF no_pedestrians_are_approaching:
        keep robots in safe holding positions

    ELSE IF people_across <= 2:
        evaluate whether robots would improve flow
        without unnecessarily dividing the exit

    ELSE:
        determine useful number of robots
        based on exit geometry and crowd conditions

        IF clog_detected OR congestion_is_increasing:
            select robots for deployment
        ELSE:
            maintain safe positions and continue monitoring

    # 7. SHARE INFORMATION THROUGH THE CENTRAL SYSTEM
    central_database.store(
        density,
        throughput,
        S_ratio,
        people_across,
        clog_detected,
        robot_states
    )

    central_system.share_crowd_information()
    central_system.share_robot_positions()
    central_system.share_coordination_information()

    # 8. EACH ROBOT SELECTS ITS OWN ACTION
    FOR each deployed robot r:

        state = robot_r.observe(
            local_state,
            shared_crowd_information,
            other_robot_positions,
            previous_action_outcome
        )

        # The robot's own ADP controller selects its action.
        action = robot_r.ADP.select_action(state)

        # Reject actions that could cause collisions or block the exit.
        IF action_is_unsafe(
            action,
            pedestrian_positions,
            other_robot_positions,
            exit_geometry
        ):
            action = select_safe_alternative_or_hold()

        robot_r.execute(action)

        central_system.receive_robot_feedback(
            robot_r.position,
            robot_r.action,
            robot_r.status
        )

    # 9. EVALUATE THE RESULT
    new_pedestrian_state = camera.get_tracked_pedestrians()
    new_density = calculate_voronoi_density(new_pedestrian_state)
    new_throughput = calculate_exit_throughput()
    new_clog_status = detect_clogging()

    # 10. STORE EXPERIENCE AND UPDATE LEARNING
    FOR each robot r:

        reward = evaluate_outcome(
            previous_throughput,
            new_throughput,
            previous_density,
            new_density,
            previous_clog_status,
            new_clog_status,
            safety_violations
        )

        central_database.store_experience(
            previous_state,
            action,
            reward,
            new_state
        )

        robot_r.ADP.update_learning_model(stored_experience)

    # 11. UPDATE THE SYSTEM FOR THE NEXT CYCLE
    update_previous_measurements()
    update_robot_positions()
    update_shared_information()

END WHILE

OUTPUT:
    Total evacuation time
    Throughput history
    Density history
    S-ratio history
    Robot deployment and movement history
    Arch and clogging statistics
    Learning history
    Safety violations
```

```py
ALGORITHM: PREDICTIVE MULTI-ROBOT CROWD FLOW CONTROL

INPUT:
    Exit width We
    Defined upstream observation region
    CCTV pedestrian tracking data
    Available robots and their movement limits
    Prediction time window T
    Safe movement constraints

INITIALIZE:
    Central database
    Independent ADP controller for each robot
    Previous exit-crossing time
    Initial robot positions
    Learning parameters

WHILE evacuation is active:

    # 1. OBSERVE THE APPROACHING CROWD
    pedestrians = camera.get_tracked_pedestrians(
        upstream_region
    )

    robot_states = central_system.get_robot_feedback()

    N_approaching = count(pedestrians)

    central_database.store(pedestrians, robot_states)

    # 2. ESTIMATE AVERAGE PEDESTRIAN AREA AND WIDTH
    IF N_approaching > 0:

        average_person_area =
            sum(measured_person_areas) / N_approaching

        # Approximate occupied pedestrian area as a circle.
        equivalent_radius =
            sqrt(average_person_area / pi)

        average_pedestrian_width =
            2 * equivalent_radius

        # Estimate how many people fit across the exit.
        people_across =
            floor(We / average_pedestrian_width)

    ELSE:
        people_across = 0
        average_pedestrian_width = unavailable

    # 3. ESTIMATE THEORETICAL EXIT CAPACITY
    # Use the estimated passage time for a defined group.
    # Q_exit is measured in pedestrians per second.

    IF people_across > 0:
        Q_exit = estimate_exit_capacity(
            people_across,
            estimated_group_passage_time
        )
    ELSE:
        Q_exit = 0

    # 4. PREDICT ARRIVING PEDESTRIANS
    # Estimate how many pedestrians will reach the exit
    # during the selected prediction time window.

    predicted_arrivals = predict_arrivals(
        pedestrians,
        pedestrian_velocities,
        T
    )

    predicted_exit_capacity = Q_exit * T

    # 5. DECIDE WHETHER TO DEPLOY ROBOTS
    IF predicted_arrivals > predicted_exit_capacity:

        required_robots = determine_useful_robot_count(
            people_across,
            predicted_arrivals,
            predicted_exit_capacity,
            robot_states
        )

        select available robots for deployment

        # Ensure robots can reach their positions in time.
        IF robots_can_reach_positions_before_arrival:

            send deployment information to selected robots

        ELSE:
            choose a safe feasible deployment
            or continue monitoring

    ELSE:
        keep robots in safe holding positions
        continue monitoring the approaching crowd

    # 6. CALCULATE ACTUAL CROWD DENSITY
    Voronoi_cells = compute_voronoi_diagram(pedestrians)

    Voronoi_cells = clip_cells_to_walkable_area(
        Voronoi_cells
    )

    total_cell_area =
        sum(area(cell) for cell in Voronoi_cells)

    IF total_cell_area > 0:
        density = N_approaching / total_cell_area
    ELSE:
        density = unavailable

    # 7. MEASURE ACTUAL EXIT THROUGHPUT
    current_time = get_current_time()

    crossing_count = count_exit_crossings(
        previous_time,
        current_time
    )

    delta_t = current_time - previous_time

    IF delta_t > 0:
        actual_throughput = crossing_count / delta_t
    ELSE:
        actual_throughput = 0

    # 8. SHARE INFORMATION THROUGH THE CENTRAL SYSTEM
    central_database.store(
        predicted_arrivals,
        predicted_exit_capacity,
        density,
        actual_throughput,
        robot_states
    )

    central_system.share_crowd_information()
    central_system.share_robot_positions()
    central_system.share_coordination_information()

    # 9. EACH DEPLOYED ROBOT DECIDES INDEPENDENTLY
    FOR each deployed robot r:

        state = robot_r.observe(
            local_state,
            shared_crowd_information,
            other_robot_positions,
            previous_action_outcome
        )

        action = robot_r.ADP.select_action(state)

        # Check safety before executing the action.
        IF action_is_unsafe(
            action,
            pedestrian_positions,
            other_robot_positions,
            exit_geometry
        ):
            action = select_safe_alternative_or_hold()

        robot_r.execute(action)

        central_system.receive_robot_feedback(
            robot_r.position,
            robot_r.action,
            robot_r.status
        )

    # 10. EVALUATE RESULTS AND UPDATE LEARNING
    new_throughput = measure_exit_throughput()
    new_density = calculate_crowd_density()
    safety_status = check_safety_constraints()

    FOR each robot r:

        reward = evaluate_outcome(
            previous_throughput,
            new_throughput,
            previous_density,
            new_density,
            safety_status
        )

        central_database.store_experience(
            previous_state,
            action,
            reward,
            new_state
        )

        robot_r.ADP.update_learning_model(
            stored_experience
        )

    # 11. PREPARE FOR THE NEXT CYCLE
    update_previous_measurements()
    update_robot_positions()
    update_shared_information()

END WHILE

OUTPUT:
    Total evacuation time
    Predicted and actual throughput
    Pedestrian density history
    Estimated exit capacity
    Robot deployment and movement history
    Arch and clogging statistics
    Learning history
    Safety violations
```



```
ALGORITHM: PREDICTIVE MULTI-ROBOT CROWD FLOW CONTROL

INPUT:
    Exit width We
    Upstream observation region
    CCTV pedestrian tracking data
    Available robots and their dimensions
    Robot movement limits
    Prediction time window T
    Safe movement constraints

INITIALIZE:
    Central database
    Independent ADP controller for each robot
    Initial robot positions
    Previous time and exit-crossing count
    Learning parameters
    Minimum clogging time t_min
    Critical density D_crit

WHILE evacuation is active:

    # 1. OBSERVE THE APPROACHING CROWD

    pedestrians = camera.get_tracked_pedestrians(
        upstream_region
    )

    robot_states = central_system.get_robot_feedback()

    N_approaching = count(pedestrians)

    central_database.store(pedestrians, robot_states)


    # 2. ESTIMATE PEDESTRIAN AREA AND WIDTH

    IF N_approaching > 0:

        average_person_area =
            sum(measured_person_areas) / N_approaching

        # Approximate each pedestrian's occupied area as a circle.
        equivalent_radius =
            sqrt(average_person_area / pi)

        average_pedestrian_width =
            2 * equivalent_radius

        # Estimate how many pedestrian widths fit across the exit.
        people_across =
            floor(We / average_pedestrian_width)

    ELSE:

        average_person_area = unavailable
        average_pedestrian_width = unavailable
        people_across = 0


    # 3. ESTIMATE THEORETICAL EXIT CAPACITY

    IF people_across > 0:

        Q_exit = estimate_exit_capacity(
            people_across,
            estimated_group_passage_time
        )

    ELSE:

        Q_exit = 0


    # 4. PREDICT PEDESTRIAN ARRIVALS

    predicted_arrivals = predict_arrivals(
        pedestrians,
        pedestrian_velocities,
        prediction_window = T
    )

    predicted_exit_capacity = Q_exit * T


    # 5. DECIDE WHETHER ROBOTS ARE NEEDED

    IF predicted_arrivals > predicted_exit_capacity:

        intervention_needed = TRUE

    ELSE:

        intervention_needed = FALSE


    # 6. DETERMINE ROBOT COUNT AND STREAM ARRANGEMENT
    # The proposed rule places robots at selected stream positions.
    # Robots must not obstruct the exit or create unsafe gaps.

    IF intervention_needed == TRUE:

        IF people_across < 3:

            required_robots = 0

        ELSE:

            # Three streams -> one robot.
            # Five streams -> two robots.
            # Generalized initial heuristic for odd stream counts.

            required_robots = floor((people_across - 1) / 2)

            required_robots = min(
                required_robots,
                number_of_available_robots
            )

        IF required_robots > 0:

            # Select approximately evenly spaced positions.
            # For 3 streams, select the middle position.
            # For 5 streams, select positions 2 and 4.

            selected_positions = select_robot_stream_positions(
                people_across,
                required_robots
            )

            FOR each selected robot r:

                target_position = calculate_upstream_position(
                    exit_geometry,
                    selected_positions[r],
                    average_pedestrian_width,
                    robot_dimensions
                )

                IF target_position_is_safe(target_position)
                   AND robots_can_reach_positions_before_arrival:

                    central_system.send_target_position(
                        robot_r,
                        target_position
                    )

                ELSE:

                    keep robot in a safe position
                    do not force deployment

    ELSE:

        required_robots = 0

        keep robots in safe standby positions


    # 7. CALCULATE CROWD DENSITY

    Voronoi_cells = compute_voronoi_diagram(pedestrians)

    Voronoi_cells = clip_cells_to_walkable_area(
        Voronoi_cells
    )

    total_cell_area =
        sum(area(cell) for cell in Voronoi_cells)

    IF total_cell_area > 0:

        density = N_approaching / total_cell_area

    ELSE:

        density = unavailable


    # 8. MEASURE ACTUAL EXIT THROUGHPUT

    current_time = get_current_time()

    crossing_count = count_exit_crossings(
        previous_time,
        current_time
    )

    delta_t = current_time - previous_time

    IF delta_t > 0:

        actual_throughput = crossing_count / delta_t

    ELSE:

        actual_throughput = 0


    # 9. DETECT EXIT CLOGGING

    IF pedestrian_crossed_exit:

        last_crossing_time = current_time

    time_since_crossing = current_time - last_crossing_time

    IF time_since_crossing > t_min OR density > D_crit:

        clog_detected = TRUE

    ELSE:

        clog_detected = FALSE


    # 10. SHARE INFORMATION THROUGH THE CENTRAL SYSTEM

    central_database.store(
        predicted_arrivals,
        predicted_exit_capacity,
        density,
        actual_throughput,
        clog_detected,
        robot_states
    )

    central_system.share_crowd_information()
    central_system.share_robot_positions()
    central_system.share_target_positions()
    central_system.share_coordination_information()


    # 11. EACH ROBOT MAKES ITS OWN MOVEMENT DECISION

    FOR each deployed robot r:

        state = robot_r.observe(
            local_robot_state,
            shared_crowd_information,
            other_robot_positions,
            previous_action_outcome
        )

        # The robot's own ADP controller selects its action.

        action = robot_r.ADP.select_action(state)

        # Safety constraints override the learned action.

        IF action_is_unsafe(
            action,
            pedestrian_positions,
            other_robot_positions,
            exit_geometry
        ):

            action = select_safe_alternative_or_hold()

        robot_r.execute(action)

        central_system.receive_robot_feedback(
            robot_r.position,
            robot_r.action,
            robot_r.status
        )


    # 12. EVALUATE THE RESULT AND UPDATE LEARNING

    new_throughput = measure_exit_throughput()
    new_density = calculate_crowd_density()
    safety_status = check_safety_constraints()

    FOR each robot r:

        reward = evaluate_outcome(
            previous_throughput,
            new_throughput,
            previous_density,
            new_density,
            safety_status
        )

        central_database.store_experience(
            previous_state,
            action,
            reward,
            new_state
        )

        robot_r.ADP.update_learning_model(
            stored_experience
        )


    # 13. UPDATE SYSTEM STATE

    update_previous_measurements()
    update_robot_positions()
    update_shared_information()
    update_previous_time
```