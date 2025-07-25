# Sprint 6 Plan – Credit Risk & Data Quality Enhancements

**Duration:** 5 days (Mon–Fri)
**Sprint Goal:** Layer basic credit-risk intelligence onto the existing PDF→JSON→PV pipeline (risk tier + discount mapping).

---

## Why This Sprint?
* The valuation engine now outputs PV/offer, but all leases assume a single analyst-supplied discount rate.
* Stakeholders need a quick “risk tier” flag to justify different discount rates (8 % vs 12 %).

---

## User Stories

### Story 1 – Lessee Credit Quick-Score (CORE)
**As a** credit analyst  
**I want** the pipeline to auto-flag whether the lessee is a public company, long-lived SPV, or small LLC  
**So that** discount rates can differ by risk tier.

*Acceptance Criteria*
- New CLI `credit_lookup.py <lessee_name>` hits SEC EDGAR & OpenCorporates APIs.  
- Returns JSON with `public_company` (bool), `years_since_incorp`, `state_of_incorp`.
- `value.py` applies discount rate mapping:
  - Low risk (`public && years > 10`) → 8 %
  - Medium (`years 5–10`) → 10 %
  - High (`years < 5` or non-public) → 12 %

### Story 2 – Update Wrapper & Reports
**As a** stakeholder  
**I want** the lease summary table to include `risk_tier` and `discount_rate` columns  
**So that** I can see why offers differ.

*Acceptance Criteria*
- `output/lease_summary.md` gains two new columns populated via credit lookup.  
- Markdown regenerated automatically by `analyze_leases.py`.

### Story 3 – Unit Tests & CI Expansion
*Acceptance Criteria*
- Mock HTTP call for SEC & OpenCorporates lookups (no network in tests).  
- Coverage remains ≥ 90 %.

---

## Timebox & Tasks
| Day | Task | Hrs |
|-----|------|----|
| Mon | Build credit lookup client w/ SEC API | 4 |
| Tue | Map lookup JSON → risk tier → discount | 3 |
| Wed | Extend extraction tests | 3 |
| Thu | Update wrapper, regenerate reports | 3 |
| Fri | CI tweaks, doc polish, sprint review | 3 |

---

## Definition of Done
- `credit_lookup.py` returns correct JSON for test LLCs.
- `output/lease_summary.md` shows risk tier & applied discount for each processed lease.
- All tests pass; GitHub Actions green.

---

## Out-of-Scope
- Full financial-statement credit scoring.  
- UI/dashboard work.  
- Pricing optimisation beyond discount-rate tiers.

---

Prepared by PM – 2025-07-12 