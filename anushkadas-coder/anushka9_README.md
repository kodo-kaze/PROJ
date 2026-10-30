# anushka9 — Stability and Comfort in Mobile Robot-Pedestrian Interactions

| | |
|---|---|
| **Citation** | A. Jafari, H.-S. Nguyen, Y.-C. Liu. *Stability and Comfort in Mobile Robot-Pedestrian Interactions.* arXiv:2607.17604v1 [cs.RO] (2026)[cite: 69]. |
| **File** | `anushka9.pdf`[cite: 69] |
| **Type** | Research paper presenting a socially aware navigation framework for Nonholonomic Mobile Robots (NMRs) with formal stability proofs and human-subject evaluations[cite: 69, 70, 71]. |
| **Role in our project** | Provides the theoretical framework for mapping force-based navigation models (SFM and TSFM) onto nonholonomic robotic constraints, stability guarantees via passivity theory, and empirical comfort metrics[cite: 71, 73, 75]. |

---

## 1. TL;DR

1. **Addresses Nonholonomic Constraints:** While most crowd navigation research assumes holonomic (omnidirectional) agents, practical mobile robots are typically Nonholonomic Mobile Robots (NMRs)[cite: 70, 71]. This paper develops a rigorous framework to apply Social Force Models (SFM) to NMRs[cite: 71, 73].
2. **Projected Time-to-Collision Social Force Model (TSFM):** Introduces TSFM, which incorporates Projected Time-to-Collision (PTTC) directly into the social repulsive force formulation to explicitly optimize pedestrian subjective comfort[cite: 71, 72, 75].
3. **Formal Stability Proofs:** Establishes system stability using port-Hamiltonian passivity analysis and Lyapunov theory under the key assumption of "boundedly non-passive" pedestrian behaviors[cite: 71, 75, 78].
4. **Extensive Empirical Validation:** Calibrates models via simulation and validates performance through human-subject experiments (32 participants, 480 trials) comparing SFM, TSFM, and remote-controlled baselines using kinematic data and survey questionnaires[cite: 71, 79, 81].

---

## 2. Definitions & Metrics We Will Reuse

| Term | Meaning in the paper | Our project meaning |
|---|---|---|
| **Nonholonomic Mobile Robot (NMR)** | Wheeled mobile robots subject to kinematic constraints preventing lateral motion ($\dot{y}\sin\theta - \dot{x}\cos\theta = 0$)[cite: 71, 73]. | Our physical or simulated wheeled differential-drive robots operating in tight corridors. |
| **Projected Time-to-Collision (PTTC / $t_{c,j}$)** | The time remaining until collision based on the projection of relative velocity onto the line-of-sight distance vector ($t_{c,j} = \frac{\|\vec{d}_j\|^2}{\vec{d}_j \cdot \vec{v}_j}$)[cite: 72]. | Our real-time metric for measuring impending closeness and discomfort relative to moving humans. |
| **TSFM (Time-to-Collision Social Force Model)** | A navigation model replacing standard distance-based social forces with exponential PTTC decay terms ($\vec{f}_{soc,j} = \alpha_T \exp(-T_j / \beta_T) \vec{e}_j$)[cite: 75]. | Our advanced navigation policy prioritizing temporal safety margins over static spatial buffers. |
| **Bounded Non-Passivity** | An assumption limiting the maximum energy injected into the system by pedestrians and boundaries ($\int z^T B_r \vec{f} d\tau \le E_{max}$)[cite: 75, 76, 78]. | Our mathematical guarantee that unpredictable human movements won't destabilize the robot control loop. |

---

## 3. Key Frameworks & Mathematical Formulations

### A. Nonholonomic Virtual Force Framework
- Instead of treating the robot as a centered mass, virtual forces ($\vec{F}$) from SFM/TSFM are mapped into local robot coordinates ($F_m, F_n$) to respect differential-drive kinematics[cite: 71, 73, 74].
- The robot's closed-loop dynamics react to virtual forces as if acted upon by equivalent physical forces, ensuring kinematically feasible trajectories[cite: 73, 74].

### B. Stability Analysis (Passivity & Lyapunov)
- **Theorem 1 & 2:** Proves asymptotic stability and Uniform Ultimate Boundedness (UUB) for the robot's velocity states under constant and varying desired speeds ($v_{des}$)[cite: 75, 77, 78].
- **Theorem 3, 4 & 5:** Models the robot-pedestrian-boundary network as a port-Hamiltonian system. Assuming pedestrians are boundedly non-passive (Assumption 1), the total system Lyapunov energy $V(t) \le V(0) + E$ remains strictly bounded, proving overall system stability[cite: 75, 76, 78].

### C. Hybrid Cost Function Calibration
- Models are calibrated by maximizing a hybrid cost function $I_Q = I_t + I_v$[cite: 77, 78]:
  - **Comfort Metric ($I_t$):** Based on minimum PTTC ($T_p$) during the encounter: $I_t = 1 - \exp(-T_p)$[cite: 77, 78].
  - **Speed Metric ($I_v$):** Normalized average robot speed inside a 5-meter influence zone ($\mathcal{I}_z$)[cite: 78, 79].

---

## 4. Experimental Highlights & Findings

- **Test Setup:** Evaluated on an Agilex Scout Mini (converted to differential-drive) equipped with a Zedx stereo depth camera and 3D LiDAR in indoor hallways and outdoor sidewalks[cite: 79, 80].
- **Kinematic Results:** TSFM achieved a significantly higher minimum PTTC ($T_p = 1.01$ s) and greater minimum avoidance distance ($DH_{min} = 0.99$ m) compared to SFM ($T_p = 0.58$ s, $DH_{min} = 0.73$ m)[cite: 80, 81].
- **Subjective Survey Ratings (Q1–Q4 Likert Scale):** 
  - Pedestrians reported significantly higher comfort with TSFM compared to SFM[cite: 81, 82].
  - Human-operated remote baselines (R14, R28) scored higher on movement smoothness, revealing that **movement smoothness** is an essential factor alongside distance and PTTC for optimal human comfort[cite: 82].

---

## 5. How We Use It (Concrete Items)

**U1. Implement nonholonomic force mapping.** Use Jafari et al.'s kinematic transformation matrix ($J$ and $M_r$) when deploying SFM or TSFM on differential-drive robots to prevent impossible lateral velocity commands[cite: 73, 74].

**U2. Adopt PTTC-based social forces.** Replace standard distance-based social repulsion with TSFM formulation ($T_j$) to generate human-like deceleration behavior and improve pedestrian subjective safety[cite: 75].

**U3. Apply passivity-based stability checks.** Use the port-Hamiltonian energy bounding approach ($\int z^T B_r \vec{f} d\tau \le E_{max}$) in safety critiques to ensure multi-agent crowd interactions remain bounded[cite: 75, 76, 78].

---

## 6. Limitations & Considerations

- The current framework does not incorporate explicit pedestrian trajectory prediction; integrating prediction models could further enhance motion smoothness and PTTC horizons[cite: 84].
- Human-subject experiments exhibited positive bias toward mobile robots, and post-trial surveys introduced minor response noise[cite: 83].

---

## 7. TODO

- [ ] Implement nonholonomic kinematic constraint mapping for our NMR simulation models[cite: 73, 74].
- [ ] Integrate TSFM PTTC calculation ($t_{c,j}$) into our local planner[cite: 72, 75].
- [ ] Incorporate passivity-based energy bounding checks for multi-agent safety verification[cite: 75, 76, 78].