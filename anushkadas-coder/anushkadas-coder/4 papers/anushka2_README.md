# anushka2 — Introduction of frictional and turning function for pedestrian outflow with an obstacle

| | |
|---|---|
| **Citation** | D. Yanagisawa, A. Kimura, A. Tomoeda, R. Nishi, Y. Suma, K. Ohtsuka, K. Nishinari. *Introduction of frictional and turning function for pedestrian outflow with an obstacle.* Physical Review E **80**, 036110 (2009). DOI: 10.1103/PhysRevE.80.036110 |
| **File** | `anushka2.pdf` |
| **Type** | Cellular-automaton (floor field) model + analytical outflow formula + two real human experiments |
| **Role in our project** | A **closed-form exit throughput model** and **real-human evidence on obstacles and lanes**. It tells us what our robots should change (number of people pressing on the exit, their incident angles) and where *not* to stand. |

---

## 1. TL;DR

1. Outflow through a narrow exit drops because of two things: **conflicts** (several people stepping into the same cell) and **turning** (people slow down when they must change heading to enter the exit).
2. The authors model both with a **frictional function φ(k)** and a **turning function τ(θ)** and derive a closed-form outflow `q`.
3. Real experiment (50 cm exit, 50 women): a **20 cm column in front of the exit raised outflow from 2.80 to 2.92 persons/(m·s)** (p < 0.01).
4. **Position matters:** an obstacle shifted off-centre helps; the same obstacle at the centre gives *lower* outflow than no obstacle.
5. In line experiments, **two lines that merge like a zipper gave the highest outflow**, which the model cannot reproduce.

---

## 2. The model in plain words

- Room = grid of 0.5 m cells, 0.3 s per step (their Eq. 22). A pedestrian chooses among 5 cells (own + 4 neighbours). The static floor field is the shortest distance to the exit, computed with Dijkstra.
- **Conflict:** k pedestrians choose the same cell. Old model: with a constant probability nobody moves. **New:** the probability that the conflict stays unresolved grows with k (frictional function).
- **Turning:** a pedestrian arriving at the exit cell with incident angle θ (angle from the exit axis) leaves with probability reduced by `τ(θ) = exp(−κ|θ|)`. κ ≈ 0.1 for real pedestrians (text, Secs. VII–VIII).
- **Exit cluster:** n_e cells touch the exit cell (Neumann 3, triangle lattice 4, Moore 5). In a congested exit all are occupied and all try to enter.

### Formulas (reconstructed; the PDF text lost the Greek letters)

I rebuilt Eqs. 4, 6, 10, 11, 15 and **checked them against the numbers printed in the paper** (Section 3 below), so they are safe to code. Notation: `a` = bottleneck parameter (probability of stepping into the exit cell), `b` = probability of leaving the room from the exit cell without turning (paper assumes `a = b` in the experiments), `g` = aggressive parameter (probability of not giving way).

```
phi(k)    = 1 - (1-g)^k - k*g*(1-g)^(k-1)              # frictional function, phi(1)=0, phi(inf)=1
tau(theta)= exp(-kappa*|theta|)                         # turning function, tau(0)=1
b_k       = C(n_e,k) * a^k * (1-a)^(n_e-k)             # k people try to enter the exit cell at once
r         = sum_{k=1..n_e} (1 - phi(k)) * b_k           # one person succeeds
q         = 1 / ( 1/r + (1/n_e) * sum_m 1/(b*tau(theta_m)) )    # outflow, persons per step
q_exp     = q / (cell * dt) = q / (0.5 m * 0.3 s)       # persons / (m s)
```

---

## 3. Verification I ran (so we know the formulas and parameter reading are right)

Parameters I read from Table IV: `a = b = 0.97`, `g = 0.22`, `κ = 0.09`. With them, the formula reproduces the paper's printed values:

| Case | Incident angles | Paper | My calculation |
|---|---|---|---|
| one line, exp (a) | 0° | 3.23 | 3.23 |
| normal evacuation, n_e = 4 | 90°, 30°, −30°, −90° | 2.80 | 2.79 |
| shifted obstacle, n_e = 3 | 90°, 30°, −90° | 2.92 | 2.92 |
| **centre** obstacle, n_e = 4 | 90°, 45°, −45°, −90° | 2.78 | 2.78 |
| one line, exp (A), a = 0.79 | 0° | 2.62 | 2.63 |

