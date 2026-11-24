# BRFSS 2024 Frequent Mental Distress — KA1 Milestone 2
Author: Ngozichukwuka Ejiofor

Research question: Can eleven respondent characteristics classify concurrent frequent mental distress, and does a constrained decision tree improve on L2 logistic regression when entire states are held out?

Project repository: https://github.com/nejiofor/brfss-mental-distress


## Status
The real-data analysis is complete in the reviewed notebook. It includes cohort preparation, the nested state-held-out baseline/logistic/tree comparison, calibration, geographic and demographic subgroup results, model complexity, training-state refit stability, and both prespecified sensitivity analyses. The report has been updated with these findings.

Survey-design standard errors and confidence intervals were withheld because the audit found 110 single-sampling-unit strata and no handling method had been specified. Full uncertainty from model fitting, tuning, and threshold selection has not been estimated. Results are descriptive; no statistical-significance or clinical-readiness claim is made.

### Key real-data results
- Input: 457,670 respondents and 301 variables; eligible cohort: 449,514, including 62,315 positive outcomes.
- Unweighted outcome prevalence: 13.86%; survey-weighted prevalence: 15.52%.

| Model | Mean outer-fold weighted AP | Pooled out-of-fold weighted AP | Pooled ROC-AUC | Pooled Brier score |
| --- | ---: | ---: | ---: | ---: |
| Constant training-prevalence baseline | 0.1558 | 0.1515 | 0.4896 | 0.1311 |
| Logistic regression | 0.4059 | 0.4041 | 0.7654 | 0.1124 |
| Decision tree | 0.3782 | 0.3833 | 0.7519 | 0.1138 |

Mean-fold and pooled metrics summarize predictions differently and should be labeled separately. The baseline predicts a constant probability within each fold, but those probabilities vary across folds; its pooled AP therefore need not equal pooled prevalence, and its pooled ROC-AUC need not equal 0.50.

Logistic regression had higher AP in 49 of 53 reporting areas and more stable classifications under training-state subsampling. At training-selected thresholds, pooled sensitivity was 69.96% for logistic regression and 70.03% for the tree; pooled precision was 29.53% and 28.36%, respectively. Performance varied across sex and age groups.

| Sensitivity analysis | Logistic regression pooled AP | Decision tree pooled AP |
| --- | ---: | ---: |
| Without physically unhealthy days | 0.3355 | 0.3071 |
| Without model-fitting weights | 0.4035 | 0.3856 |

Both sensitivity analyses were completed with survey-weighted evaluation. Removing physical-health days reduced performance materially; removing fitting weights had much smaller effects. Logistic regression remained the preferred model under both checks. These results do not establish causation or robustness to every analysis choice.

## Run on your Mac
1. Open the project folder. If using the ZIP package, extract it first and keep its folder structure. Rename the reviewed notebook to `BRFSS_KA1_Milestone2.ipynb` if your download has a numbered suffix.
2. Keep `LLCP2024.XPT` local. The notebook checks your previously supplied project/iCloud path, the repository root, and a `data` subfolder. An explicit `BRFSS_PATH` environment variable overrides those choices.
3. Use a Python 3.12.4 environment with the dependencies in `requirements.txt`. Do not replace an existing environment that you need for earlier work.
4. From the extracted folder, run:

```bash
python3.12 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
jupyter lab BRFSS_KA1_Milestone2.ipynb
```

Choose **Restart Kernel and Run All Cells**. Keep `DIAGNOSTIC_N=None` for full-cohort results. The reviewed notebook has `RUN_STABILITY=True` and `RUN_SENSITIVITIES=True`. A full run includes the primary comparison, refit stability checks, and both sensitivity analyses. The sensitivity analyses repeat nested tuning and increase runtime. After the analysis finishes, run the final reproducibility-record cell and save the notebook with its outputs.

Optional environment-variable path (Terminal syntax):

```bash
export BRFSS_PATH='/Users/honeydrops/Library/Mobile Documents/com~apple~CloudDocs/Documents/Harrisburg University/Classes/Fall 2026/ANLY 530/Project/brfss-mental-distress-ka1/LLCP2024.XPT'
```

