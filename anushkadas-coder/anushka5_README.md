# anushka5 — Applicability of Multi-Agent Simulation for Crowd Control After a Large-Scale Event

| | |
|---|---|
| **Citation** | A. Takahashi, K. Yasufuku, M. Hegazy. *Applicability of Multi-Agent Simulation for Crowd Control After a Large-Scale Event.* Japan Architectural Review **9**, e70063 (2026). DOI: 10.1002/2475-8876.70063[cite: 1] |
| **File** | `anushka5.pdf` |
| **Type** | Field measurements + Multi-Agent Simulation (Pathfinder 2022) validation around an urban stadium[cite: 1, 3, 8] |
| **Role in our project** | Provides empirical backing and calibration methodology for normal post-event egress simulation, replacing emergency-evacuation defaults with data-driven speed-density relationships, personal distances, and downstream bottleneck constraints[cite: 3, 8, 9]. |

---

## 1. TL;DR

1. Post-event egress dynamics differ fundamentally from emergency evacuations; default multi-agent simulation (MAS) parameters designed for crises cannot accurately replicate everyday crowd dispersal[cite: 2, 3].
2. Field data around a stadium reveals that regular stop-and-go congestion is dictated by downstream bottlenecks (e.g., signalized pedestrian crosswalks) whose capacities are substantially lower than upstream exit flows.
3. Pathfinder simulations achieved high reproducibility ($NSE \ge 0.7$) across independent event days when calibrated with empirical walking speeds, customized speed-density weight factors ($v_{fmin}(D)$), and precise personal distance settings[cite: 3, 11].
4. Simulative testing demonstrates that **flattening peak inflow rates** via crowd guidance/signage can effectively eliminate bottleneck congestion and keep densities below critical thresholds without reducing total crowd volume.

---

## 2. Definitions we will reuse

| Term | Meaning in the paper | Our exit meaning |
|---|---|---|
| Nash-Sutcliffe Efficiency (NSE) | Statistical metric evaluating prediction accuracy by accounting for flow fluctuation magnitudes ($NSE \ge 0.7$ indicates high reproducibility) | Primary validation score for comparing simulation output time-series against sensor/counting lines |
| Steady-state period | Period of regular stop-and-go congestion caused by downstream bottlenecks exceeding capacity[cite: 5] | The duration of active arching/stalling at the exit corridor |
| Level of Service (LOS) | Fruin's density-based service levels; event crowds operate heavily at Level E/F during peaks[cite: 3, 6, 8] | Reference standard for acceptable pedestrian comfort and safety thresholds |
| Personal Distance ($PD$) | Distance agents attempt to maintain from others, derived from standstill density[cite: 9] | Agent spacing configuration parameter in simulation profiles |

---

## 3. Key setups and numbers (so we do not have to re-open the PDF)

**Field Measurement Setup**
- **Location & Dates**: Pedestrian walkways around a dome stadium leading to Station A West Exit, measured on June 1 (Wed, 32,058 spectators), June 3 (Fri, 40,392 spectators), and June 4 (Sat, 40,444), 2022[cite: 4, 5].
- **Observation Window**: 60 min before to 120 min after match end time ($t_0$)[cite: 4, 5].
- **Walking Speed**: Measured average $1.36 \pm 0.21 \text{ m/s}$ (sampled from 110 pedestrians using Tracker software)[cite: 6].
- **Footbridge & Density**: Measurement Area 1 ($4450 \times 4450 \text{ mm}$); average steady-state density $1.23 \pm 0.27 \text{ persons/m}^2$ (LOS E); complete standstill average density $1.87 \text{ persons/m}^2$[cite: 5, 6, 8].
- **Crosswalk 2 Bottleneck**: Signal cycle set to 60s open / 80s closed; estimated operational capacity before congestion is 160–170 people/min (flow coefficient $\approx 22.9–24.3 \text{ people/m/min}$)[cite: 5].

