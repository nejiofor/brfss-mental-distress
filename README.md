# BRFSS 2024 Frequent Mental Distress

## KA1 Milestone 1: Research Design and Feasibility

**Author:** Ngozichukwuka Ejiofor  
**Course:** ANLY 530 — Principles and Applications of Machine Learning

## Project overview

This project examines whether demographic, socioeconomic, behavioral, and physical-health information can identify adults reporting frequent mental distress in the 2024 Behavioral Risk Factor Surveillance System (BRFSS).

The research protocol proposes comparing regularized logistic regression with a decision tree that has limits on its complexity. Later analyses will examine performance in states excluded from training, the reliability of predicted probabilities, and differences across recorded sex and age groups.

## What is included at Milestone 1?

The current notebook, `BRFSS_KA1_Initial_Exploration.ipynb`, stops at exploration and feasibility. It:

- Loads the local BRFSS file and records its dimensions, software versions, and digital fingerprint.
- Checks that the required measurements are available.
- Defines frequent mental distress and handles invalid or missing responses.
- Reports respondent exclusions and predictor missingness.
- Summarizes the outcome using both unweighted counts and survey-weighted prevalence.
- Produces descriptive tables and plots.
- Checks for outcome-related information that must be excluded from predictors.
- Checks whether five proposed state-held-out validation folds are feasible.
- Saves aggregate exploratory summaries.

**The initial notebook does not fit, tune, or compare predictive models.** It does not produce model-performance scores, calibration results, optimized thresholds, or subgroup model comparisons. Those analyses are planned for later milestones. Any earlier modeling results discussed in the protocol are separate preliminary work, not outputs of this notebook.

## Dataset and outcome

**Source:** Centers for Disease Control and Prevention, [2024 BRFSS survey data and documentation](https://www.cdc.gov/brfss/annual_data/annual_2024.html).

BRFSS is a telephone survey of noninstitutionalized adults. Each record represents one respondent. These are cross-sectional observations, not repeated measurements of the same person over time.

The outcome is frequent mental distress:

- **Frequent distress:** 14–30 mentally unhealthy days in the previous month.
- **Comparison group:** 0–13 mentally unhealthy days.

The code distinguishes responses indicating zero days from unknown, refused, invalid, or missing responses. Missing outcomes are excluded rather than imputed. Counts and weighted prevalence are calculated from the actual local file when the notebook runs.

The eleven predictors are age, body mass index, physically unhealthy days, recorded sex, education, household income, employment status, routine-checkup history, leisure-time physical activity, smoking status, and walking difficulty. Health-plan coverage and sleep duration are excluded because they were unavailable in the working file. Survey weights and sampling identifiers are not predictors.

## Files for the initial submission

| File | Purpose |
|---|---|
| `README.md` | Project overview and run instructions |
| `BRFSS_KA1_Initial_Exploration.ipynb` | Self-contained exploratory notebook; it may be stored in a `notebooks/` folder |
| `requirements.txt` | Software dependencies |
| `CONTRIBUTIONS.md` | Individual responsibilities and AI assistance |
| Research protocol in DOCX or PDF format | Research question, literature synthesis, measurement, proposed methods, schedule, and limitations |
| `.gitignore` | Helps exclude local data and temporary outputs |


## How to run

1. Download and extract `LLCP2024.XPT` from the CDC source above. Keep the matching codebook for checking response definitions.
2. Use Python 3.12 with the accompanying requirements file. From the repository folder on macOS or Linux, run:

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
jupyter lab
```

3. Open `BRFSS_KA1_Initial_Exploration.ipynb`, either from the repository root or the `notebooks/` folder.
4. Put the XPT file beside the notebook, put it in the repository's `data/` folder, or edit `DATA_PATH` in the settings cell to point to its actual location. Alternatively, set the `BRFSS_PATH` environment variable before starting Jupyter.
5. Restart the kernel and run all cells in order.
6. Review the actual results, add any needed interpretation, and save the executed notebook before submitting it.


## Outputs and reproducibility

The notebook saves aggregate CSV tables and a JSON manifest in `ka1_aggregate_outputs/`, relative to the notebook's working directory. These include cohort flow, missingness, target distribution, numeric and categorical summaries, and proposed validation-fold checks.

The manifest records the input fingerprint, software versions, seed 42, selected predictors, and analysis scope. The proposed folds keep entire states separate. Checking the folds does not constitute fitting or validating a model.

Unweighted percentages describe respondents in the analytic sample; survey-weighted percentages estimate prevalence for the population represented by the included data. They should not be used interchangeably.

## Planned later work

Subsequent milestones will compare a constant baseline, logistic regression, and a constrained decision tree. The plan includes training-fold preprocessing, nested state-grouped selection, survey-weighted average precision and other metrics, calibration, training-selected thresholds, subgroup evaluation, and sensitivity checks. Survey-design uncertainty requires separate attention to the documented sampling structure.


## Interpretation limits

Frequent mental distress is a self-reported surveillance measure, not a clinical diagnosis. This cross-sectional study does not predict future illness, identify causes of distress, or establish that a model is suitable for individual screening. Nonresponse, missing data, survey coverage, measurement error, and geographic differences limit interpretation.

## Contribution and AI assistance

This is an individual project. Ngozichukwuka Ejiofor is responsible for reviewing the literature and study design, checking the data coding, executing the analysis, interpreting results, maintaining the repository, and preparing the submission.

ChatGPT assisted with GitHub instructions. 
