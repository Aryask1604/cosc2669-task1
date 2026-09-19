# Task 2 — saved drafts and status

Saved Saturday 19 Sep, ~2:45am Melbourne. All text below is Arya's own drafting, stored verbatim.

**Deadline note:** the assignment was due Fri 18 Sep 16:59. Submitting before **Sat 19 Sep 16:59** keeps the late penalty at one day (10% = 2 marks). That is about 14 hours from when this was saved.

---

## STATUS

### Done

| Item | State |
|---|---|
| Repo, folder structure, process log template | Done, pushed |
| Overleaf project set up with course template, title, author, skeleton sections | Done |
| Notebook: preprocessing matching Task 1 (81 features, sorted) | Done |
| Notebook 2(a): churn 10-fold StratifiedKFold CV, attendance 5-fold TimeSeriesSplit | Done |
| Notebook 2(b): learning curves for 4 models + summary CSVs + 4 real PNGs | Done |
| Notebook 2(c): Fairlearn on out-of-fold predictions, group sizes, churn rate by group, difference() | Done |
| Notebook runs top to bottom with no errors | Done (16 cells, exec 1–17) |
| Part 1 draft (all six subsections) | Drafted, needs fixes below |
| Part 2 (a) draft | Drafted, accurate |
| Part 2 (b) draft | Drafted, accurate after the SVR correction |
| Part 2 (c) draft | Drafted, accurate; 4 small additions pending |

### Left to do

| Item | Est. | Notes |
|---|---|---|
| **Part 2 (d)** security / privacy / ethics | 45 min | Not started. Outline saved below |
| **Part 3** peer review | 60 min | Blocked on Canvas: check "Assigned Peer Reviews". If none, submit the request form and write one sentence saying so |
| Part 1 fixes | 30 min | Cite the paper in-text; get to 5–10 extra citations; cut ~230 words; fix .bib author names |
| Part 2 (c) additions | 10 min | Name the models; attendance group sizes; show the 145 arithmetic; era clause |
| Assemble in Overleaf | 45 min | Paste sections, upload 4 PNGs, build the CV table, check all \citep resolve |
| Process log + appendix | 30 min | 4 "fill in" cells, issues table, screenshots |
| **AI attribution statement** | 10 min | aiattribution.github.io |
| **Condition 3 declaration** | 20 min | Template: Downloads/condition3_declaration_arya_khamkar_s4152566-2.docx. Missing declaration = assessment incomplete |
| Final checks + submit | 20 min | Search "TODO", word counts, both PDFs |

### Triage if time runs short

1. Declaration. 2. Part 1. 3. Part 2 write-up. 4. Part 3. 5. Polish.

---

## PENDING FIXES BY SECTION

**Part 1**

- The paper is never cited in-text — add `\citep{trippas2025adapting}` where claims are the paper's.
- Only 4 extra sources; the brief requires 5–10 (peer-reviewed, per Task 1 feedback).
- ~1,230 words against the 750–1000 limit; cut ~230, mostly repetition in the case study and challenges.
- `.bib` author names must read: Johanne R. Trippas, Damiano Spina, Falk Scholer.

**Part 2 (b)**

- Figure filenames are `lc_attendance_xgboost.png`, `lc_attendance_svr.png`, `lc_churn_xgboost.png`, `lc_churn_svm.png`.
- `[H]` needs `\usepackage{float}`; use `[htbp]` instead.
- Say "three limitations" (the draft says two, then lists three).
- Add to each caption: "each line shows one cross-validation fold".

**Part 2 (c)**

- Name the models audited: XGBoost classifier (churn), XGBoost 5k regressor (attendance).
- Add attendance group sizes: 9,264 / 5,679 / 4,765 / 5,747 by `H Pop` quartile; ~6,200–6,500 per attendance band.
- Show the arithmetic: "about 145 of the roughly 347 churners in that group".
- Optional: era clause — MAE 1,625 pre-2005 (n=8,745) vs 1,261 from 2005 (n=16,710), which links to the drift noted in (a).

---

## KEY NUMBERS (from the notebook, verified)

**2(a) cross-validation**

| Dataset | Model | Task 1 | CV mean ± SD |
|---|---|---|---|
| Attendance | SVR | MAE 1,247 | 1,654.8 ± 326.8 |
| Attendance | XGBoost (5k) | MAE 1,056 | 1,386.0 ± 282.2 |
| Churn | SVC | Recall 0.557 | 0.518 ± 0.065 |
| Churn | XGBoost | Recall 0.680 | 0.650 ± 0.087 |

Attendance XGBoost per fold: 1,580 → 1,700 → 1,445 → 1,200 → 1,006.