**Simulation Parameters (Pathfinder 2022)**
- **Max Speed ($v_{max}$)**: Set to measured average $1.36 \text{ m/s}$[cite: 9, 10].
- **Speed-Density Weight ($v_{fmin}(D)$)**: Calibrated optimum range $v_{fmin}(D) = 2.0 - 2.4$ (Model E used 2.2)[cite: 10, 11].
- **Personal Distance ($c$)**: Derived from standstill density ($1.87 \text{ persons/m}^2$) and shoulder width ($d = 45.58 \text{ cm}$), giving $c = 0.33 \text{ m}$[cite: 9, 10].

---

## 4. The control factors and our operational mapping

| Group / Factor | Paper Variable / Finding | Our Counterpart / Implementation |
|---|---|---|
| **Downstream Bottlenecks** | Signalized crosswalks restrict flow more severely than stadium exits, creating regular stop-and-go waves[cite: 5]. | Must model exit constraints and downstream absorption rates rather than assuming unobstructed outflow. |
| **Parameter Transferability** | Parameters calibrated on one event day successfully reproduce trends on other days ($NSE \ge 0.7$), provided routing splits are adjusted[cite: 3, 15]. | Validates using a core calibrated simulation model across different experimental layouts. |
| **Peak Flattening** | Distributing pedestrian departures evenly over time prevents footbridge density from exceeding $0.8 \text{ persons/m}^2$[cite: 16]. | Validates our hypothesis that active crowd management / flow regulation prevents physical arching/clogging. |

---

## 5. How we use it (concrete items)

**U1. Abandon emergency-evacuation default profiles.** Use empirically grounded normal walking speeds ($1.36 \text{ m/s}$) and calibrated speed-density curves ($v_{fmin}(D) = 2.2$) for baseline crowd simulations.

**U2. Adopt NSE for quantitative validation.** Evaluate simulation fidelity against empirical counts using the Nash-Sutcliffe Efficiency formula, targeting $NSE \ge 0.7$ for temporal agreement[cite: 11].

**U3. Account for downstream capacity limits.** Recognize that exit congestion is frequently governed by external bottlenecks (like crosswalk signal cycles or narrow street capacity) rather than the exit door width alone[cite: 5].

**U4. Validate peak-smoothing strategies.** Use simulation to demonstrate that flattening inflow rates via active guidance prevents critical density build-ups without forcing reduced attendance[cite: 16].

---

## 6. Tested code / Analysis metrics

**Nash-Sutcliffe Efficiency (NSE) Formula Reference**[cite: 11]
$$NSE = 1 - \frac{\sum_{t=1}^{T}\{(R(t) - S(t))\}^{2}}{\sum_{t=1}^{T}\{(R(t) - \overline{R})\}^{2}}$$
*Where $R(t)$ is real-world measured flow, $S(t)$ is simulated flow, and $\overline{R}$ is the mean real-world flow[cite: 11].*

**Python Validation Function Snippet**
```python
import numpy as np

def calc_nse(real_flow, sim_flow):
    """Calculates Nash-Sutcliffe Efficiency between real and simulated flow."""
    r_mean = np.mean(real_flow)
    numerator = np.sum((real_flow - sim_flow) ** 2)
    denominator = np.sum((real_flow - r_mean) ** 2)
    return 1.0 - (numerator / denominator if denominator != 0 else 0.0)

```

---

## 7. What not to claim

- The paper evaluates normal stadium egress under sports event conditions, not emergency crisis evacuations.
- The study focuses on macroscopic pedestrian flow and density counts via video analysis rather than microscopic pedestrian psychological behavior or individual social force vector modeling
- Perfect real-world transferability requires accounting for concurrent external events (e.g., nearby facility schedules altering route choice proportions).

---

## 8. TODO

- [ ] Incorporate empirically calibrated normal walking speeds ($1.36 \text{ m/s}$) and $v_{fmin}(D)$ parameters into our simulation profiles[
- [ ] Implement calc_nse() statistical validation function in model evaluation scripts.
- [ ] Test peak-flow flattening strategies in simulation to verify mitigation of exit crowding.