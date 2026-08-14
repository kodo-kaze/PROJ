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

For any synchronization issues, branch questions, or merge conflicts, coordinate immediately with the bimbok or aditya.