The notebook also runs without `src/` because its analysis functions are embedded. If the accompanying script and test files are present, the command-line version can be run as follows. Their presence in the published repository has not been checked here:

```bash
python tests/test_milestone2.py
python src/milestone2.py --data '/your/local/LLCP2024.XPT' --sensitivities
```

The tests use synthetic records only. Full-data model fitting can take substantial time; progress is printed by fold. A diagnostic subsample checks execution but does not support full-survey population inference.

## Data and measurement
Source: CDC public-use BRFSS 2024, with matching codebook and survey guidance:
https://www.cdc.gov/brfss/annual_data/annual_2024.html

Frequent mental distress is 14–30 mentally unhealthy days versus 0–13. The zero-days response becomes zero; unknown, refused, invalid, and missing outcomes are excluded. Eleven predictors: age, BMI, physically unhealthy days, recorded sex, education, income, employment, routine-checkup history, leisure-time physical activity, smoking status, and walking difficulty. Routine-checkup “never” is a valid category. BMI is converted to ordinary units.

No mental-health outcome-derived fields, geographic fields, weights, strata, or sampling-unit identifiers enter the predictors. Health-plan coverage and sleep duration are not included in this eleven-predictor comparison.

## Evaluation
- Five identical outer state-held-out folds; three state-grouped inner folds; seed 42.
- Training-only median/mode imputation, numeric scaling, and categorical indicator encoding. No outcome imputation.
- Constant weighted training-prevalence baseline, logistic C values 0.1/1/10, tree depth 3/6 and minimum leaf size 50/200.
- Unweighted preprocessing; training survey weights normalized to mean one; survey-weighted evaluation.
- Inner mean weighted AP selects settings. The largest inner-OOF threshold reaching at least 70% sensitivity is frozen for outer evaluation. This is an educational operating point.
- AP with prevalence, ROC-AUC, Brier score, calibration, default and training-selected threshold performance, paired state results, age/sex subgroups, model complexity, and training–held-out gaps.
- Descriptive age/sex standardization across states. Not causal adjustment and not proof that composition explains differences.
- Five training-state subsampling refits with settings and evaluation population held fixed. These diagnose rule/prediction sensitivity, not survey uncertainty.
- Completed fully nested comparisons excluding physical-health days and omitting fitting weights; evaluation remains weighted. These can be skipped for a shorter future run, but execution status must then be reported accurately.

## Uncertainty: read this distinction
Survey-weighted performance is not automatically design-based population inference. The workflow audits state, sampling stratum, and sampling unit. A with-replacement Taylor ratio approximation aggregates within sampling units and calculates within-stratum variation, retaining zero-domain contributions from outcome-ineligible records.

Prevalence is an eligible-domain estimate. For Brier score, sensitivity, and specificity, the code treats out-of-fold predictions as fixed. Its intervals are **conditional descriptive approximations**, excluding training, tuning, and threshold-selection uncertainty. They do not justify a full learning-procedure confidence claim. AP/ROC-AUC/calibration intervals and complete survey-resampling/retraining uncertainty remain work for the final paper. In this run, the audit identified 2,393 strata and 457,670 sampling units, including 110 single-unit strata. The code returned point estimates but withheld standard errors and confidence intervals. Single-unit strata or missing eligible design identifiers trigger this safeguard; a documented handling approach is still needed. Diagnostic subsamples skip these intervals. Fold SDs and state-refit diagnostics are not design-correct confidence intervals.

## Aggregate outputs
The local `milestone2_outputs/` directory includes cohort/missingness audits; selected settings; inner complexity results; training–held-out gaps; fold and pooled metrics; paired state comparisons and population profiles; standardized state metrics; subgroup/calibration summaries; stability diagnostics; sampling-design audit; conditional uncertainty; two PNG figures; and a manifest with checksum, software versions, scope, and switches. Completed sensitivity outputs are written to `without_physical_health/` and `unweighted_fitting/` beneath this directory.

Weighted confusion totals are sums of survey weights, not respondent counts. Metrics describe the observed-outcome domain. Weights do not correct all nonresponse or measurement error.