Swapping the readings of `g` and κ breaks the match, so the reading is right.

**Model outputs (my run, same parameters; relative comparisons only, calibrated to a 50 cm exit)**

| n_e (all angles 0°) | 1 | 2 | 3 | 4 | 5 |
|---|---|---|---|---|---|
| q (persons/(m·s)) | 3.23 | 3.21 | 3.08 | 2.92 | 2.73 |

| n_e = 3 | angles 0°, 0°, 0° | 45°, 0°, −45° | 90°, 0°, −90° |
|---|---|---|---|
| q | 3.08 | 3.01 | 2.94 |

So the model says: **fewer people pressing on the exit and smaller incident angles give more outflow.**

---

## 4. Experiments and findings

Outflow definition: `q_exp = (j − i) / (w · (t_j − t_i))`, i.e. persons per metre of exit width per second, using persons i..j to skip the empty start and the thin end (i = 4..18 for the 18-person single-line cases, i = 1..15 for the others). Each experiment was repeated 2–3 times (people learn the situation).

**Experiment 1 — evacuation in lines (18 men, 50 cm door).** Outflow values as extracted from Table I (persons/(m·s)):

| Case | Setup | q_exp |
|---|---|---|
| A | 1 line | 2.62 |
| B | 2 lines, ±30° | **2.81** (best) |
| C | 2 lines, 0°/90° | 2.69 |
| D | 2 lines, ±90° | 2.59 |
| E | 3 lines, 45°/0°/−45° | 2.69 |
| F | 3 lines, 90°/0°/−90° | 2.59 |
| G | 4 lines | 2.51 |
| H, I | normal evacuation | 2.51, 2.53 |

Four findings: (1) outflow drops for more than 2 lines (more conflicts); (2) outflow drops as incident angle grows (turning cost); (3) two lines **merge like a zipper** and give the maximum, which the model does *not* reproduce (authors: future work); (4) in a normal evacuation about **4 people press on a 50 cm exit at once**; the triangle lattice (n_e = 4) fits best.

**Experiment 2 — obstacle (NHK studio, 50 women, 50 cm exit, column Ø 20 cm).**

| Case | q_exp |
|---|---|
| (a) one line | 3.23 |
| (b) normal evacuation | 2.80 |
| (c) normal + column in front of exit | **2.92** (p < 0.01 vs b) |

About 35 % went round the left of the obstacle and 65 % round the right. The obstacle blocks one of the side neighbours, so n_e goes 4 → 3. The model says a **centre** obstacle (2.78) is worse than no obstacle (2.80), because straight-ahead walkers (0°) must detour.

---

## 5. How we use it (concrete items)

**V1. Replace `T = t_A / p` with a model that has the right inputs.** Use `q(n_e, θ_m)` for the exit throughput. Service rate of the exit = `q × W_e` persons/s (with `q` per metre of width). In HALER the expected waiting time at an exit vertex becomes `N_waiting / (q · W_e)`. (Our suggestion, not in the paper.)

**V2. Define what the robots change.** The paper says outflow rises when the crowd pressing on the exit is smaller (n_e) and when people arrive straight (θ → 0). So robot placement should maximise `q`. Cheap rule: **intercept arrivals with |θ| ≥ 45°, keep the exit axis free.** Incident angle from the camera: angle between the exit normal and the line from each head to the exit centre.

**V3. Warning about our oscillating robots.** In the team notes the robots oscillate sideways in front of the exit. Every time one crosses the centreline it acts like the **centre obstacle**, which this paper found *worse than no obstacle*. Test amplitude and phase in simulation; do not assume the oscillation is harmless.

**V4. Do not assume single file is optimal.** Experiments gave 2.81 for two lines against 2.62 for one line, though the model gives about equal (3.21 vs 3.23). So the number of lanes `t_A` should be a **swept design variable (1, 2, 3)**, not fixed by `⌈W_e / (2·W_h)⌉`. Caveat: the zipper result was for lines merging into a one-person-wide exit; it is not proven for a 1.6 m exit.

