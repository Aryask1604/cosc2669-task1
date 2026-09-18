# Task 2: Finish Plan

Written Friday 18 Sep, ~9:30pm. Target: submit **before Saturday 4:59pm** so the late penalty stays at one day (10%, i.e. 2 marks).

Total working time below is about 7 hours. Sleep in between if you need to — the penalty is the same anywhere before Saturday 4:59pm.

**Condition 3 reminder:** AI may help you plan, explain concepts, debug code and polish surface wording. The analysis, argument, reflection and final text must be yours, and every use goes in the declaration.

| Step | What | Time |
|---|---|---|
| A | Fix figures, commit | 20 min |
| B | Canvas peer video | 10 min |
| C | Part 3 peer review (4 marks) | 60 min |
| D | Part 1 executive summary (6 marks) | 150 min |
| E | Part 2 write-up (7 marks) | 90 min |
| F | Overleaf assembly | 40 min |
| G | Process log + appendix | 30 min |
| H | Declaration + AI attribution | 30 min |
| I | Final checks and submit | 20 min |

---

## Step A: Fix the figures and commit (20 min)

1. Open `task2/task2-analysis.ipynb`.
2. In **both** learning-curve cells, move `plt.savefig("figures/<name>.png", dpi=200, bbox_inches="tight")` to the line **before** `plt.show()`, inside the same cell. Use `lc_attendance.png` and `lc_churn.png`.
3. Delete the old separate `plt.savefig` cell near the end (it only saves an empty figure).
4. **Kernel → Restart & Run All.** It takes a few minutes because of the SVR.
5. Open both PNGs in Finder and confirm they aren't blank.
6. Save, then in Terminal:

```bash
cd ~/cosc2669_task1
git add task2 && git commit -m "Task 2: fairness on held-out predictions, learning curves" && git push
```

If Run All fails, stop and send me the error. Don't spend more than 20 minutes here.

---

## Step B: Canvas peer video (10 min)

Canvas → COSC2669 → Individual Task 1 Part 2 → **Assigned Peer Reviews** in the right sidebar (also check your To Do list).

- **If a video is assigned:** open it, note the student's name or submission title, and screenshot the assignment page.
- **If nothing is assigned:** fill in the request form from the brief **now**, screenshot that you submitted it, and write one sentence in Part 3 of your report explaining that no video was assigned and that you submitted the request form on this date. Then skip Step C and put the hour into Part 1.

---

## Step C: Part 3 peer review, ~400 words (60 min)

**Watch (25 min)**

1. First pass, no notes. Just watch.
2. Second pass with the video paused often. Write timestamped notes in three columns:

| Time | What they said/did | My judgement |
|---|---|---|

Cover all three criteria:

- **Soundness and significance:** is the analysis correct? Are claims backed by results? Do they mention limitations, data quality, evaluation choices?
- **Executive-level insights:** would a manager know what to *do* after watching? Is it business language rather than model jargon? Is there a recommendation, a cost or a risk?
- **Presentation quality:** structure, pacing, slide readability, audio, whether they went over time.

**Write (35 min)** — roughly:

- Which video (name or title) and a 60-word summary of what they presented.
- Soundness and significance, about 90 words, with **one specific example with a timestamp**.
- Executive-level insights, about 90 words, again with an example.
- Presentation quality, about 70 words.
- 3–4 concrete suggestions, about 80 words. "Add the dollar value of the retention offer on slide 4" beats "add more detail".
- One positive closing sentence.

Keep it professional: judge the work, not the person. Paste into a word counter and aim for 380–420.

---

## Step D: Part 1 executive summary, 750–1000 words (150 min)

### D1. Pick the paper (5 min)

Suggested: **Trippas, Spina & Scholer (2025), "Adapting generative IR systems to users, tasks, scenarios"** (doi 10.1007/978-3-031-73147-1_4). It's clearly data science, and it connects to your NBL marketing context. Get it through the RMIT Library: library.rmit.edu.au → search the title → sign in with your student account. If you can't get the full text in 10 minutes, switch to an open-access option (Barrowcliffe et al. 2025, or the AIATSIS 2020 Code of Ethics) rather than losing time.

