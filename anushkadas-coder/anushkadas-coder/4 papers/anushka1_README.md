# anushka1 — Clogging transition of many-particle systems flowing through bottlenecks

| | |
|---|---|
| **Citation** | I. Zuriguel, D. R. Parisi, R. C. Hidalgo, C. Lozano, A. Janda, P. A. Gago, J. P. Peralta, L. M. Ferrer, L. A. Pugnaloni, E. Clément, D. Maza, I. Pagonabarraga, A. Garcimartín. *Clogging transition of many-particle systems flowing through bottlenecks.* Scientific Reports **4**, 7324 (2014). DOI: 10.1038/srep07324 |
| **File** | `anushka1.pdf` |
| **Type** | Experiments + simulations (sheep through a gate, simulated pedestrians, vibrated silos, colloids) |
| **Role in our project** | Gives us a way to **measure and classify a clog at an exit** (our "arch"), and a map of which knobs unclog it. It is the measurement backbone for the robot-at-the-exit idea (`arch-research.pdf`). |

---

## 1. TL;DR

1. Flow through a narrow opening is **intermittent**: *bursts* of passages separated by *clogs*.
2. The time lapses τ between consecutive passages have a **power-law tail**. If its exponent **α ≤ 2**, the mean clog time diverges and the system is **clogged**; if **α > 2** it is **unclogged**.
3. The same behaviour appears in sheep, simulated pedestrians, grains and colloids, so the authors propose one unified picture with **three kinds of control knobs** (Section 4).
4. For sheep, a cylinder in front of the gate **raised α** and made bursts longer (mean burst size 11 → 17).

---

## 2. Definitions we will reuse

| Term | Meaning in the paper | Our exit meaning |
|---|---|---|
| time lapse τ | time between two consecutive bodies crossing the exit line | timestamp difference at the exit counting line |
| `t_min` | start of the power-law tail; returned by the Clauset–Shalizi–Newman (CSN) fit; also used as the cut-off between "flow" and "clog" | per-exit constant, calibrated on a baseline run |
| burst | consecutive passages separated by lapses ≤ `t_min` | a run of people passing without a stall |
| clog | a lapse > `t_min` | **arch / stall at the exit** |
| burst size s | people per burst; always **exponentially** distributed, so mean ⟨s⟩ is well defined | report ⟨s⟩ |
| α | exponent of the power-law tail of τ | main clog indicator |
| W (flowing parameter) | `W = ⟨t_f⟩ / (⟨t_c⟩ + ⟨t_f⟩)`; ⟨t_f⟩ mean burst duration, ⟨t_c⟩ mean clog duration | order parameter we report |

| α | ⟨t_c⟩ | W | state |
|---|---|---|---|
| ≤ 2 | diverges | 0 | **clogged** (mean flow rate is not well defined: it keeps dropping as the measuring time grows) |
| > 2 | finite | > 0 | **unclogged** |
| > 2 | finite | between 0 and 1 | intermittent flow |
| > 2 | finite | = 1 | continuous flow |

Fits in the paper have p-value > 0.1.

---

## 3. Key setups and numbers (so we do not have to re-open the PDF)

**Sheep (real data)**
- Gate width 77 cm, sheep width at the hips about 35 cm (ratio ≈ 2.2). Flock 75 ± 10 animals.
- Zenithal camera, 704 × 576 px, 25 fps. A 5-pixel-thick line one body length after the door is stacked over time to give a spatio-temporal diagram ("photo finish").
- 3070 sheep logged without an obstacle, 1432 with one.
- Obstacle: vertical concrete pipe, diameter 114 cm (≈ 1.5 × gate width), placed 80 cm in front of the gate (≈ 1.0 × gate width), taller than the sheep so they cannot see the door from behind.
- `t_min ≈ 1 s`; choices between 0.7 s and 2 s made no significant difference.
- Result: α is higher with the obstacle; ⟨s⟩ = 17 with, 11 without.

**Simulated pedestrians (Social Force Model + noise)**
- Noise: random force perpendicular to the door line, uniform in [−h, h].
- Reference condition: desired speed v_d = 6 m/s, h = 10 kN, door L = 1.0 m.
- N = 30 pedestrians, 20 × 20 m room, **periodic boundary** (anyone who leaves is re-inserted at a random position once 3 m past the door) so the door stays at maximum density.
- SFM: A = 11 N, B = 0.08 m, k_n = 180 kN/m, k_t = 300 kN/m, τ_p = 0.5 s, r = 0.27 ± 0.03 m, m = 80 ± 10 kg; velocity-Verlet, dt = 0.001 s; 54 runs of 2,000,000 s each.
- Result: clogged (α ≤ 2) turns unclogged when **v_d is reduced**, **h is increased**, or **the door is widened**. The v_d effect is suggested as the origin of faster-is-slower.

*(Note: A = 11 N here versus A = 2000 N in the arch paper. Different papers, different SFM calibrations. We must pick one set and cite it.)*

---

## 4. The three knob groups (Table 1 + Fig. 5 of the paper) and our robots

| Group | Meaning | Pedestrian / sheep examples in the paper | Our counterpart |
|---|---|---|---|
| **CL — compatible load** | driving force / pressure that builds clogging structures | desired speed v_d; obstacle in front of the gate | pressure of the crowd at the exit; lanes/robots that reduce the number pushing |
| **IL — incompatible load** | noise that can destabilise a clog | random force h; vibration in silos; temperature in colloids | **hypothesis:** oscillating robots act like a perturbation |
| **L — length scale** | outlet size relative to body size | door width | `W_e / W_h` (our `t_i`) |