**V5. Measurement protocol for all our simulations.** Report specific flow `(j−i)/(w(t_j−t_i))`, drop the first and last few people, repeat runs. Add this to `pillar_sweep.py` next to the clog statistics from `anushka1_README.md`.

**V6. Calibration recipe.** Fit `a` from a one-line run (no conflict, no turning: `q = a·b/(a+b)`), then fit `g` and κ from multi-line or normal runs by least squares. Warning from the paper itself: in Experiment 2 the fit has 3 parameters and 3 data points, so the zero error is **by construction**, not validation. We need held-out data.

**V7. Observable trigger for the robots.** Count heads in the exit-mouth zone to get n_e, and compute θ_m per head, then `q_hat = q(n_e, θ_m)`. If `q_hat` is well below the best reachable value, engage the robots. Use `J = ∫ (q_measured − q_ref)² dt` as the ADP cost, same form as in the ADP robot paper.

**Tested code**
```python
from math import comb, exp, radians

def phi(k, g):  return 1 - (1-g)**k - k*g*(1-g)**(k-1)
def tau(th, kappa): return exp(-kappa*abs(radians(th)))

def q_theo(angles_deg, a=0.97, b=0.97, g=0.22, kappa=0.09):
    """persons/step for an exit with n_e = len(angles_deg) approaching people."""
    ne = len(angles_deg)
    bk = [comb(ne, k) * a**k * (1-a)**(ne-k) for k in range(ne+1)]
    r  = sum((1-phi(k, g)) * bk[k] for k in range(1, ne+1))
    s  = sum(1/(b*tau(t, kappa)) for t in angles_deg) / ne
    return 1 / (1/r + s)

to_persons_per_m_s = lambda q: q / (0.5 * 0.3)       # cell 0.5 m, step 0.3 s
# q_theo([90,30,-30,-90]) -> 2.79 ; q_theo([90,30,-90]) -> 2.92 ; q_theo([90,45,-45,-90]) -> 2.78
```

---

## 6. Obstacle geometry across the three sources (the sweep has to settle this)

| Source | Exit width w | Obstacle | Stand-off | Position |
|---|---|---|---|---|
| Li et al. 2019 (arch paper, SFM simulation) | 1.6 m | two squares, l = d = 0.5 w (sim 0.9 m) | h = w/3 (sim 0.6 m); gap s = w (sim 1.8 m; their best 2.5 m) | symmetric |
| Zuriguel et al. 2014 (sheep) | 0.77 m | one cylinder Ø 1.14 m (≈ 1.5 w) | 0.80 m (≈ 1.0 w) | in front of gate |
| Yanagisawa et al. 2009 (humans) | 0.5 m | one column Ø 0.20 m (≈ 0.4 w) | not stated in the text I have | **off-centre helps, centre hurts** |

The three disagree on size, stand-off and symmetry. Do not copy any single rule as final.

---

## 7. What not to claim

- Exit is **one person wide** (50 cm) and staged volunteers were used, so there is no real panic and no pushing.
- The model does not reproduce the zipper effect, and the obstacle experiment was one 20 cm column.
- "Two pillars" or "two robots" is **not** what was tested here. This paper tests one obstacle.

---

## 8. Combined build order (anushka1 + anushka2 + `arch-research.pdf`)

1. Simulation: SFM exit room, with `--v0`, periodic boundary option, baseline / pillars / robot agent (`pillar_sweep.py`).
2. Metrics: specific flow `q_exp` (V5) **and** clog statistics `α, W, ⟨s⟩` (`anushka1_README.md`).
3. Exit model: `q_theo(n_e, θ)` as the predicted throughput `q_ref` (V1, V6).
4. Robot rule: intercept side arrivals, keep the centreline free (V2, V3), test 1 / 2 / 3 lanes (V4).
5. Controller: ADP/DRL with cost on `(q_measured − q_ref)` (V7).
6. HALER link: exit-vertex service rate `q · W_e`.
7. Evaluate on the L × IL × CL phase-diagram sweep.
