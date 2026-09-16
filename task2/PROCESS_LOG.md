# COSC2669 Task 2 - Process Log

Arya Khamkar (s4152566)

This log feeds three things: the **Appendix** (process, prompts, critical
assessment, issues and fixes), the **Generative AI Attribution** statement, and
the **Condition 3 AI Declaration**. Add a row every time you use an AI tool,
and fill in the last two columns yourself. The rubric rewards critical
assessment of intermediate outputs.

Condition 3 reminder: AI is allowed for code assistance when planning and running
fairness-aware analyses, for consulting concepts, for brainstorming applications,
and for surface-level polishing. AI must **not** produce final answers, analysis,
argument, code, design decisions or reflection.

## 1. Generative AI use log

| # | Date | Tool | Part | What I asked | What it gave | Accepted / rejected / changed, and why | How it affected my work | Screenshot |
|---|---|---|---|---|---|---|---|---|
| 1 | 16 Sep 2026 | _[tool used for the plan]_ | All | A step-by-step plan for doing Task 2 | A step-by-step plan for Step 0 and Parts 1-3 (splitters, metrics, Fairlearn groups, write-up structure, Overleaf finishing) | _[fill in]_ | _[fill in]_ | |
| 2 | 16 Sep 2026 | Claude (Cowork) | All | Asked Claude to complete Task 2 from the plan, including final code, analysis and writing | Declined to produce final assessable content, citing Condition 3. Listed the help it could give instead (setup, concept explanations, debugging, rubric checks, formatting) | _[fill in]_ | _[fill in]_ | |
| 3 | 16 Sep 2026 | Claude (Cowork) | Setup | Set up Step 0: process log, Overleaf skeleton, repo/folder structure | Created `task2/` folder (data copy, figures/outputs/screenshots folders, README, requirements, .gitignore), empty notebook with section headings only, `main.tex` skeleton (section headings, table/figure LaTeX patterns), starter `references.bib` (scikit-learn, XGBoost, Fairlearn, datasets), and this log template | _[fill in, e.g. did you keep the section structure? did you check the bib entries?]_ | _[fill in]_ | |
| 4 | | | | | | | | |

## 2. Issues encountered and fixes (AI-related or not)

| # | Date | Part / step | Problem | How I diagnosed it | Fix | Evidence (commit / screenshot) |
|---|---|---|---|---|---|---|
| 1 | 16 Sep 2026 | Setup | Task 1 repo on GitHub has no data files, so the notebook can't run from a fresh clone | Checked repo contents | Copied the CSVs into `task2/data/`, kept them out of git via `.gitignore`, and documented the sources in README | |
| 2 | | | | | | |

## 3. Key decisions (your reasoning, in your words)

| Decision | Options considered | Chosen | Why |
|---|---|---|---|
| CV splitter, attendance | | | |
| CV splitter, churn | | | |
| SVR subsampling in folds | | | |
| Fairness tool | Fairlearn / AIF360 / What-If / other | | |
| Proxy groups, churn | | | |
| Proxy groups, attendance | | | |
| Part 1 paper | | | |
| Part 1 case study | | | |
| Peer video reviewed | | | |

## 4. Screenshot checklist

- [ ] This setup conversation
- [ ] Each AI prompt where you rejected or changed the output
- [ ] Error messages and how you fixed them
- [ ] Final CV table and learning curve output in the notebook
- [ ] Fairlearn `MetricFrame.by_group` output
- [ ] GitHub commit history before submitting
