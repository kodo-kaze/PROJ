# Project Research & Literature Review Repository

Welcome to the collaborative research repository for our final year project. 

To ensure a streamlined, decentralized research workflow, every team member is expected to conduct independent literature reviews, analyze methodologies, and archive findings within this shared space before we synthesize our core system design.

> **Communication Protocol:**  
> All project communication and updates will take place on Discord. Please ensure you have the Discord application installed on your phone and desktop and remain logged in.
> - **Text Channel:** `#stella` (for text discussions and async updates)  
> - **Voice Channel:** `hollow-purple` (for meetings and sync-ups)
---

## 1. Onboarding & Workflow Protocol

All members have direct push access via the organization. Please adhere strictly to the following procedures to avoid merge conflicts and maintain documentation integrity:

### Step 1: Clone the Repository
Clone the repository to your local machine using SSH or HTTPS:
```bash
git clone https://github.com/kodo-kaze/PROJ.git
cd PROJ
```

### Step 2: Workspace Directory Allocation

- Create a dedicated top-level directory labeled with your **Name** or **Handle** (e.g., `bimbok/`, `ligheangle/`).
- All your notes, research assets, data models, and literature analyses **must reside exclusively within your designated folder**.
- You have complete autonomy over the internal folder structure within your workspace; organize it in a way that best supports your workflow.


> **Important Boundary Rule:** Modifying, overwriting, or deleting files within another member's directory is **strictly prohibited**. Cross-referencing and reading your peers' research for knowledge sharing is strongly encouraged.
> 

## 2. Documentation & Tooling Guidelines

Team members are free to use their preferred documentation environment:

- **Obsidian / Markdown (Recommended):** If you use Obsidian or standard Markdown, ensure internal relative links remain consistent within your folder.
- **Word / Alternative Document Processors:** If using Microsoft Word, Google Docs, or LaTeX, please export and commit a compiled **`.pdf` version** alongside the source file to allow quick in-browser preview for the rest of the team.

## 3. Sourcing & Asset Archiving

Comprehensive literature tracking is essential for our technical project report and literature review defense. For every paper, article, or system you explore:

- **Include Source Metadata:** Document full URLs, DOIs, author names, publication year, and core problem statements.
- **Archive Artifacts:** Place downloaded research paper PDFs, whitepapers, datasets, or reference diagrams directly in an `assets/` or `references/` subfolder within your workspace.
- **Summarize Key Takeaways:** Outline how the research directly applies to our project's technical architecture, algorithms, or evaluation metrics.

## 4. Version Control & Synchronization Discipline

To prevent merge divergence and accidental data loss:

1. **Pull Regularly:** Always run `git pull origin main` before starting a work session.    
2. **Commit Frequently:** Write meaningful, descriptive commit messages describing what was reviewed or added:
    ```bash
    git add <your-folder>/
    git commit -m "docs: add literature review on crowd density estimation models"
    ```
    
3. **Push on Intervals:** **Always commit and push your progress before taking breaks, switching machines, or concluding your work session.**

## 5. Proposed Internal Folder Structure (Example)

You may design your workspace as you see fit, but here is a recommended template:


```plaintxt
your-folder-name/
├── 01_papers/           # Downloaded research paper PDFs
├── 02_notes/            # Markdown / Obsidian notes & analysis
├── 03_exports/          # Compiled PDFs of your summaries (if applicable)
└── README.md            # Brief summary of your individual focus area
```

---

## 6. Phase 2: Algorithm Formulation Guidelines

As we transition from literature review to system design, every member is required to formulate and document an algorithmic approach or decision model based on the research they have gathered. 

Your proposed algorithm should address a specific aspect of the Pedestrian Crowd Problem (such as density classification, bottleneck detection, dynamic path rerouting, or anomaly detection). Rather than writing unstructured scripts, structure your proposal using formal pseudocode backed by clear inputs, outputs, and logic.

### Acceptable Algorithm Patterns & Examples

#### Pattern A: Threshold & State Classification Logic
Used for categorizing crowd hazard states, bottleneck risks, or triggering alerts based on empirical metrics.

```text
Algorithm: ClassifyCrowdHazardState
Input: Density ρ (pedestrians/m^2), AverageVelocity v (m/s), CorridorWidth W (m)
Output: HazardLevel (SAFE, HIGH_THROUGHPUT, CRITICAL_JAM)

1. Set CriticalDensityThreshold = 4.0
2. Set VelocityStallThreshold = 0.2
3. Set MinimumWidth = 1.0

4. If W < MinimumWidth AND ρ >= CriticalDensityThreshold AND v <= VelocityStallThreshold Then:
5.      Return HazardLevel.CRITICAL_JAM       // Faster-is-Slower clogging regime
6. Else If ρ >= CriticalDensityThreshold AND v > VelocityStallThreshold Then:
7.      Return HazardLevel.HIGH_THROUGHPUT    // Faster-is-Faster evacuation regime
8. Else:
9.      Return HazardLevel.SAFE

```

#### Pattern B: Graph & Routing Optimization (DAA)

Used for evacuation route planning, exit load balancing, or detour calculation across a mapped facility.

```text
Algorithm: DynamicEvacuationPathfinding
Input: Facility Graph G(V, E), Source s, Target Exits Set X, RealTimeDensityMap D
Output: OptimalEvacuationPath P

1. Initialize MinPriorityQueue Q
2. Initialize CostMap dist with ∞ for all vertices v in V, set dist[s] = 0
3. Q.insert(s, 0)

4. While Q is not empty:
5.      u = Q.extractMin()
6.      If u in X:
7.          Return reconstructPath(u)
8.      For each neighbor v of u:
9.          weight = calculateEdgeCost(Edge(u, v), D[v], mergeAngle(u, v))
10.         If dist[u] + weight < dist[v]:
11.             dist[v] = dist[u] + weight
12.             parent[v] = u
13.             Q.insertOrUpdate(v, dist[v])
```

#### Pattern C: Computer Vision & Pipeline Processing

Used for frame-by-frame inference, density map regression, optical flow tracking, and alerting.

```text
Algorithm: ProcessCrowdAnomaly
Input: VideoStream S, RegionOfInterest ROI
Output: RealTimeAlertStatus

1. For each incoming frame F in S:
2.      F_cropped = cropToROI(F, ROI)
3.      density_map = NeuralNetInference(F_cropped)
4.      count = IntegrateDensity(density_map)
5.      vectors = ComputeOpticalFlow(F_previous, F_cropped)
6.      avg_velocity = Mean(vectors)
7.      
8.      status = ClassifyCrowdHazardState(count / Area(ROI), avg_velocity, ROI.width)
9.      If status == HazardLevel.CRITICAL_JAM:
10.         TriggerAudibleAlarm()
11.         BroadcastMQTTAlert(ROI.id, status)
12.     F_previous = F_cropped

```

---

### Documentation Requirements for Your Workspace

In your personal folder, create a dedicated file (e.g., `03_algorithms/algorithm_proposal.md`) containing:

1. **Problem Statement:** What specific sub-problem does your algorithm target?
2. **Inputs & Outputs:** The exact parameters/data required and the generated output.
3. **Formal Pseudocode:** Clean, numbered step-by-step logic following one of the patterns above.
4. **Time/Space Complexity:** Brief theoretical bounds (e.g., $O(V \log V + E)$ or $O(N \times M)$ per frame).
5. **Literature Justification:** A short explanation citing the research paper or principle that supports your design.


For any synchronization issues, branch questions, or merge conflicts, coordinate immediately with the [bimbok](https://bratikmkj.vercel.app/) or [aditya](https://adityapaul26.vercel.app/).
