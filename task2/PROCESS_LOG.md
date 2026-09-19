# COSC2669 Task 2 - Process Log

Arya Khamkar (s4152566)

This log feeds three things: the **Appendix** (process, prompts, critical
assessment, issues and fixes), the **Generative AI Attribution** statement, and
the **Condition 3 AI Declaration**. It must stay consistent with Appendices C
and D of the submitted report.

Condition 3 reminder: AI is allowed for code assistance when planning and running
fairness-aware analyses, for consulting concepts, for brainstorming applications,
and for surface-level polishing. AI must **not** produce final answers, analysis,
argument, code, design decisions or reflection.

## 1. Generative AI use log

| # | Date | Tool | Part | What I asked | What it gave | Accepted / rejected / revised, and why |
|---|---|---|---|---|---|---|
| 1 | 16 Sep 2026 | Claude (Cowork) | All | A step-by-step plan for completing Task 2 | A plan covering setup and Parts 1-3: CV splitters, metrics, Fairlearn proxy groups, write-up structure, Overleaf finishing | Revised the plan rather than following it exactly. I kept the main structure but reordered some of the notebook work so that I completed the cross-validation and learning-curve checks before finalising the fairness discussion, and I selected proxy groups based on the variables available in my datasets. I also checked the suggested references before using them. |
| 2 | 16 Sep 2026 | Claude (Cowork) | Setup | To set up the Task 2 scaffolding | `task2/` folder structure, `requirements.txt`, `.gitignore`, empty notebook with section headings, `main.tex` skeleton, starter `references.bib`, this process-log template | Accepted the folder structure and section order because they matched the Task 2 requirements and made the work easier to organise. I checked the bibliography entries I used against the original papers and corrected details that did not match before using them in the report. |
| 3 | 16-17 Sep 2026 | Claude (Cowork) | Part 2 | A code skeleton with blanks and explanations for the Part 2 analysis | Section-by-section structure with `___` blanks and `# TODO` markers, plus the logic behind each step and checks to run after each block | _[FILL IN: what you accepted, what you wrote differently, and why]_ |
| 4 | 17-19 Sep 2026 | Claude (Cowork) | Part 2 | Help debugging errors and checking outputs | Explanations of the git authentication and rebase errors, the blank-PNG cause, and confirmation of which figures and tables matched the notebook | _[FILL IN]_ |
| 5 | 19 Sep 2026 | Claude (Cowork) | Part 2 | A review of the Part 2 notebook against the rubric | Identified that the fairness metrics were computed on training data, that the SVR learning-curve description did not match the figure, and that section (c) mixed up two groupings | _[FILL IN: which points you accepted and what you changed]_ |
| 6 | 19 Sep 2026 | Claude (Cowork) | Appendices | Help documenting the issues encountered for Appendix C | Table structure plus a factual reconstruction of the problems encountered during the work | _[FILL IN]_ |
| 7 | 19 Sep 2026 | Claude (Cowork) | Part 1, Part 3 | LaTeX formatting and layout assistance | Arrangement of my written sections into the course template, table and figure code for my own results, and bibliography setup | _[FILL IN]_ |
| 8 | _[FILL IN: date]_ | _[FILL IN: the other AI tool used]_ | Parts 1-2 | _[FILL IN: what you asked]_ | Draft text for Parts 1 and 2 | **Rejected.** The draft contained results that did not match my notebook (for example cross-validation and fairness figures I had not produced), so I did not use it. I rewrote both parts from my own notebook outputs. |

Row 8 must be completed if another AI tool was used at any point. Under-declaring
is the main risk in a Condition 3 assessment.

## 2. Issues encountered and fixes (AI-related or not)

