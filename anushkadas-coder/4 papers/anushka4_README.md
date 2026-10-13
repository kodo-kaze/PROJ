# anushka4 — SICNav: Safe and Interactive Crowd Navigation using Model Predictive Control and Bilevel Optimization

| | |
|---|---|
| **Citation** | Sepehr Samavi, James R. Han, Florian Shkurti, Angela P. Schoellig. *SICNav: Safe and Interactive Crowd Navigation using Model Predictive Control and Bilevel Optimization.* arXiv:2310.10982 (2025). |
| **File** | `anushka4.pdf` |
| **Type** | Nonlinear Model Predictive Control (MPC) + Bilevel Optimization + ORCA (Optimal Reciprocal Collision Avoidance) + Simulations & Real-robot experiments (Clearpath Jackal) |
| **Role in our project** | Gives us a formal, optimization-based framework to **jointly plan robot motions and interactive crowd predictions** at exits and bottlenecks, overcoming the Freezing Robot Problem (FRP) by modeling human responses using ORCA embedded via KKT-reformulation. |

---

## 1. TL;DR

1. Classical decoupled prediction and planning ignores human-robot interaction and causes the **Freezing Robot Problem (FRP)** in dense crowds.
2. **SICNav** formulates crowd navigation as a bilevel nonlinear MPC problem where the upper level optimizes robot actions while the lower level predicts human motions using an **Optimal Reciprocal Collision Avoidance (ORCA)** model.
3. By applying Lagrangian duality and KKT conditions, the bilevel problem is reformulated into a single-level Mathematical Program with Complementarity Constraints (MPCC) that can be solved in real-time (10 Hz).
4. Evaluated extensively in simulations (with ORCA and Social Force Model agents) and real-robot indoor experiments, outperforming traditional baselines in success rate, path efficiency, and safety.

---

## 2. Definitions we will reuse

| Term | Meaning in the paper | Our exit meaning |
|---|---|---|
| SICNav | Safe and Interactive Crowd Navigation framework combining nonlinear MPC and bilevel ORCA prediction | Our interactive robot controller for managing bottleneck arches and preventing stalls |
| ORCA | Optimal Reciprocal Collision Avoidance model used to determine collision-free and minimum-cost human velocities | The behavioral model used to predict how pedestrians react to the robot's presence at the exit |
| Bilevel Optimization / KKT Reformulation | Casting hierarchical optimization (robot planning over human reactions) into a single-level MPCC via Karush-Kuhn-Tucker conditions | The mathematical trick to solve closed-loop robot-crowd interaction efficiently |
| Slack Variable ($\zeta$) | Variable added to relaxed collision avoidance constraints to handle dense, otherwise infeasible scenarios | A measure of constraint relaxation when the exit arch becomes tightly congested |

---

## 3. Key setups and numbers (so we do not have to re-open the PDF)

**Simulation Environment**
- 2m-wide corridor with initial positions and goals on opposite ends, featuring static obstacles and a **1m-wide doorway bottleneck**.
- Evaluated with $N = 3$ and $N = 5$ simulated humans following either ORCA or the Social Force Model (SFM) across 500 random scenarios per setup.
- Discretization time step $\delta t = 0.25s$, MPC prediction horizon $T = 1s$.

**Real Robot Experiments**
- Clearpath Jackal differential drive robot (20 kg, 46cm W $\times$ 60cm L $\times$ 50cm H).
- Indoor testing area measuring $5m \times 9m$ using a VICON motion capture system and EKF/KFs for tracking.
- 80 total runs across 5 multi-agent scenarios with $N \in \{1, 2, 3\}$ humans. Real-time execution at 10 Hz using Acados C++ code generation with horizon $T = 2s$.

**Mathematical Formulations**
- Relaxed ORCA optimization problem for each human $j$:
  $$\min_{v, \zeta} \Vert{}v - v_{\text{pref}}(x_t)\Vert{}_2^2 + M\zeta^2$$
  $$\text{s.t. } \Vert{}v\Vert{}_2^2 \le v_{\text{max}}^2, \quad n^{(j\vert{}l)}(x_t)^\top v \ge \rho^{(j\vert{}l)}(x_t) - \zeta, \quad \zeta \ge 0$$
- KKT Reformulation replaces the lower-level optimality conditions with Stationarity ($\nabla_{\nu}\mathcal{L} = 0$), Complementary Slackness ($\lambda^\top c = 0$), Primal Feasibility ($c \le 0$), and Dual Feasibility ($\lambda \ge 0$).

---

## 4. The three knob groups / Framework Architecture

| Group | Meaning | Pedestrian / sheep examples in the paper | Our counterpart |
|---|---|---|---|
| **Upper-Level MPC** | Robot cost function, unicycle kinematics, kino-dynamic constraints, and safety/collision boundaries | Robot trajectory optimization | The robot's real-time trajectory and velocity command generator |
| **Lower-Level ORCA** | Decentralized velocity obstacle optimization representing human reaction to the system state | Pedestrian reaction model | The predictive model of how crowd members react to geospatial obstacles and the robot at the exit |
| **KKT Reformulation** | Single-level MPCC transformation enabling gradient-based non-linear solvers (like Acados) to solve the bilevel problem | Mathematical transformation for tractability | The optimization backend that makes interactive crowd navigation tractable in real-time |

---

## 5. How we use it (concrete items)

**U1. Stop using open-loop prediction for exits.** Decoupled predict-then-plan architectures fail and lead to the Freezing Robot Problem; switch to closed-loop joint planning where robot actions influence human movement.

**U2. Embed ORCA constraints into local planners.** Use the relaxed ORCA formulation with slack variables ($\zeta$) to model pedestrian response inside constraint boundaries.

**U3. Implement KKT-reformulation for real-time control.** Cast the bilevel problem into an MPCC using Lagrangian duality and solve via CasADi/Acados at 10 Hz.

**U4. Handle non-privileged information (SICNav-np).** When true human goals are unknown, project current velocities forward in time to estimate destinations for the ORCA prediction model.

**U5. Validate bottleneck interaction.** Test the robot's ability to "persuade" or wait for humans at narrow doorway arches without getting stuck or deadlocked.

---

## 6. Tested code (SICNav optimization & ORCA relaxation concepts)

```python
import numpy as np

def relaxed_orca_objective(v, v_pref, zeta, M=1000.0):
    """
    Computes the cost for the relaxed ORCA problem for an individual agent.
    v: velocity vector (2D)
    v_pref: preferred velocity vector towards goal (2D)
    zeta: slack variable for collision constraint relaxation
    M: large penalty constant for constraint violation
    """
    cost_velocity = np.sum((v - v_pref) ** 2)
    cost_slack = M * (zeta ** 2)
    return cost_velocity + cost_slack

def check_kkt_stationarity_residual(grad_lagrangian):
    """
    Checks the stationarity condition of the KKT constraints (nabla_nu L = 0).
    """
    return np.linalg.norm(grad_lagrangian)