**2(b) learning curves**

Attendance XGBoost: train RMSE 62 (509 games) → 1,221 (25,455); validation 2,675 → 1,305; at 5,091 games validation is 2,194. Attendance SVR: train 2,750 → 1,870, test 3,400 → 1,600, curves converge (underfitting). Churn XGBoost: train F1 1.00 (300) → 0.90 (3,000); validation 0.55 → 0.74. Churn SVM: train ~0.60–0.69 → 0.72; test ~0.25–0.44 → 0.54–0.70, still rising.

**2(c) fairness — churn (out-of-fold, all 3,333)**

| Group | n | Churn rate | Recall | Selection rate |
|---|---|---|---|---|
| No data plan | 2,411 | 16.7% | 0.677 | 0.135 |
| Data plan | 922 | 8.7% | 0.512 | 0.051 |
| 0–3 service calls | 3,066 | 11.3% | 0.580 | 0.079 |
| 4+ service calls | 267 | 51.7% | 0.826 | 0.487 |
| Tenure quartiles | 803–857 | 13.2–15.7% | 0.576–0.690 | 0.102–0.126 |

**2(c) fairness — attendance (25,455 held-out rows)**

| Grouping | Group (n) | MAE | Mean signed error |
|---|---|---|---|
| `H Pop` quartile | Q1 (9,264) | 1,764 | −279 |
| | Q2 (5,679) | 1,359 | −464 |
| | Q3 (4,765) | 1,239 | −380 |
| | Q4 (5,747) | 926 | −506 |
| Era | pre-2005 (8,745) | 1,625 | −354 |
| | 2005+ (16,710) | 1,261 | −409 |
| Attendance band | lowest (6,364) | 1,893 | +1,328 |
| | 2nd (6,432) | 1,164 | −642 |
| | 3rd (6,469) | 1,119 | −950 |
| | highest (6,190) | 1,375 | −1,310 |

---

## OUTLINE STILL TO WRITE — Part 2 (d)

**Security (~100 words):** membership inference against the churn model; data poisoning of retraining data; model extraction through a prediction API; mitigations — authentication, access control, logging, monitoring for distribution shift.

**Privacy (~100 words):** Privacy Act 1988 and APP 3 (collection), APP 6 (use and disclosure), APP 7 (direct marketing), APP 11 (security). The Kaggle churn data is semi-synthetic with no direct identifiers; real NBL membership data would be personal information. Data minimisation, restricted access, retention periods.

**Ethics (~100 words):** targeted retention offers; who is excluded when recall differs by group (0.512 vs 0.677); treating scores as decision support not verdicts; human review for consequential cases; monitoring over time.

Add 2–3 citations across this section — it currently has none, and Task 1 feedback was specifically about thin citation.

---

# DRAFTS (verbatim)

## Part 1: Executive Summary

### Purpose and Audience

The position paper by Trippas, Spina and Scholer discusses how generative information retrieval (GenIR) systems should be designed around users, tasks and scenarios rather than treating retrieval as only a process of finding documents. The authors argue that generative systems change how people interact with information, but many of the ideas from traditional information retrieval and interactive information retrieval are still important. The paper is a position paper rather than an experimental study, so its main purpose is to bring together existing ideas and identify areas that need more research. The main audience is researchers and practitioners working on information retrieval and generative AI. The paper is useful for this audience because it shows why the same GenIR system may behave differently depending on who is using it, what they are trying to do and the situation in which it is being used.

### Main Argument and Contributions

The main argument is that GenIR should not be evaluated only by looking at whether the generated answer is relevant. The system also needs to consider the user, the task being performed and the scenario in which the interaction happens. This is important because a generated answer can look correct but still be unsuitable for the task or the particular user. The authors therefore connect GenIR with ideas from information seeking and interactive information retrieval, where the user's goal and interaction with the system are already important considerations.

One contribution of the paper is the use of users, tasks and scenarios as a way of thinking about GenIR systems. This gives researchers a broader way to describe what happens during a search interaction rather than focusing only on the query and retrieved results. Previous research has also shown that search tasks can differ in complexity and purpose, which affects how people search and interact with information [2,3]. Another contribution is the discussion of how GenIR changes the interaction itself. Instead of simply returning a list of results, a system can generate an answer, ask for clarification, reformulate the task or continue the interaction. This makes the evaluation of these systems more complicated because the quality of the interaction can matter as well as the quality of the final answer [4,5]. The paper also points to areas where more research is needed, including evaluation, user modelling, task understanding and the role of people in checking generated information.

### Case Study