### D2. Read with a purpose (45 min)

Don't read start to finish. Go: abstract → introduction → conclusion → section headings → then the parts that answer your questions. Take notes under exactly these headings, since they become your sections:

1. **Motivation:** what problem or gap prompted this paper?
2. **Aims / research questions:** what does it set out to do?
3. **Contributions:** what does it actually deliver (framework, findings, method, agenda)?
4. **Audience:** who is it written for (researchers, practitioners, policymakers)?
5. **Challenges** it identifies (list them).
6. **Opportunities** it identifies (list them).
7. **The paper's own examples** — list these so you can be sure to avoid reusing them.

### D3. Choose your case study (20 min)

Brainstorm 2–3 candidates, then pick the one that best shows *most of* the challenges and opportunities you listed. It must not be one of the paper's own examples. Ideally it links to your Part 2 work, which makes the report feel coherent.

Test each candidate:

- Can I describe it concretely (who, what, what data)?
- Does it show at least 2 challenges **and** 2 opportunities from my list?
- Can I cite a real source for it?

### D4. Find 5–10 peer-reviewed citations (25 min)

This is where you lost marks in Task 1 ("needs more peer-reviewed references, not just online resources"). On **Google Scholar**, search phrases from your notes, e.g. "conversational search evaluation", "user-centred information retrieval", "responsible AI deployment" with "case study". For each: confirm it's a journal or conference paper, click **Cite → BibTeX**, and paste into `references.bib`. Note in one line where you'll use it.

### D5. Write (55 min) — suggested word budget

| Section | Words |
|---|---|
| Purpose and audience | 120 |
| Main argument and contributions | 220 |
| Case study | 250 |
| Challenges shown by the case | 180 |
| Opportunities | 150 |
| Conclusion | 80 |

That's about 1,000; trim to fit 750–1000 excluding references. Cite with `\citep{key}` on every claim that isn't yours. No code or pseudocode.

---

## Step E: Part 2 write-up (90 min)

You already have every number. Don't re-run anything. Aim for about 1,200–1,500 words across (a)–(d).

**Your numbers**

- **Churn CV (10-fold stratified):** XGB accuracy 0.932 ± 0.011, precision 0.847 ± 0.042, recall 0.650 ± 0.087, F1 0.732 ± 0.058, ROC-AUC 0.898 ± 0.039, PR-AUC 0.802 ± 0.064. SVM: 0.917, 0.860, 0.518, 0.643, 0.890, 0.767.
- **Task 1 single split:** XGB 0.930 / 0.805 / 0.680 / 0.737 / 0.873 / 0.787; SVM 0.918 / 0.818 / 0.557 / 0.663 / 0.887 / 0.754.
- **Attendance CV (TimeSeriesSplit, 5 folds, 5k subsample):** SVR MAE 1655 ± 327, R² 0.353 ± 0.048; XGB MAE 1386 ± 282, R² 0.480 ± 0.106. Per fold, XGB MAE runs 1580 → 1700 → 1445 → 1200 → 1006.
- **Task 1:** SVR-5k MAE 1247, XGB-5k 1056, XGB-full 958.
- **Fairness, churn (out-of-fold):** data plan recall 0.512 vs no plan 0.677; selection rate 0.051 vs 0.135; 4+ service calls recall 0.826 (n=267) vs 0.580 (n=3066).
- **Fairness, attendance (held-out):** MAE by market-size quartile 1764 / 1359 / 1239 / 926; signed error −279 / −464 / −380 / −506; by attendance band, signed error +1328 for the lowest band and −1310 for the highest.

**(a) Cross-validation, about 400 words.** Why StratifiedKFold for churn and TimeSeriesSplit for attendance; why the scaler goes inside the Pipeline; why you kept the 5k subsample. Compare with Task 1: is each Task 1 value inside mean ± std? Note that churn results are consistent but attendance CV error is much higher than Task 1's, and that fold 5 (the most recent games, MAE 1006) is close to Task 1's 1056 — so the earlier folds are harder, which says something about drift and about how favourable your Task 1 test period was. Remaining biases: no tuning, one seed, subsample, no XGB-full in CV.

