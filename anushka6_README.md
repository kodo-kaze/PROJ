# anushka6 — Mixed Crowd Navigation: Perception, Interaction, Planning, and Control

| | |
|---|---|
| **Citation** | J. Pan, W. Li, W. Liu, I. Islam, K. Guo, Y. Yang, S. Zhang, X. Ji, D. Wang. *Mixed Crowd Navigation: Perception, Interaction, Planning, and Control.* Annu. Rev. Control Robot. Auton. Syst. **9**, 325–349 (2026). DOI: 10.1146/annurev-control-032024-023929[cite: 20] |
| **File** | `anushka6.pdf` |
| **Type** | Comprehensive literature review / survey across the perception-interaction-planning-control pipeline for mixed robotic-volitional crowds[cite: 20, 21] |
| **Role in our project** | Provides the overarching conceptual framework, taxonomy, and state-of-the-art literature reference for integrating robotic agents (our pillars/robots) with volitional human crowds at bottleneck exits[cite: 20, 21]. |

---

## 1. TL;DR

1. Mixed crowd navigation studies how autonomous robotic agents interact with and regulate volitional crowds (humans and human-driven vehicles) to achieve system-wide efficiency and safety[cite: 20].
2. Organizes the field around a **perception-interaction-planning-control pipeline**, tackling key challenges such as complex interaction modeling, partial observability, and underactuation[cite: 20, 21, 22].
3. Examines multiscale dynamics across **microscopic, mesoscopic, and macroscopic** granularities, bridging individual behavior with collective phenomena[cite: 25, 29].
4. Categorizes control strategies into **explicit control** (direct commands/interventions) and **implicit control** (environmental shaping and behavioral nudges), backed by real-world validations like the CIRCLES traffic smoothing project[cite: 33, 35, 36].

---

## 2. Definitions we will reuse

| Term | Meaning in the paper | Our project meaning |
|---|---|---|
| Volitional crowds | Groups of agents (pedestrians or drivers) acting on instinctual responses, goal-driven resistance, and heuristics rather than rigid robotic protocols[cite: 20, 25] | The human crowd moving toward and clogging our exit bottlenecks |
| Perception-interaction-planning-control pipeline | The four-stage core framework: observing crowd states, modeling responses, predicting multiscale dynamics, and synthesizing feedback control policies[cite: 20, 22] | Our systematic architecture for designing robot interventions at exits |
| Bird's-Eye View (BEV) | Top-down spatial representation mitigating perspective distortion and occlusions from ground cameras[cite: 26] | Overhead or overhead-projected sensor data framework for tracking exit queues |
| Explicit vs. Implicit Control | Direct commands/physical guidance versus subtle environmental shaping/behavioral nudges (shepherding, barriers)[cite: 33] | Comparing direct gating/barriers versus oscillating/moving pillar nudges |

---

## 3. Key setups and frameworks (so we do not have to re-open the PDF)

**The Four Core Pipeline Components**
1. **Perception**: 
   - *Nonparticipant observation*: Top-down surveillance (counting, point-level/head localization via Faster R-CNN, CLTR transformers, roadside/overhead cameras, Wi-Fi/Bluetooth sniffing)[cite: 26, 27].
   - *Participant observation*: First-person/mobile robot sensors; uses Bird's-Eye View (BEV) transformations to overcome occlusions[cite: 26, 27].
   - *Global state estimation*: Fusing sparse local/global views using PDEs, generative models, and conservation laws (e.g., CIRCLES project fusing RV data)[cite: 28, 32].
2. **Interaction**:
   - *Rule-based*: Social Force Models (SFM) for pedestrians; follow-the-leader, optimal velocity, intelligent driver model (IDM), and Gipps models for traffic[cite: 29, 30].
   - *Data-driven*: Social-LSTM, Social-GAN, graph neural networks (GNNs), transformers, and active probing (where robots intentionally probe human latent states)[cite: 30, 31].
3. **Planning**:
   - Bridging microscopic, mesoscopic, and macroscopic scales[cite: 25, 31]. Incorporates psychological/stress models for humans and kinematic/dynamic constraints for vehicles[cite: 31, 32]. Uses RL, imitation learning (IL), and diffusion models[cite: 32, 33].
4. **Control & Applications**:
   - *Explicit control*: Active guidance, multirobot leaders, exit redirection[cite: 33].
   - *Traffic & Swarm Control*: Lagrangian variable speed limits (e.g., CIRCLES project yielding an 8% density reduction), robotic shepherding (e.g., drone/quadruped bird/sheep herding), and unsignalized intersection management[cite: 35, 36].

---

## 4. Open challenges and realities

| Challenge | Description | Implications for our project |
|---|---|---|
| **Human Irrationality** | Humans deviate from rational economic/physical behavior due to biases (loss aversion, discounting)[cite: 36, 37] | Simple physics or optimal models fail; models must incorporate stochasticity and behavioral quirks |
| **Compliance Uncertainty** | Robots have indirect influence and rely on human cooperation; drivers/pedestrians often ignore guidance[cite: 36, 37] | Robot interventions must be robust to non-compliance or bypass attempts |
| **Privacy & Security** | Granular tracking of trajectories risks leaking sensitive individual data[cite: 37] | Need anonymization/federated approaches when monitoring real-world exit crowds |

---

## 5. How we use it (concrete items)

**U1. Anchor our project in formal literature.** Cite Pan et al. (2026) to establish that our robot-at-the-exit / pillar intervention strategy fits squarely within modern *mixed crowd navigation* and *implicit control* frameworks[cite: 20, 33].

**U2. Adopt the Perception-Interaction-Planning-Control structure.** Organize our methodology and thesis chapters around this four-stage pipeline[cite: 20, 22].

**U3. Justify implicit robotic control.** Use the paper's taxonomy of *implicit control* (shaping crowd flow via movement/barriers without explicit verbal commands) to defend our oscillating/moving robot/pillar designs[cite: 33].

**U4. Acknowledge human irrationality and compliance limits.** Explicitly note in our limitations section that crowd flow models must account for human non-compliance and localized irrationality rather than assuming perfect agent obedience[cite: 36, 37].

---

## 6. What not to claim

- The survey does not propose a single new algorithm; it is a comprehensive review of existing multi-disciplinary literature spanning robotics, traffic engineering, and physics[cite: 20].
- Real-world robot herding and crowd control are still nascent outside of controlled traffic experiments (like CIRCLES) or agricultural settings; active human crowd control remains legally and ethically constrained[cite: 35, 36].

---

## 7. TODO

- [ ] Incorporate Pan et al. (2026) into our literature review and related work section[cite: 20].
- [ ] Align our simulation framework terms with the paper's perception-interaction-planning-control taxonomy[cite: 20, 22].
- [ ] Reference the CIRCLES project findings when discussing real-world density reduction via mobile robotic actuators/pillars[cite: 35].