A useful case outside the scenarios discussed in Section 5 of the paper is **online product comparison**. For example, a customer may want to compare several laptops before buying one. The person may care about different things such as price, battery life, storage, performance or whether the laptop is suitable for university work. Instead of giving the customer a normal list of product pages, a GenIR system could summarise information from different products and help the customer compare them.

This case fits the users, tasks and scenarios framing because the system is supporting a decision rather than simply answering a factual question. The user has a specific goal, but the goal can change as they learn more about the products. For example, a customer who originally searches for the cheapest laptop may later decide that battery life is more important after seeing the available options. The system therefore needs to understand the task and respond to changes in the user's information needs.

The case is also different from the five scenarios in Section 5 of the paper because it is focused on **consumer product comparison and purchasing decisions**, rather than work, knowledge-base access, learning and teaching, research, or personal information management. Research on conversational and interactive information seeking suggests that these types of interactions can involve changing information needs and multiple steps rather than one simple query [5]. This makes product comparison a useful example for considering how a GenIR system could support users during a decision-making process.

### Challenges

One challenge is understanding the user's actual task. A short query may not contain enough information to determine what the person is really trying to achieve. In the laptop example, a search for "best laptop for university" does not explain the user's budget, course requirements or preferred features. Search tasks can also vary in complexity, which makes it difficult to treat all queries in the same way [2,4].

A second challenge is query reformulation and interaction. Users may change their requirements after seeing generated information. The system therefore needs to support an ongoing interaction rather than assuming that the first query completely describes the task. Another issue is evaluation. Traditional retrieval measures do not necessarily capture whether a generated comparison actually helped the user make a decision. A response could contain relevant information but still leave out an important product difference.

There is also a risk that generated information is incomplete or incorrect. This is particularly important in product comparison because users may rely on the generated summary when making a purchase. The system should therefore make it clear where important information comes from and allow users to check the original product information.

### Opportunities

The main opportunity is to make GenIR systems more aware of the task being performed. In the product comparison example, the system could ask a small number of useful questions when the user's requirements are unclear and then adjust the results based on the answers. This could make the interaction more useful than returning the same type of response for every user.

Another opportunity is better support for transparency and user checking. Generated comparisons could show the information used to support important claims so that users can verify them. This is especially useful when the system is helping with a decision rather than just providing general information. However, these features also need to be designed carefully so that they do not make the interaction unnecessarily complicated.

### Conclusion

The paper's users, tasks and scenarios framing is useful for understanding why GenIR systems cannot be designed only around generating relevant answers. The product comparison example shows how the user's goal, changing requirements and decision context can affect what a useful system should do. It also shows that better generation alone does not solve the problems of task understanding, evaluation and trust.

---

## Part 2: Deliberation on Task 1

### (a) Cross-Validation and Sampling

I used cross-validation to check whether the results from Task 1 were reasonable across different splits of the data. The attendance and churn datasets required different approaches because they were different types of prediction problems.

For the attendance data, I used **5-fold TimeSeriesSplit**, rather than a standard random cross-validation split. This was used because the data has a time order, so randomly mixing earlier and later observations would not represent how the model would be used in practice. The cross-validation was run using all **30,546 attendance records**. The XGBoost model used in this CV was the same 5,000-row version used for the Task 1 comparison, rather than the full 24,239-row XGBoost model.

| Dataset    | Model        |  Task 1 result | Cross-validation result |
| ---------- | ------------ | -------------: | ----------------------: |
| Attendance | SVR          |    MAE = 1,247 |   MAE = 1,654.8 ± 326.8 |
| Attendance | XGBoost (5k) |    MAE = 1,056 |   MAE = 1,386.0 ± 282.2 |
| Churn      | SVC          | Recall = 0.557 |  Recall = 0.518 ± 0.065 |
| Churn      | XGBoost      | Recall = 0.680 |  Recall = 0.650 ± 0.087 |

The attendance results show that the cross-validation errors were higher than the Task 1 test errors. This means that the Task 1 test period was easier for the models than the average split seen during cross-validation. The comparison is more appropriate when using the XGB-5k result of 1,056, because this is the same model size as the model used in the cross-validation. The full-data XGBoost result of 958 is therefore not directly compared with the 1,386 CV result.

For churn, the Task 1 recall results are reasonably close to the cross-validation results. The XGBoost recall of 0.680 and SVC recall of 0.557 from Task 1 are both within about one standard deviation of their respective CV means. This suggests that the Task 1 split was not particularly unusual for the churn models.

The sampling approach for churn was also important because only about 14.5% of customers were in the churn class. I used stratified sampling so that the class proportions were maintained across the splits. This makes the comparison more useful than using a random split that could produce noticeably different class proportions.

