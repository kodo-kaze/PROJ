# anushka11 — Policy Optimization for Pedestrian Traffic Management by Surrogation of Simulation Models

| | |
|---|---|
| **Citation** | F. Tanaka, T. Amano, A. Uchiyama, A. Hiromori, Y. Nakamura, H. Yamaguchi. *Policy Optimization for Pedestrian Traffic Management by Surrogation of Simulation Models.* IEEE 21st International Conference on Mobile Ad-Hoc and Smart Systems (MASS), Seoul, Korea, Republic of (2024), pp. 203-211. DOI: 10.1109/MASS62177.2024.00036 |
| **File** | `anushka11.pdf` |
| **Type** | Conference proceedings full paper proposing a differentiable deep learning surrogate model to accelerate black-box pedestrian flow policy optimization. |
| **Role in our project** | Provides the gradient-based optimization framework using neural network surrogates over abstracted detour networks to rapidly optimize phased exits and detour incentive strategies without running slow agent-based simulations. |

---

## 1. TL;DR

1. **Surrogate Model Acceleration:** Replaces computationally expensive agent-based pedestrian traffic simulations with a differentiable neural network surrogate model, enabling direct gradient-based optimization via Projected Gradient Descent (PGD).
2. **Detour-Centric Network Abstraction:** Simplifies complex urban OpenStreetMap (OSM) road networks into essential origin, intermediate detour, and destination bypass nodes, drastically cutting dataset preparation and model inference time.
3. **Multi-Objective Policy Tuning:** Optimizes venue exit parameters (phased exit stages and intervals) alongside intermediate spot parameters (attractiveness and stay duration) against a combined loss of congestion penalty, monetary cost, and travel time.
4. **Significant Time Savings:** Demonstrates an average reduction of ~14,500 seconds (~4 hours) in exploration time per scenario compared to traditional Bayesian Optimization while achieving comparable policy performance.

---

## 2. Definitions & Metrics We Will Reuse

| Term | Meaning in the paper | Our project meaning |
|---|---|---|
| **Detour-Centric Network** | Abstracted virtual road network comprising origin node $n_{\text{origin}}$, intermediate nodes $I$, and destination nodes $D$. | Simplified graph representation for quick simulation and surrogate training. |
| **Phased Exit Procedure ($p, b$)** | Dividing crowd departures into $p$ stages separated by time intervals $b \in [b_{\min}, b_{\max}]$. | Staggered release strategy to prevent simultaneous crowd rushes at transit hubs. |
| **Attractiveness & Stay Time ($g_i, s_i$)** | Incentive-based probability ($g_i$) and duration ($s_i$) for pedestrians detouring to commercial spot $i$. | Spot-level behavioral intervention parameters configured via monetary incentives. |
| **Policy Effectiveness Score ($\mathcal{F}$)** | Weighted linear combination of destination congestion penalty ($\mathcal{P}$), incentive cost ($\mathcal{M}$), and travel time ($\mathcal{T}$). | Multi-objective cost function to evaluate and select optimal crowd management strategies. |

---

## 3. Key Mathematical Formulations

### A. Multi-Objective Function

$$\arg\min_{\text{policy}} \mathcal{F} = w_1 \mathcal{P} + w_2 \mathcal{M} + w_3 \mathcal{T}$$

Where weighting factors $w_1, w_2, w_3$ control optimization preferences.

### B. Component Cost Formulations

1. **Destination Congestion Penalty ($\mathcal{P}$):**

$$\mathcal{P} = \sum_{t=0}^{T} \sum_{i \in D} \max(0, c_{i,t} - f_{i,t})$$

*(Accumulates population $c_{i,t}$ exceeding transit handling capacity $f_{i,t}$ across time steps $T$)*

2. **Monetary Incentive Cost ($\mathcal{M}$):**

$$\mathcal{M} = p \frac{b}{b_{\max} - b_{\min}} + \sum_{i \in I} \left(\frac{s_i}{s_{\max}} + \alpha\right) g_i$$

*($\alpha$ is the minimum base incentive cost to attract pedestrians)*

3. **Average Travel Time Cost ($\mathcal{T}$):**

$$\mathcal{T} = \frac{\sum_{t=1}^{T} \sum_{i \in D} (c_{i,t} - c_{i,t-1}) t}{n}$$

*(Normalized total travel time over all $n$ participants)*

### C. Projected Gradient Descent (PGD) Optimization

$$\theta_{k} \leftarrow \text{proj}\left(\theta_{k} - \alpha \nabla \tilde{\mathcal{F}}(\theta_{k}, \mathcal{G}, \mathcal{B})\right)$$

Iteratively updates policy parameters $\theta$ via backpropagation through the surrogate model $\tilde{\mathcal{F}}$ under graph $\mathcal{G}$ and agent behavior $\mathcal{B}$, projecting parameter values onto valid ranges $[0, s_{\max}]$ and $[b_{\min}, b_{\max}]$.

---

## 4. How We Use It (Concrete Items)

**U1. Construct detour-centric abstraction layers.** Map real-world venue and transit hub locations into abstracted origin-intermediate-destination graphs to streamline training datasets.

**U2. Train differentiable surrogate neural networks.** Predict $\mathcal{P}$ and $\mathcal{T}$ using Fully Connected Networks (FCN) or Graph Attention Networks (GAT) trained on synthetic and real-world crowd flows (e.g., Koshien Stadium data).

**U3. Execute Grid-Initialized PGD.** Use multi-start grid initialization followed by Projected Gradient Descent to find optimal $(p, b, g_i, s_i)$ parameters within seconds without requiring live simulation evaluations.

---

## 5. Limitations & Tested Conditions

- Evaluated on virtual fields ($2\text{ km} \times 2\text{ km}$, 10k–30k agents) and Koshien Stadium event dataset.
- Abstracted detour-centric networks introduce minor errors ($\text{MAE} = 0.022$ for Congestion, $0.077$ for Travel Time) compared to original full-scale road networks.
- Two-stage evaluation relies on surrogate prediction quality; secondary fine-tuning on full simulators may still be beneficial for ultra-precise deployments.

---

## 6. TODO

- [ ] Create graph conversion script to abstract OpenStreetMap (OSM) areas into detour-centric structures.
- [ ] Implement FCN / GAT model architecture in PyTorch for learning simulation outputs.
- [ ] Build Projected Gradient Descent (PGD) optimizer loop supporting dynamic weightings ($w_1, w_2, w_3$).