**(b) Learning curves, about 300 words + figures.** What the gap between train and validation shows, whether validation is still improving at the largest size, and what that means for your Task 1 claim that XGBoost's advantage wasn't only a data-size effect. Be honest if it partly was.

**(c) Fairness, about 400 words + tables.** Say you used Fairlearn and why (MetricFrame gives per-group metrics and differences and works with any scikit-learn model). Say that you had no protected attributes, so you used proxy groups, and that this is itself a limitation. Report group sizes every time. The headline findings are the data-plan recall gap and the 4+ calls group. Then the crucial distinction: how much of each gap is the model versus different base rates in the data? In NBL terms: members the model misses never get a retention offer, and the attendance model under-predicts big-market games, which skews promotional spending.

**(d) Risks, about 300 words in three paragraphs.** Security: membership inference and data poisoning against a deployed model, and what NBL would do about it. Privacy: Privacy Act 1988 and the APPs — APP 3 (collection), APP 6 (use and disclosure), APP 7 (direct marketing), APP 11 (security). Note that the churn data is semi-synthetic with no direct identifiers, but a real NBL membership dataset would be personal information. Ethics: targeted retention offers, fairness between fan groups, transparency, and the risk of price or offer discrimination. Cite 2–3 sources here.

---

## Step F: Overleaf assembly (40 min)

1. Upload the PNGs from `task2/figures/` into the Overleaf project (make a `figures` folder there).
2. Paste your text into the sections, replacing each placeholder.
3. Build the tables using the `booktabs` pattern in `main.tex`. Every table and figure needs `\caption` and `\label`, referenced from the text with `Table~\ref{...}` / `Figure~\ref{...}`.
4. Merge your Task 1 `.bib` entries with `references.bib`, and check every `\citep{}` resolves (no bold `[?]` in the PDF).
5. Recompile and read the PDF once, start to finish.

---

## Step G: Process log and appendix (30 min)

1. Fill the four "fill in" cells in `PROCESS_LOG.md` with your own assessment of each AI output.
2. Add the issues you hit: the GitHub push and token problem, the rebase conflict, fairness measured on training data, the blank PNG, and any error of your own.
3. Take screenshots now (this chat, the fairness output, the git history) into `task2/screenshots/`.
4. In the report appendix: the GitHub link, the key output tables, issues and fixes, and a summary of the AI log with 2–4 screenshots.

---

## Step H: Declaration and AI attribution (30 min)

1. **Attribution:** aiattribution.github.io → answer the questions → paste the generated statement into the Generative AI Attribution section before the references.
2. **Declaration:** copy `Downloads/condition3_declaration_arya_khamkar_s4152566-2.docx`, rename it for Task 2, and update it to cover **all three parts**: each tool, its purpose, what it produced, what you accepted, rejected or revised, and how it influenced your submission. Export to PDF.

Missing declaration = the whole assessment is incomplete, so do not skip this.

---

## Step I: Final checks and submit (20 min)

- [ ] Search the Overleaf source for "TODO" — zero results.
- [ ] Part 1 is 750–1000 words, Part 3 about 400.
- [ ] Every figure and table has a caption, a number and a mention in the text.
- [ ] References all resolve; 5–10 extra citations in Part 1.
- [ ] AI attribution statement present, before the references.
- [ ] Appendix has the GitHub link and the process summary.
- [ ] Notebook committed and pushed; the repo link works.
- [ ] Report PDF named `task2_arya_khamkar_s4152566.pdf`.
- [ ] Submit **both** the report and the declaration PDF.

---

## If you run out of time

Protect, in this order:

1. **Declaration** (without it the assessment is incomplete).
2. **Part 1** (6 marks, and it's the largest block still missing).
3. **Part 2 write-up** (7 marks, but the analysis is already done, so it's mostly typing).
4. **Part 3** (4 marks, but it's only an hour, so don't drop it unless you have no video).
5. Polish, extra citations, nested CV, ThresholdOptimizer.

A thin section still earns marks. A missing section earns none.