| # | Date | Part / step | Problem | How I diagnosed it | Fix |
|---|---|---|---|---|---|
| 1 | 16 Sep 2026 | Setup | Task 1 repo on GitHub has no data files, so the notebook can't run from a fresh clone | Checked repo contents | Copied the CSVs into `task2/data/`, kept them out of git via `.gitignore`, documented the sources in the README |
| 2 | 16 Sep 2026 | Setup | `git push` failed: "Authentication failed" and "Invalid username or token. Password authentication is not supported for Git operations" | Read the error output | Created a personal access token with `repo` scope, stored it in the macOS keychain, pushed with `--set-upstream` |
| 3 | 16 Sep 2026 | Setup | Push rejected as non-fast-forward; `git pull --rebase` gave "CONFLICT (add/add)" on `s4152566-task1-analysis.ipynb` because the local folder and the GitHub repo had unrelated histories | Compared the local and remote versions of the notebook | Confirmed the local working copy matched GitHub's version, kept GitHub's (`git checkout --ours`), continued the rebase, pushed |
| 4 | 18 Sep 2026 | Part 2(c) | Fairness metrics were computed on training data; group metrics were implausibly high (DataPlan accuracy 0.970 / 0.977 against 0.93 under CV) | Compared the fairness output with the cross-validation results | Switched to out-of-fold predictions (`cross_val_predict` for churn, held-out fold predictions for attendance); data-plan recall fell from 0.750 to 0.512 |
| 5 | 18 Sep 2026 | Part 2(b) | `figures/attendance_learning_curve.png` was a blank ~15 KB image | Opened the file | `savefig` was in a later cell, after `plt.show()` had cleared the figure; moved it above `plt.show()` in the same cell, producing one figure per model |
| 6 | 19 Sep 2026 | Part 2(b) | Learning-curve summary table showed `std = NaN` for every training size | Checked the aggregated table | Not fixed: training sizes are fractions of each fold's length and `TimeSeriesSplit` folds differ, so each size occurs once. Reported as a limitation in Section 2.2 |
| 7 | 19 Sep 2026 | Part 2(b) | Held-out prediction arrays re-initialised inside the model loop | Reviewed the loop | Verified the retained predictions were XGBoost, the intended model; noted the fragility rather than restructuring |
| 8 | 19 Sep 2026 | Part 2(a) | CV attendance results not directly comparable with Task 1 (every fold capped at 5,000 training rows) | Compared CV MAE 1,386 with Task 1's full-data 958 | Compared against the Task 1 XGB-5k result (MAE 1,056) instead and stated the limitation in Section 2.1 |
| 9 | 19 Sep 2026 | Overleaf | Compile silently used an old PDF; new sections never appeared | Read the compile log: a stray line ("Claude Desktop (macOS), Connected") had been pasted above `\documentclass` | Deleted the stray lines so the file begins with `\documentclass`; recompiled |
| 10 | 19 Sep 2026 | Overleaf | All citations rendered as `[?]` and References was empty | Checked the bib filename against `\bibliography{}` | The file is `99-references.bib`, not `references.bib`; updated the `\bibliography{}` argument and recompiled twice |

## 3. Key decisions (my reasoning, in my own words)

| Decision | Options considered | Chosen | Why |
|---|---|---|---|
| CV splitter, attendance | | 5-fold `TimeSeriesSplit` | _[FILL IN]_ |
| CV splitter, churn | | 10-fold `StratifiedKFold` | _[FILL IN]_ |
| SVR subsampling in folds | | 5,000 rows, as in Task 1 | _[FILL IN]_ |
| Fairness tool | Fairlearn / AIF360 / What-If | Fairlearn | _[FILL IN]_ |
| Proxy groups, churn | | DataPlan, CustServCalls >= 4, tenure quartiles | _[FILL IN]_ |
| Proxy groups, attendance | | `H Pop` quartiles, era, attendance bands | _[FILL IN]_ |
| Part 1 paper | 5 options in the brief | Trippas, Spina & Scholer (2025) | _[FILL IN]_ |
| Part 1 case study | | Online product comparison | _[FILL IN]_ |
| Peer video reviewed | | Surbhi Chauhan, "Understanding E-commerce Customers" | Assigned via Canvas |

## 4. Screenshot checklist (for Appendix D)

- [ ] AI conversation showing the code skeleton with blanks
- [ ] The fairness output before and after the out-of-fold fix
- [ ] An error message and its resolution (git or LaTeX)
- [ ] Notebook run showing all cells executed in order
- [ ] GitHub commit history
