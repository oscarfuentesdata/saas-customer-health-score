# Customer Health Score & Churn Driver Analysis

**A predictive analytics project for B2B SaaS Customer Success teams.**

> *Work in progress. Active development.*

---

## The problem

In mid-sized B2B SaaS companies, Customer Success Managers (CSMs) handle 60–120 accounts each — without data-driven prioritization. Churn is discovered reactively: when the customer is already halfway out the door. This project builds a predictive Customer Health Score and a churn driver analysis layer to shift the CS team from reactive to proactive.

The simulated stakeholder is a Head of Customer Success answering, every Monday:

> *"Which 20 accounts should my team focus on this week, and what specific action should they take?"*

---

## What's in this repo

| Folder | Purpose |
|---|---|
| `docs/` | Project brief, methodology, analytical decisions. Start here. |
| `data/` | Synthetic SaaS operational data (18 months, ~800 accounts). |
| `sql/` | Schema definition and feature engineering queries. |
| `notebooks/` | EDA, modeling, and validation, numbered in order. |
| `src/` | Reusable pipeline code. |
| `dashboard/` | Power BI file and screenshots. |

---

## Tech stack

Python (pandas, scikit-learn, Faker, numpy) · SQL (SQLite) · Power BI · Git/GitHub.

---

## Key design decisions

- **Synthetic data over public datasets.** No realistic public B2B SaaS churn dataset exists. Designing calibrated synthetic data is itself part of the project.
- **Logistic regression over XGBoost.** The end user is a CSM, not a data scientist. Interpretable coefficients trump marginal accuracy gains.
- **90-day prediction window.** Matches the operational rhythm of renewal management.

Full methodology in [`docs/methodology.md`](docs/methodology.md) (coming soon).

---

## Status

- [x] Project brief
- [x] Repo setup
- [ ] Synthetic data generation
- [ ] SQL schema & EDA
- [ ] Feature engineering
- [ ] Modeling
- [ ] Dashboard
- [ ] Final writeup

---

## Author

**Oscar Fuentes** — Industrial Engineer, MSc Business Analytics candidate.
[LinkedIn] · [GitHub](https://github.com/oscarfuentesdata)