Reading of the diagram (my interpretation): there are two ways to unclog, lower CL or raise IL, and a wider door shifts the whole boundary.
The team notes say the robots "prevent the arch from forming". In this language that means **lowering CL**. The sideways oscillation could additionally act as **IL**, but that is a hypothesis to test, not something this paper shows.

---

## 5. How we use it (concrete items)

**U1. Define an arch operationally.** Arch/clog = a lapse between two exit-line crossings longer than `t_min`. Calibrate `t_min` on a baseline simulation of the same exit, because the paper says it depends on speeds, sizes and door width (≈ 1 s for sheep).

**U2. Stop relying on one throughput number.** `T = t_A / p` assumes steady service. Near the transition the mean flow rate depends on the measuring time. Compare baseline / pillars / robots using **α, W, ⟨s⟩, clog-time fraction**, and still report mean outflow over a fixed window.

**U3. Use α and W for evaluation, not for the live trigger.**
My synthetic test (not from the paper): the CSN fit recovers the true exponent with about 20,000 lapses (true 1.7 → 1.70; true 2.6 → 2.67), but with 30–100 passages the estimate has a standard deviation of about 0.3, which cannot reliably separate α ≤ 2 from α > 2. So the real-time ARCHING/CRITICAL_CLOG trigger should be cheap: time since the last passage > `t_min`, plus clog-time fraction over the last 60 s. Use α and W offline.

**U4. Build a phase-diagram sweep (our headline experiment).** Same three axes as the paper:

| Axis | Paper variable | Our simulation variable |
|---|---|---|
| L | door size | exit width `W_e` (and `W_e / W_h`) |
| IL | random force h | SFM noise h |
| CL | desired speed v_d | `--v0` in `pillar_sweep.py` |

Map W over the grid for **baseline vs pillars vs robots**. If an obstacle or robot enlarges the unclogged region, that is a measurable claim that needs no real-crowd data. With periodic boundaries one run yields 10⁴–10⁵ passages, enough for the α fit.

**U5. Starting geometry for the obstacle/robot stand-off.** The sheep result gives one data point (obstacle ≈ 1.5 × gate width, ≈ 1.0 × gate width in front). The arch paper uses h ≈ w/3. These disagree, so the sweep must decide. See the comparison table in `anushka2_README.md`.

**U6. Quantify "faster-is-slower".** Our synopsis (Algorithm 1) labels `CRITICAL_CLOG_FIS` from density and velocity thresholds. This paper gives a statistical definition: FIS clog = α ≤ 2 (W = 0), FIF = unclogged. Use it to validate the label.

**U7. Be careful with our density threshold.** The authors argue packing fraction at the orifice cannot be rigorously measured or controlled, and clogging depends on local correlations near the opening. So our ρ_crit formula should be treated as an **early-warning input** and validated against clog statistics (does exceeding ρ_crit actually lower α?).

---

## 6. Tested code (clog statistics from exit timestamps)

```python
# pip install powerlaw   (implements Clauset–Shalizi–Newman)
import numpy as np, powerlaw

def clog_stats(lapses, t_min=None):
    """lapses: array of time gaps between consecutive exit-line crossings (s)."""
    fit = powerlaw.Fit(lapses, verbose=False)        # alpha and xmin (=t_min) of the tail
    t_min = fit.xmin if t_min is None else t_min
    flow, clog = lapses[lapses <= t_min], lapses[lapses > t_min]
    t_f = flow.sum() / max(len(clog), 1)             # simplified: flow time per clog
    t_c = clog.mean() if len(clog) else 0.0
    W = t_f / (t_f + t_c) if (t_f + t_c) > 0 else np.nan
    return dict(alpha=fit.alpha, t_min=t_min, W=W, state="CLOGGED" if fit.alpha <= 2 else "UNCLOGGED")
```

Checked on synthetic data: true α = 1.7 gave α = 1.70, W = 0.06 (clogged); true α = 2.6 gave α = 2.67, W = 0.86. My `W` is a simplified estimator, so treat it as a comparative score between layouts.

**Monitor sketch**
```
log crossing times at the exit line (camera counting line or simulation)
live    : clog_now  = (now - last_crossing) > t_min
          clog_frac = share of last 60 s spent in clog     -> ARCHING flag for HALER exit vertex
offline : lapses -> clog_stats() -> alpha, W, <s>           -> compare baseline / pillars / robots
```

---

## 7. What not to claim

- The paper has **no robots and no human-crowd arch experiment**. Humans appear only as a simulation; real data is sheep.
- The pedestrian simulation is extreme (v_d = 6 m/s, N = 30, 1 m door, periodic boundary). Treat it as qualitative.
- α ≤ 2 is a statistical statement about diverging mean clog time, not "this crowd is permanently stuck".
- The "oscillating robot = incompatible load" link is our hypothesis.

---

## 8. TODO

- [ ] Add exit-crossing logging and `clog_stats()` to `pillar_sweep.py`.
- [ ] Add a periodic-boundary option to the simulation (re-insert agents past the door).
- [ ] Calibrate `t_min` on the baseline exit.
- [ ] Run the L × IL × CL sweep for baseline vs pillars vs robots.
- [ ] Check whether ρ_crit exceedance predicts α drop (U7).