## Files and submission
The following describes the intended project package. Keep only file listings that match the files actually uploaded to the repository.
- `BRFSS_KA1_Milestone2.ipynb`: self-contained analysis notebook.
- `src/milestone2.py`: reusable implementation and command-line execution.
- `tests/test_milestone2.py`: synthetic recoding, leakage, split, threshold, integration, and variance checks.
- `requirements.txt`: tested core dependencies plus Jupyter support.
- `.gitignore`: excludes raw data, respondent extracts, environments, and local outputs.
- `CONTRIBUTIONS.md`: individual responsibilities and AI assistance.
- `KA1_Milestone2_Report_Updated.docx`: report containing completed real-data findings, tables, interpretation, conclusion, and limitations.


## Limitations
- **Concurrent classification, not future prediction.** The predictors and outcome were collected in the same cross-sectional survey. The models classify reported frequent mental distress and do not establish diagnosis, causation, or future illness risk.
- **Self-reported measures.** Recall, interpretation of questions, and reporting differences may affect both predictors and the outcome. Frequent mental distress is not equivalent to a clinical mental-health diagnosis.
- **Outcome nonresponse.** Respondents with missing or invalid outcomes were excluded. Findings apply to the observed-outcome domain; survey weights do not automatically remove bias from outcome nonresponse.
- **Missing predictors and simple imputation.** Among eligible respondents, income missingness was 18.72%, body mass index missingness was 9.24%, and smoking-status missingness was 6.91%. Training-only median and mode imputation prevents test-data leakage but does not resolve possible bias from informative missingness or fully represent imputation uncertainty.
- **Dependence on a concurrent physical-health measure.** Physically unhealthy days shares the outcome’s 30-day recall period. Excluding it reduced pooled AP from 0.4041 to 0.3355 for logistic regression and from 0.3833 to 0.3071 for the tree. Logistic regression remained preferred, but absolute performance depended partly on that predictor.
- **Limited geographic and temporal scope.** Holding out states and territories tests transfer across the 53 reporting areas in this file. It does not establish performance in future years, other populations, or clinical settings. Differences in prevalence, population composition, and survey measurement can affect geographic comparisons.
- **Unequal subgroup performance.** Overall sensitivity near 70% did not imply similar sensitivity or specificity across sex and age groups. AP and precision depend on prevalence, and AP/prevalence ratios do not fully correct for population differences. These comparisons alone cannot establish fairness.
- **Exploratory population standardization.** Age/sex reweighting is descriptive, requires adequate cell counts, and does not account for all measured or unmeasured differences between reporting areas. It is not causal adjustment.
- **No completed survey-design confidence intervals.** The audit found 110 single-sampling-unit strata. Standard errors and confidence intervals were withheld because a handling method had not been specified. This is an implementation limitation, not evidence that BRFSS uncertainty cannot be estimated. Observed model differences are not claimed to be statistically significant.
- **Learning-procedure uncertainty remains unestimated.** Even the proposed conditional intervals would treat out-of-fold predictions as fixed and omit fitting, tuning, and threshold-selection uncertainty. Fold standard deviations and the 80% training-state refits are descriptive checks, not replacements for appropriate survey-resampling uncertainty.
- **Restricted model search and stability checks.** Conclusions concern the tested regularized logistic regression and constrained trees, not all possible algorithms or settings. Refit checks held hyperparameters and thresholds fixed and did not repeat model selection. Small positive-case counts in some areas can also make local results unstable.
- **Threshold trade-offs and practical use.** The 70% training-sensitivity target was chosen for research comparison, not validated as a screening standard. At training-selected thresholds, pooled logistic-regression precision was 29.53%, so many flagged respondents did not report the outcome. No clinical benefit, deployment readiness, or acceptable balance of false positives and missed cases has been established.

## Interpretation
This is cross-sectional classification of self-reported distress, not diagnosis, future illness prediction, causal inference, or screening deployment. A tree's higher recall in selected states is insufficient for selection without precision, calibration, paired performance, and stability evidence.


