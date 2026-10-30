# anushka10 — Dynamic Pedestrian Flow Guidance through Multi-robot Interaction along Human Crowd Boundaries

| | |
|---|---|
| **Citation** | R. Shigemoto, H. Kimura, R. Tasaki. *Dynamic Pedestrian Flow Guidance through Multi-robot Interaction along Human Crowd Boundaries.* ACM/IEEE Int. Conf. Human-Robot Interaction (HRI Companion '26), Edinburgh, UK (2026). DOI: 10.1145/3776734.3794428[cite: 90] |
| **File** | `anushka10.pdf`[cite: 90] |
| **Type** | Conference proceedings short paper proposing a soft, boundary-centric multi-robot guidance framework using dynamic convex hulls and Hungarian algorithm matching[cite: 90, 91]. |
| **Role in our project** | Provides the specific algorithm for dynamic crowd boundary identification, priority scoring (head/side/corner), and reachability-aware multi-robot allocation using Hungarian optimization to guide crowd flows along guidance lines[cite: 91, 92]. |

---

## 1. TL;DR

1. **Soft Boundary Guidance:** Replaces coercive deceleration/blocking interventions with soft, human-centered guidance where robots position themselves with a lateral side-offset ($d_{side}$) perpendicular to boundary pedestrians to elicit anticipatory flow alignment[cite: 90, 91].
2. **Boundary Classification:** Approximates crowd outer contours using a dynamic convex hull, categorizing boundary agents into **Head**, **Side**, and **Corner** pedestrians based on outward normal angle offsets relative to average crowd flow[cite: 91, 92].
3. **Priority & Urgency Scoring:** Unifies boundary-type importance ($P_j^{type}$) with time-to-intersection urgency ($U_j(t_j^{line})$) relative to a target guidance line[cite: 92].
4. **Reachability-Aware Allocation:** Employs the Hungarian algorithm to compute an optimal one-to-one matching between mobile robots and target interaction destinations based on a combined priority-reachability score[cite: 91, 92].

---

## 2. Definitions & Metrics We Will Reuse

| Term | Meaning in the paper | Our project meaning |
|---|---|---|
| **Boundary Pedestrians** | Pedestrians forming the outer convex hull contour of a crowd (classified into Head, Side, Corner)[cite: 91, 92]. | Target pedestrians whose local motion changes alter overall crowd flow shape. |
| **Parallel-Interaction Strategy** | Positioning a robot at a side-offset distance ($d_{side}$) perpendicular to a target pedestrian's walking vector to gently guide their heading[cite: 91]. | Non-coercive robot positioning strategy to guide exit queues. |
| **Time-to-Intersection ($t_j^{line}$)** | Approximated remaining time for a pedestrian's current velocity trajectory to cross a target guidance line ($t_j^{line} = d_j^\perp / v_j^\perp$)[cite: 92]. | Urgency metric determining which boundary pedestrians need immediate guidance. |
| **Reachability Term ($A_{ij}$)** | Exponentially scaled arrival time score of robot $i$ reaching target destination $g_j$ ($A_{ij} = \exp(-t_{ij}^{robot} / \tau_r)$)[cite: 92]. | Feasibility factor preventing robots from bidding on unreachable crowd targets. |

---

## 3. Key Mathematical Formulations

### A. Dynamic Boundary Categorization
1. Compute the convex hull of the crowd and estimate average flow direction[cite: 91, 92].
2. For each hull edge, measure angle $\theta$ between its outward normal and the average flow vector[cite: 91, 92]:
   - **Head Boundary:** $\theta < 45^\circ$[cite: 91, 92].
   - **Side Boundary:** $45^\circ \le \theta \le 135^\circ$ (Left/Right split via cross product)[cite: 91, 92].
   - **Tail Boundary:** $\theta > 135^\circ$[cite: 91, 92].
3. **Corner Pedestrians:** Overlap of head and side sets ($P_{corner}(t) = P_{head}(t) \cap P_{side}(t)$)[cite: 91].

### B. Unified Guidance Priority Score
- **Base Type Weight ($P_j^{type}$):** $P_j^{type} = 5$ for Corner, $2$ for Head/Side[cite: 92].
- **Time Urgency ($U_j$):** $U_j(t_j^{line}) = \exp(-t_j^{line} / \tau_l)$[cite: 92].
- **Priority Equation:**
  $$P_j^{guide} = P_j^{type} \cdot U_j(t_j^{line})$$[cite: 92]

### C. Reachability-Aware Multi-Robot Matching
- Compute target interaction destination $g_j$ with offset $d_{side}$[cite: 91, 92].
- Calculate robot travel time $t_{ij}^{robot} = \|g_j - r_i(t)\| / v_i^{max}$ and reachability $A_{ij} = \exp(-t_{ij}^{robot} / \tau_r)$[cite: 92].
- **Final Matching Matrix Score ($S_{ij}$):**
  $$S_{ij} = P_j^{guide} \cdot A_{ij}$$[cite: 92]
- Solve optimal assignment using the Hungarian algorithm[cite: 91, 92].

---

## 4. How We Use It (Concrete Items)

**U1. Classify crowd boundary agents dynamically.** Use convex hull extraction and normal angle thresholds to tag Head, Side, and Corner pedestrians in real-time surveillance feeds[cite: 91, 92].

**U2. Implement reachability-aware assignment.** Solve $S_{ij} = P_j^{guide} \cdot A_{ij}$ with the Hungarian algorithm to distribute robot targets across our multi-robot exit-shepherding team[cite: 91, 92].

**U3. Apply non-coercive side-offset guiding.** Deploy $d_{side}$ parallel interaction targets to steer pedestrians toward exit doors without causing panic or blocking flow[cite: 91, 92].

---

## 5. Limitations & Tested Conditions

- Evaluated primarily in unidirectional simulation settings (15 pedestrians, 3 robots)[cite: 90, 92].
- Base importance weights ($P_j^{type} = 5, 2$) are heuristic and require formal sensitivity tuning for opposing/crossing crowd flows[cite: 92, 93].

---

## 6. TODO

- [ ] Implement Convex Hull dynamic boundary extraction script for crowd tracking[cite: 91, 92].
- [ ] Build Hungarian algorithm solver mapping $S_{ij}$ matrix for multi-robot dispatching[cite: 91, 92].
- [ ] Configure $d_{side}$ parallel interaction offset parameter in our ROS2 local planner[cite: 91, 92].