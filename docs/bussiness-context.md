# Business Context — Acme Analytics Cloud

> Simulated company profile and design decisions underlying the synthetic dataset.
> All parameters calibrated against public SaaS benchmarks (OpenView SaaS Benchmarks, SaaS Capital Annual Survey).

---

## Company profile

**Acme Analytics Cloud** is a fictional B2B SaaS company used as the analytical context for this project. Profile:

- **Product:** Self-service Business Intelligence platform. Lets non-technical teams connect data sources (Salesforce, HubSpot, SQL databases, spreadsheets) and build dashboards, automated reports, and alerts without needing an analyst.
- **Competitor category:** Tableau, Looker, Mode, Domo, Sisense, ThoughtSpot.
- **Business model:** Annual subscription, seat-based pricing, monthly billing.
- **ICP:** Companies with 50–5,000 employees, ops/finance/marketing teams needing recurring reports without dedicated data teams.
- **Stage:** Series B, ~4 years in market.
- **Scale:** ~800 active accounts, $25M–$40M ARR range.
- **CS team:** 8 CSMs managing the portfolio, ~100 accounts per CSM on average.

**Why this profile:** Series B with 800 accounts is the operational sweet spot where predictive health scoring adds value. Smaller companies don't need prioritization models; larger companies typically already have them.

---

## Decision 1 — Customer segmentation

Portfolio distributed across three standard SaaS segments:

| Segment | % portfolio | # accounts | Employee range | ARR per account | Annual churn rate |
|---|---|---|---|---|---|
| SMB | 60% | 480 | 50–200 | $6k–$30k | 18% |
| Mid-Market | 30% | 240 | 200–1,000 | $30k–$100k | 10% |
| Enterprise | 10% | 80 | 1,000–5,000 | $100k–$400k | 5% |

**Rationale:**

- Distribution (60/30/10) reflects a typical Series B "land and expand" pyramid.
- Churn rates calibrated against SaaS Capital benchmarks: SMB 15–25%, Mid-Market 8–12%, Enterprise 3–7%. Values chosen sit slightly above median, consistent with a Series B that hasn't fully optimized Customer Success — precisely the premise of this project.
- ARR ranges match public pricing of competitor category (Looker, Mode, Hex) at ~$50–$250 per seat/month.

**Implication for modeling:** Churn behavior is heterogeneous across segments. Leading indicators differ (SMB is product-driven; Enterprise is relationship-driven). The modeling phase must evaluate unified vs. segmented models.

---

## Decision 2 — Industry mix

6 industries assigned across the portfolio with differentiated churn modifiers:

| Industry | % portfolio | Churn modifier |
|---|---|---|
| Technology / SaaS | 25% | baseline |
| Financial Services | 20% | −20% (more stable) |
| Retail / E-commerce | 20% | +25% (more volatile) |
| Healthcare | 15% | −15% (regulated, sticky) |
| Manufacturing | 10% | baseline |
| Media / Marketing Agencies | 10% | +30% (highly volatile) |

**Example application:** An SMB (base churn 18%) in Retail (+25% modifier) has effective annual churn rate of 22.5%. An SMB in Financial Services (−20%) has 14.4%.

**Rationale:** Industry modifiers calibrated from SaaS Capital Benchmarks showing that healthcare and financial services retain 15–20% better than average, while retail and media agencies suffer 25–30% higher churn.

**Implication for modeling:** Allows the model to learn industry × segment interactions, a third layer of sophistication beyond naive homogeneous modeling.

---

## Decision 3 — Pricing structure

3 plans offered by Acme:

| Plan | Price/user/month | Typical seat range | Typical ARR | Primary segment |
|---|---|---|---|---|
| Starter | $50 | 10–50 | $6k–$30k | SMB |
| Professional | $120 | 40–200 | $30k–$150k | Mid-Market |
| Enterprise | $150–$250 (negotiated) | 200–1,000+ | $100k–$500k+ | Enterprise |

**Plan × segment assignment (with realistic overlap):**

| Segment | % in Starter | % in Professional | % in Enterprise |
|---|---|---|---|
| SMB | 85% | 15% | 0% |
| Mid-Market | 10% | 70% | 20% |
| Enterprise | 0% | 10% | 90% |

**Plan dynamics modeled across 18 months:**

- **Expansion** (upgrade or seat growth): base probability ~15%/year, higher for healthy accounts (high NPS, high engagement).
- **Contraction** (downgrade or seat reduction): base probability ~8%/year. Strong leading indicator of upcoming churn.
- **Cross-sell** (add-ons): out of scope for V1.

**Rationale:** Plan changes — especially contraction — are among the strongest predictors of churn the model will learn. Static plans would eliminate a critical signal.

**Implication for modeling:** MRR movement is a key engineered feature. For portfolio prioritization, the final dashboard ranks accounts by `churn_probability × MRR`, not probability alone.

---

## Decision 4 — Temporal window

**Dataset span:** January 1, 2024 to June 30, 2025 (18 months).

**Account lifecycle mix:**
- 50% accounts signed before Jan 2024 (legacy accounts).
- 35% accounts signed during 2024.
- 15% accounts signed during 2025.

**Prediction point:** March 31, 2025.
**Outcome window:** 90 days (April 1 – June 30, 2025).

**Temporal split for modeling:**

```
Jan 2024 ─────────────────────────────── Jun 2025
|──── TRAINING (12 months) ────|── VAL ──|─ TEST ─|
                                 (2 mo)    (1 mo + 3 mo outcome)
```

- **Training:** Jan–Dec 2024 features, with outcomes observed in Q1 2025.
- **Validation:** Jan–Feb 2025 for hyperparameter tuning.
- **Test (hold-out):** March 31, 2025 prediction point, with outcomes observed Apr–Jun 2025.

**Critical design rule:** Split is **temporal, not random**. Random split would cause temporal leakage (future data informing past predictions), making the model artificially accurate in validation and useless in production. This is the #1 technical error in junior portfolio projects.

**Churn timing:** Concentrated near contract renewal dates (anniversaries of contract start), not uniformly distributed. Reflects real B2B SaaS churn dynamics.

---

## Expected data volume

| Table | Approximate rows |
|---|---|
| accounts | ~800 |
| users | ~15,000–25,000 |
| usage_events | **~5–10 million** |
| support_tickets | ~15,000–25,000 |
| nps_surveys | ~3,000–5,000 |
| csm_interactions | ~8,000–12,000 |

Manageable in SQLite without performance issues. Demonstrates ability to work with non-trivial data volumes.

---

## Defense-ready summary

When asked in interviews why each decision was made, the short answers:

- **Why 60/30/10 segment split?** Reflects Series B "land and expand" pyramid from OpenView Benchmarks. Gives enough volume of churn events across segments to train and evaluate heterogeneous behavior.
- **Why 6 industries with modifiers?** Captures industry-specific retention patterns documented by SaaS Capital. Enables the model to learn industry × segment interactions.
- **Why 3 pricing tiers with dynamic movement?** Contraction is one of the strongest churn predictors available. Eliminating plan dynamics would remove this signal.
- **Why 18 months and temporal split?** Need 12-month training window + validation + 90-day outcome window. Temporal split prevents leakage that invalidates random-split models in production.
- **Why ~800 accounts?** Series B sweet spot: enough volume for modeling, small enough that the operational problem (CSM prioritization) genuinely exists.

---

*End of business context. See `project-brief.md` for analytical framing and `design-decisions.md` for cross-phase technical decisions.*