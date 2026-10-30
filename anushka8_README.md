# anushka8 — GROVE: Grounded Pedestrian Simulation via Natural Language for Interactive Social Robot Navigation

| | |
|---|---|
| **Citation** | D. T. Nguyen, V. Shcherbyna, A. D. Duc, Z. Shen, T. Buiyan, L. Kästner. *GROVE: Grounded Pedestrian Simulation via Natural Language for Interactive Social Robot Navigation.* arXiv:2606.25504v1 [cs.RO] (2026). |
| **File** | `anushka8.pdf` |
| **Type** | Text-to-scenario hierarchical pedestrian simulation framework combining RAG, LLMs, Behavior Trees, Theta* global planning, and Social Force Models[cite: 60, 61]. |
| **Role in our project** | Offers a text-conditioned scenario generator that grounds high-level natural language intent into executable behavior trees, global waypoints, and velocity fields for testing social robot navigation in complex indoor environments[cite: 60, 61]. |

---

## 1. TL;DR

1. Existing pedestrian simulators decouple scenario definition from simulation, forcing manual specification of agent positions and rigid parameters that fail to capture real-world diversity[cite: 61].
2. **GROVE** is a hierarchical text-to-scenario framework that translates free-text prompts into high-fidelity pedestrian simulations across strategic, tactical, and operational layers[cite: 60, 61].
3. Utilizes a Retrieval-Augmented Generation (RAG) module with ChromaDB and a multi-model LLM architecture to synthesize robust Behavior Trees (BTs) without model hallucination[cite: 61, 62].
4. Integrates Theta* global grid planning and velocity fields with a Social Force Model (SFM) to guarantee static obstacle avoidance and realistic multi-agent interactions within ROS2, Isaac Sim, and Gazebo[cite: 60, 61, 62].

---

## 2. Definitions we will reuse

| Term | Meaning in the paper | Our project meaning |
|---|---|---|
| Regions of Interest (RoIs) | Semantic spaces (rooms, zones, hallways) encoded in a YAML world representation for LLM spatial grounding[cite: 62] | Spatial zones used to anchor agent spawn points and navigation goals |
| Behavior Trees (BTs) | Hierarchical symbolic programs acting as a contract between language intent and simulation execution[cite: 63] | Structured decision-making framework governing long-horizon agent logic |
| RAG Module for BTs | ChromaDB vector database retrieval injecting only task-relevant node descriptions and constraints into the LLM context[cite: 62, 63] | Mechanism to prevent LLM node hallucinations and speed up scenario generation |
| Theta* Global Planner | Any-angle grid path planning algorithm computing smooth, collision-free paths around static obstacles[cite: 64] | Global path planner bridging symbolic intent and geometric constraints |

---

## 3. Key setups and numbers (so we do not have to re-open the PDF)

**System Architecture & Components**
- **LLM Multi-Model Strategy**: Lightweight model (`Gemini 2.5 Flash` or local `Qwen-3-0.6B`) for rapid RoI selection; advanced reasoning model (`Gemini 3 Pro` or `Qwen-3-4B`) for Behavior Tree synthesis[cite: 62].
- **World Representation**: Encoded via `world.yaml` storing zone metadata, doors, entities, and geometric boundaries[cite: 62].
- **Optimized Presets**:
  - *Emergency Mode*: Targets evacuations, high density, exit-seeking behavior, and utilizes velocity fields for group flow optimization[cite: 64].
  - *Queuing Mode*: Targets structured wait lines and service counters using dedicated queue subtrees and sequential goal allocation[cite: 64].
  - *Normal Mode*: Daily-life mixed scenarios with full social behaviors (conversations, roaming, group formation)[cite: 64].

**Quantitative Performance Highlights**
- Evaluated against Text-Crowd and TRACE across hospital, office, and residential environments[cite: 64].
- **VLM Evaluation (GPT-5 scoring on a 0-10 scale)**: GROVE achieved top scores in Prompt Alignment, Plausibility, and Visual Realism across Normal, Queuing, and Emergency presets (e.g., Queuing average score of 6.62 vs TRACE's 4.39 and Text-Crowd's 4.61)[cite: 66].
- **Token Efficiency**: Optimized presets reduced LLM token usage by 35% to 49% across cold-cache test runs compared to vanilla generation[cite: 67].

---

## 4. How we use it (concrete items)

**U1. Ground natural language prompts into simulation structures.** Adopt GROVE's world YAML semantic representation (`world.yaml`) to map physical environments into LLM-readable spaces[cite: 62].

**U2. Prevent LLM hallucinations via RAG.** Implement a vector database (ChromaDB) retrieval step to supply exact behavior node schemas and constraints to language models during scenario creation[cite: 62, 63].

**U3. Ensure geometric feasibility via global planning.** Couple high-level Behavior Trees with Theta* grid planning to eliminate unrealistic wall penetrations and straight-line paths through obstacles[cite: 63, 64].

**U4. Utilize scenario-specific presets.** Apply mode-specific configuration overlays (Emergency, Queuing, Normal) to optimize token efficiency and constrain agent behavioral logic based on operational requirements[cite: 64].

---

## 5. Tested code / Framework integration workflow

**Pipeline Execution Flow**

User Prompt + World YAML
│
├──> Lightweight LLM (RoI Selection) ──> Filtered Spatial Context
├──> ChromaDB RAG (Behavior Nodes)  ──> Task-Relevant Node Schemas
│
▼
Advanced LLM (Behavior Tree Synthesis)
│
▼
Theta* Global Planner (Waypoint Injection) ──> Static Obstacle Avoidance
│
▼
Simulator Execution (Isaac Sim / Gazebo / RViz2 via ROS 2) + SFM / Velocity Fields

---

## 6. What not to claim

- GROVE's current architecture assumes a static obstacle map and does not natively handle dynamic environmental changes like moving furniture or temporary obstructions without re-planning[cite: 67].
- Combining multiple state-of-the-art models introduces high computational costs and inference overhead compared to traditional rule-based simulators[cite: 67].

---

## 7. TODO

- [ ] Implement `world.yaml` semantic structure for our local simulation testing environment[cite: 62].
- [ ] Set up ChromaDB vector database retrieval for behavior node generation[cite: 62, 63].
- [ ] Integrate Theta* waypoint planning with Behavior Tree execution to prevent wall collisions[cite: 63, 64].
- [ ] Explore world-level precomputed caching to mitigate LLM inference latency[cite: 67].