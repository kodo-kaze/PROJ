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


## 6. Phase 2: Detailed Guide for Algorithm Formulation

This guide provides a step-by-step breakdown of how to convert your research notes into an individual algorithm proposal for our final-year project synopsis.
### 1. What Are We Doing in Phase 2?

In **Phase 1**, everyone gathered research papers, articles, and concepts related to the **Pedestrian Crowd Problem**.

In **Phase 2**, each member selects **one specific sub-problem** from their research and creates a structured algorithm to solve or analyze it.


> **Key Rule:** You are **not** required to write a full working software program right now. You are writing **formal Pseudocode**—a clean, step-by-step logical blueprint showing inputs, processing steps, and expected outputs.
> 
## 2. Step-by-Step Instructions

1. **Synthesize All Research Areas:** Your algorithm should combine our collective research findings into a full multi-stage process:
	1. Extracting crowd density and regions from input frames.
	2. Analyzing flow vectors and disorder metrics.
	3. Classifying the crowd hazard state (determining if it is a fast evacuation or a dangerous bottleneck jam).
	4. Generating the safest evacuation route and adjusting traffic/exit flows dynamically.
    
2. **Define Inputs and Outputs:** Identify the exact data needed (e.g., video frame, crowd count, corridor width) and what the algorithm produces (e.g., alert trigger, safe route, hazard score).
3. **Write Numbered Pseudocode:** Express the logic using standard programming structures (loops, variables, conditions) in numbered steps.
4. **Link to Research:** Add 2–3 sentences explaining which paper or finding inspired this design.
5. **Push to Your Folder:** Save your work in your personal workspace directory (e.g., `03_algorithms/algorithm_proposal.md`).
## 3. Choose One of Three Common Patterns

Select the pattern that best fits your individual research area:

### Pattern A: Threshold & Decision Logic (Best for Anomaly & Safety Rules)

- **When to use:** If your research focuses on crowd thresholds, safety limits, panic detection, or emergency alert states.   
    
- **Core Idea:** Take measured variables (such as crowd density $\rho$, movement speed $v$, and walkway width $W$) and evaluate logical conditions to determine system state.


```
Algorithm: ClassifyCrowdHazardState
Input: 
    Density ρ (pedestrians per m^2), 
    AverageVelocity v (meters per second), 
    CorridorWidth W (meters)
Output: 
    HazardLevel (SAFE, HIGH_THROUGHPUT, CRITICAL_JAM)

1. Set CriticalDensityThreshold = 4.0
2. Set VelocityStallThreshold = 0.2
3. Set MinimumWidth = 1.0

4. If W < MinimumWidth AND ρ >= CriticalDensityThreshold AND v <= VelocityStallThreshold Then:
5.      Return HazardLevel.CRITICAL_JAM       // Severe bottleneck clogging
6. Else If ρ >= CriticalDensityThreshold AND v > VelocityStallThreshold Then:
7.      Return HazardLevel.HIGH_THROUGHPUT    // Fast, efficient evacuation
8. Else:
9.      Return HazardLevel.SAFE
```

### Pattern B: Graph & Routing Logic (Best for Evacuation & Path Planning)

- **When to use:** If your research focuses on directing people to exits, load balancing corridors, or avoiding choke points.
    
- **Core Idea:** Treat the facility floor plan as a network graph ($G = (V, E)$) and compute the lowest-cost evacuation route.

```
Algorithm: DynamicEvacuationPathfinding
Input: 
    Facility Graph G(V, E), 
    Source Node s, 
    Set of Safe Exits X, 
    RealTimeDensityMap D
Output: 
    OptimalEvacuationPath P

1. Initialize MinPriorityQueue Q
2. Initialize CostMap dist with ∞ for all vertices v in V, and set dist[s] = 0
3. Q.insert(s, priority=0)

4. While Q is not empty:
5.      u = Q.extractMin()
6.      If u is in Set of Safe Exits X:
7.          Return reconstructPath(u)
8.      For each neighbor v of node u:
9.          weight = calculateEdgeCost(Edge(u, v), D[v], mergeAngle(u, v))
10.         If dist[u] + weight < dist[v]:
11.             dist[v] = dist[u] + weight
12.             parent[v] = u
13.             Q.insertOrUpdate(v, dist[v])
```

### Pattern C: Vision & Pipeline Processing (Best for AI & CCTV Analytics)

- **When to use:** If your research focuses on image segmentation, feature extraction (e.g., LBP/GLCM), or video tracking.  
    
- **Core Idea:** Process an incoming camera frame through discrete computer vision stages to compute metrics and trigger events.
    
```
Algorithm: ProcessCrowdAnomaly
Input: 
    VideoStream S, 
    RegionOfInterest ROI
Output: 
    RealTimeAlertStatus

1. For each incoming frame F in VideoStream S:
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

## 4. Required Note Template

Create a file at `03_algorithms/algorithm_proposal.md` inside your personal workspace folder and fill out these 5 sections:12

```markdown
# Algorithm Proposal: [Name of Your Algorithm]

## 1. Problem Statement
Explain in 2–4 sentences what exact problem this algorithm addresses.

## 2. Input and Output
- **Inputs:** List all inputs (e.g., Video frame, Density map, Graph layout).
- **Outputs:** List all outputs (e.g., Alert level, Coordinates, Next safe node).

## 3. Pseudocode
[Paste your clean, numbered step-by-step pseudocode here]

## 4. Complexity Analysis
- **Time Complexity:** E.g., O(N) per frame, or O(V log V + E) for graphs.
- **Space Complexity:** E.g., O(N) for grid storage.

## 5. Research Justification
Briefly cite the paper, article, or formula that inspired your logic and explain why this approach works.
```

For any synchronization issues, branch questions, or merge conflicts, coordinate immediately with the [bimbok](https://bratikmkj.vercel.app/) or [aditya](https://adityapaul26.vercel.app/).
