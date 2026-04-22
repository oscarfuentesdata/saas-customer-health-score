# Project Roadmap

> Phase-by-phase execution plan for the Customer Health Score project.
> Progress is tracked in the main `README.md`.

---

## Phase 0 — Setup ✅

- Repository initialized on GitHub
- Python, Git, PyCharm configured
- Virtual environment created
- Folder structure scaffolded
- `.gitignore` and `README.md` written
- First commit pushed

---

## Phase 1 — Synthetic Data Generation

Goal: Generate 18 months of realistic B2B SaaS operational data for a simulated company (Acme Analytics Cloud), stored in SQLite as a clean relational model.

### Sub-steps

- **1.1 Business context design** ✅ — Five foundational decisions documented in `business-context.md`: company profile, segmentation, industry mix, pricing, temporal window.
- **1.2 Python environment setup** — Install required libraries (`pandas`, `numpy`, `Faker`, `scipy`, `sqlalchemy`), create `requirements.txt`, scaffold data generation scripts under `src/data_generation/`.
- **1.3 Generate `accounts` table** — The foundational entity. 800 accounts with segment, industry, plan, contract dates, MRR.
- **1.4 Generate `users` and `usage_events` tables** — User population per account + daily product usage events. The largest table (~5–10M rows).
- **1.5 Generate `support_tickets` table** — Ticket volume and severity correlated with account health.
- **1.6 Generate `nps_surveys` table** — Quarterly NPS surveys with realistic response distribution.
- **1.7 Generate `csm_interactions` table** — CSM touchpoints, calibrated by segment and account health.
- **1.8 Inject churn signal** — Apply calibrated churn probabilities based on feature interactions. Create `churned` outcome flag.
- **1.9 Sanity checks** — Validate distributions, correlations, and aggregates against SaaS benchmarks. Catch trivial leakage.
- **1.10 Export to SQLite** — Load all tables into `acme.db`. Commit.

**Deliverable:** A populated SQLite database + reproducible generation scripts + sanity-check notebook.

---

## Phase 2 — SQL Modeling & Exploratory Data Analysis

Goal: Treat the database like a real data warehouse. Build feature engineering queries in SQL, then explore the data systematically.

### Sub-steps

- **2.1 Schema review and indexing** — Verify table relationships, add indexes for performance.
- **2.2 Feature engineering queries** — Write SQL to derive candidate features: engagement metrics, ticket trends, NPS trajectories, plan changes, tenure.
- **2.3 Exploratory Data Analysis notebook** — Distributions, correlations, churn rate by segment/industry, time-series patterns.
- **2.4 Hypothesis generation** — Document which features look predictive and why, before modeling.

**Deliverable:** A library of reusable SQL views + an EDA notebook that answers the six business questions from the brief.

---

## Phase 3 — Feature Engineering for ML

Goal: Transform exploratory features into a modeling-ready feature store with strict temporal discipline.

### Sub-steps

- **3.1 Point-in-time feature store** — For each account at each prediction date, assemble features using only data available before that date. No future leakage.
- **3.2 Temporal train/validation/test split** — Implement the split defined in `business-context.md`.
- **3.3 Outcome labeling** — For each prediction point, assign `churned_in_90d` label based on actual outcome in the hold-out window.
- **3.4 Class imbalance diagnosis** — Measure imbalance. Decide between class weights, threshold tuning, or resampling.
- **3.5 Feature scaling and encoding** — Standardize numerical features, encode categoricals.

**Deliverable:** Three datasets (train, val, test) ready for modeling, with a feature dictionary documenting each variable.

---

## Phase 4 — Modeling

Goal: Train an interpretable churn classifier, validate rigorously, and extract actionable insights.

### Sub-steps

- **4.1 Baseline model** — Naive "predict no churn for everyone." Establish the floor.
- **4.2 Logistic regression with L2 regularization** — Primary model. Emphasize interpretability.
- **4.3 Cross-validation and hyperparameter tuning** — On training + validation sets only.
- **4.4 Test set evaluation** — Out-of-time performance. Precision, recall, AUC, calibration.
- **4.5 Coefficient analysis** — Translate model coefficients into plain business language.
- **4.6 Segmented model comparison** — Compare unified model vs. per-segment models. Decide.
- **4.7 Decision threshold analysis** — Business-driven threshold selection, not default 0.5.

**Deliverable:** A trained, calibrated model with interpretability artifacts and a validation report.

---

## Phase 5 — Dashboard

Goal: Deliver a Power BI dashboard that operationalizes model output for CSMs.

### Sub-steps

- **5.1 Dashboard design** — Three views: portfolio health, account drill-down, driver analysis.
- **5.2 Data pipeline to Power BI** — Connect Power BI to model output (scored accounts).
- **5.3 Build visuals** — Applying `Storytelling with Data` principles. No chartjunk.
- **5.4 Iteration and polish** — Test readability, fix labels, verify numbers.

**Deliverable:** A `.pbix` file + high-quality screenshots for the repo.

---

## Phase 6 — Presentation

Goal: Package the project for public consumption. This is the phase that determines whether the project gets attention or gets scrolled past.

### Sub-steps

- **6.1 Rewrite `README.md` as a narrative** — Target audience: recruiters, hiring managers.
- **6.2 Methodology write-up** — `docs/methodology.md` with cross-phase technical decisions.
- **6.3 Findings and recommendations** — Close out Section 8 of `project-brief.md`.
- **6.4 LinkedIn post draft** — Optimized for a 3-second scroll decision.
- **6.5 Optional: 3-minute video walkthrough** — Recorded explanation of key decisions.
- **6.6 Repo hygiene** — License, final cleanup, verification that a stranger can reproduce the project from README instructions.

**Deliverable:** A portfolio-ready repository and public-facing assets.

---

*Progress and checkpoints tracked in `README.md`.*