# Twitch Channel Growth — Regression Analysis

A regression analysis of 1,000 top Twitch channels to understand and predict channel
growth from streaming activity and audience engagement metrics.

## Project Files

| File | Description |
|---|---|
| `Twitch_user_data.csv` | Raw input dataset (1,000 channels x 11 columns) |
| `1_Data_Cleaning_EDA.ipynb` | Data cleaning and exploratory data analysis |
| `2_Feature_Engineering_Regression.ipynb` | Feature engineering, model training, and overfitting diagnostics |
| `Twitch_Growth_Findings_Report.pdf` | Written summary of findings with visualizations |
| `twitch_cleaned.csv` | Cleaned dataset exported by Notebook 1, used as input to Notebook 2 |
| `model_comparison_summary.csv`, `results_setA.csv`, `results_setB.csv`, `feature_importances.csv` | Model results exported by Notebook 2, used to build the PDF report |

## How to Use This Project

1. Open **`1_Data_Cleaning_EDA.ipynb`** first. It loads `Twitch_user_data.csv`, cleans and
   inspects the data, runs EDA, and exports `twitch_cleaned.csv`.
2. Open **`2_Feature_Engineering_Regression.ipynb`** next. It loads `twitch_cleaned.csv`,
   engineers features, trains and compares 5 regression models, and checks for overfitting.
3. Read **`Twitch_Growth_Findings_Report.pdf`** for a narrative summary of the findings,
   written for a non-technical audience, with the key charts included.

Both notebooks were run end-to-end and already contain their outputs (tables, printed
stats, and charts), so you can read them without re-running any code. To re-run them
yourself, execute cells top to bottom in order — Notebook 2 depends on the cleaned CSV
produced by Notebook 1.

### Requirements to re-run the notebooks
- Python 3
- `pandas`, `numpy`, `matplotlib`, `seaborn`, `scikit-learn`

## Project Summary

**Goal:** Predict Twitch channel growth, defined as **`Followers gained`** — the number of
new followers a channel gained over the observation period — from streaming activity and
audience metrics.

**Approach:**
1. **Data cleaning** — verified no missing values or duplicates; identified and kept 3
   channels with legitimate negative growth (net follower loss / churn) rather than
   treating them as errors.
2. **EDA** — found that all numeric features are heavily right-skewed (a "power law" pattern
   typical of platform data) and log transforms substantially clean up the relationships.
   Current follower count, watch time, and viewer counts showed the strongest correlations
   with growth.
3. **Feature engineering** — built log-transformed predictors and engagement ratios
   (average-to-peak viewer ratio, watch-time-per-stream-minute, views-per-follower), encoded
   categorical variables, and used a signed-log growth target to handle both extreme skew
   and the rare negative-growth cases.
4. **Leakage check** — trained models both **with** and **without** current follower count,
   since it's highly predictive but not an actionable growth lever (a channel's current size
   already reflects the followers it just gained).
5. **Modeling** — compared Linear Regression, Ridge, Lasso, Random Forest, and Gradient
   Boosting using an 80/20 train-test split and 5-fold cross-validation.
6. **Overfitting checks** — compared train vs. test R², cross-validation scores, and
   learning curves for each model.

**Key findings:**
- The best-generalizing model (Linear Regression, activity-only features) achieved a test
  R² of **~0.13** — channel growth has real, meaningful limits to how well it can be
  predicted from activity metrics alone, since factors like content quality, virality, and
  external promotion aren't captured in this dataset.
- Tree-based ensembles (Random Forest, Gradient Boosting) fit the training data far better
  (R² up to 0.84) but **overfit badly** — train/test gaps of 0.52–0.77 — while regularized
  linear models stayed far more consistent (~0.29–0.30 gap) despite the smaller dataset
  size (~1,000 rows).
- The strongest activity-based drivers of growth are **watch time**, **stream time**, and
  **audience consistency** (average-to-peak viewer ratio) — steady engagement matters more
  than one-off viewership spikes.

See `Twitch_Growth_Findings_Report.pdf` for the full write-up, including all
visualizations, model comparison tables, and recommendations.