### (b) Learning Curves

The attendance XGBoost learning curve shows training RMSE increasing from 62 at 509 training games to 1,221 at 25,455 games, while validation RMSE falls from 2,675 to 1,305. The gap therefore decreases from about 2,613 to 84. This shows strong overfitting with the smaller training sets, while the model generalises better as more games are included. Validation RMSE is still falling at the largest training size, so more data could still improve the model. The SVR curve shows the same general pattern, although the gap between training and validation performance remains larger.

> FIX PENDING: the SVR sentence above is wrong. The figure shows training RMSE falling from ~2,750 to ~1,870 and test RMSE falling from ~3,400 to ~1,600, so the curves converge and test ends slightly below train. The high training error indicates underfitting rather than overfitting (epsilon=100 ignores errors within ±100 attendees), so SVR's limitation is model capacity, not data quantity.

For churn, XGBoost training F1 decreases from 1.00 at about 300 customers to 0.90 at about 3,000, while validation F1 increases from about 0.55 to 0.74. The gap becomes smaller, from about 0.45 to 0.16, but does not disappear, so some overfitting remains at the largest training size. The SVM curve also shows improving validation performance as more customers are used, with a remaining gap between training and validation F1. [Could add: the SVM gap narrows to under 0.10 and its validation F1 is still rising at 3,000 customers, so more data would likely help SVM more than XGBoost.]

There are two limitations to these curves. For attendance, each training size comes from a different fold, so the larger training sizes are associated with later folds. The final fold also had an easier test period than some earlier folds, so part of the improvement may be due to the period being tested rather than training size alone. Because each subset uses \texttt{tr[:size]}, smaller training sets are the oldest games, so training size is partly confounded with recency. Also, each training size is represented by only one fold, so a standard deviation cannot be reported for the attendance learning curve.

The curves also help explain the Task 1 difference between XGB-5k (MAE 1,056) and XGB-full (MAE 958). At around 5,000 games, the validation RMSE is 2,194, compared with 1,305 at 25,455 games, suggesting that training size contributes to the difference between the two XGBoost results, although training size and test period are not fully separable in this design.

### (c) Bias and Fairness

I used Fairlearn to check whether model performance differed across groups. I generated out-of-fold predictions for all 3,333 churn customers and the 25,455 held-out attendance rows, then used \texttt{MetricFrame} to compare accuracy, precision, recall, F1 and selection rate, together with \texttt{difference()}. Fairlearn was useful because it provides the same metrics for each group without requiring separate calculations. The datasets do not contain protected attributes, so the groups used here are proxy groups based on operational variables.

For churn, customers with a data plan had recall of 0.512, compared with 0.677 for customers without a plan. Their selection rates were 0.051 and 0.135 respectively. The underlying churn rates were also different: 8.7% for plan holders and 16.7% for customers without a plan, with group sizes of 922 and 2,411. This difference in base rates explains much of the selection-rate gap, since the groups have different proportions of actual churners. However, it does not explain the recall gap because recall is calculated only among customers who actually churned. The model therefore missed a larger proportion of actual churners among plan holders.

For \texttt{CustServCalls}, recall was 0.826 for customers with four or more calls, compared with 0.580 for customers with zero to three calls. The four-or-more group contained only 267 customers and had a churn rate of 51.7%, so this result is less stable than the larger group. The more important operational issue is the low-call group, where the lower recall means the model misses around 145 churners. Tenure showed only relatively small differences, with recall ranging from 0.576 to 0.690 across the tenure quartiles, so there was no large recall difference by tenure.

For attendance, MAE varied from 1,764 in the lowest \texttt{H Pop} quartile to 926 in the highest quartile. I cannot verify the exact meaning of \texttt{H Pop} from the dataset documentation, so I describe these only as quartiles of the variable rather than interpreting them as different market sizes. Looking at attendance bands also showed signed error changing from +1,328 in the lowest attendance band to -1,310 in the highest. This indicates that predictions were pulled towards the middle, although this pattern is partly expected because the groups were created using the target variable itself.

For the NBL, these differences could affect decisions made from the models. Missed churners may not receive a retention offer, while systematic attendance errors could affect where promotional effort is allocated. However, these results should not be treated as evidence of discrimination because the groups are based on operational variables rather than protected attributes. They instead show where model performance should be monitored before using the predictions in real decisions.

### (d) Security, Privacy and Ethical Risks

NOT YET WRITTEN — see the outline above.

---

## Part 3: Peer Review

NOT STARTED — blocked on the Canvas peer video.
