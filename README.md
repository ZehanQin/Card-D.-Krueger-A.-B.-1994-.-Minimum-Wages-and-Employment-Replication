# Card, D. & Krueger, A. B. (1994) — Minimum Wages and Employment: Replication & Extension

## Overview

This repository contains a full replication and original extension of:

> Card, D., & Krueger, A. B. (1994). *Minimum Wages and Employment: A Case Study of the Fast-Food Industry in New Jersey and Pennsylvania.* American Economic Review, 84(4), 772–793.

**Track:** Track A — The Causal Policy Track (Difference-in-Differences)

**Core Research Question:** Does a rise in the minimum wage reduce employment?

Using the April 1, 1992, increase in New Jersey's minimum wage (from $4.25 to $5.05), Card and Krueger surveyed 410 fast-food restaurants in New Jersey and eastern Pennsylvania before and after the policy change. Their difference-in-differences (DID) estimates found no evidence that the higher minimum wage reduced employment — in fact, New Jersey stores gained roughly 2.76 FTE workers relative to Pennsylvania.

---

## Data Source

- **Original data:** [David Card's data archive](https://davidcard.berkeley.edu/data_sets.html) (`njmin.zip`)
- **Original paper:** [Card & Krueger (1994) — AER](https://davidcard.berkeley.edu/papers/njmin-aer.pdf)

The raw data files are stored in `data/raw/` and remain unmodified from the original source. The codebook provided by David Card documents the variable definitions and file structure.

---

## Repository Structure

```
├── data/
│   └── raw/                  # Original njmin data files (immutable)
├── notebooks/
│   ├── 01_Data_Exploration.ipynb
│   ├── 02_Baseline_Replication.ipynb
│   └── 03_Extension_and_Results.ipynb   # Phase 2 + Phase 3 combined
├── README.md
└── ...
```

---

## Phase 1: Data Audit & Validation

- Imported the fixed-width `public.dat` file using the codebook specifications.
- Converted placeholder values (`.`) to `NaN` for numeric columns.
- Verified variable types, missing-value counts, and the distribution of Wave 2 store statuses (open, permanently closed, temporarily closed, refused interview).
- Constructed FTE employment: `FTE = full-time workers + managers + 0.5 × part-time workers`.
- Handled store closures per the original paper's methodology: permanently closed stores set to 0 FTE; temporarily closed and non-responding stores treated as missing.

## Phase 2: Baseline Replication

Replicated the core results from Card & Krueger (1994):

- **Table 2** — Summary statistics (FTE employment, starting wages, full meal prices) by state, confirming pre-treatment comparability.
- **Table 3** — Simple DID: New Jersey stores gained +2.76 FTE relative to Pennsylvania (t = 2.03), consistent with the paper's reported value.
- **Table 4** — Reduced-form regressions:
  - Model (i): NJ dummy = +2.28 (paper: 2.33)
  - Model (iv): GAP variable with chain/ownership controls = +16.36 (paper: 14.92)
- **DID regression with cluster-robust standard errors** — Interaction coefficient (treat × post) = +2.75 (p = 0.035).

All replicated estimates closely match the original paper's findings.

## Phase 3: Extension — Heterogeneous Treatment Effects

### Motivation

The original paper reports a single average treatment effect. This extension asks: **does the minimum wage affect all restaurants equally, or do employment responses vary by firm characteristics?**

### Extension 1: Ownership Structure (Company-Owned vs. Franchise)

- **Hypothesis:** Company-owned stores (centralized HR) and franchise stores (independent owners) may respond differently to wage mandates.
- **Method:** Interaction regression — `dFTE ~ NJ + CO_OWNED + NJ × CO_OWNED + chain controls` (HC1 robust SEs).
- **Results:**
  - Franchise DID: +3.13 FTE (SE = 1.89)
  - Company-Owned DID: +2.04 FTE (SE = 1.55)
  - Interaction term: −0.48 (p = 0.85) → **not statistically significant**
- **Placebo test (PA only):** Welch t-test comparing ownership types within Pennsylvania yields p = 0.87, confirming ownership is not a confound.
- **Conclusion:** Both ownership types show non-negative employment effects; no statistically significant heterogeneity by ownership.

### Extension 2: Firm Size (Small vs. Large Restaurants)

- **Definition:** Stores classified as "Small" or "Large" based on the **median pre-treatment (Wave 1) FTE** to avoid endogeneity.
- **Method:** Interaction regression — `dFTE ~ NJ + large + NJ × large + chain/ownership controls`.
- **Results:**
  - Small restaurants DID: −0.45 FTE (t = −0.34) → essentially zero effect
  - Large restaurants DID: +5.09 FTE (t = 2.54) → **statistically significant**
  - NJ × Large interaction: +6.77 (p = 0.006)
- **Conclusion:** The positive employment effect of the minimum wage is **concentrated among larger restaurants**. Smaller stores were largely unaffected.

### Production-Grade Visualization

A forest plot (coefficient plot) displays all five subgroup DID estimates with 95% confidence intervals, clearly showing the heterogeneity by firm size.

### Executive Summary

This extension examines heterogeneous treatment effects along two dimensions: ownership structure (company-owned vs. franchise) and restaurant size (small vs. large). Both ownership types exhibit non-negative employment responses to the minimum wage increase, with the franchise DID at +3.13 and company-owned at +2.04; however, the difference is not statistically significant (interaction p = 0.85). A placebo test within Pennsylvania (p = 0.87) confirms that ownership structure does not confound the results.

The more striking finding is the heterogeneity by firm size. Large restaurants (above-median pre-treatment FTE) show a statistically significant positive DID of +5.09 (t = 2.54), while small restaurants show essentially no effect (−0.45). The NJ × Large interaction is significant at p = 0.006. This suggests the overall positive DID of +2.76 is driven primarily by larger establishments, which may have greater capacity to absorb wage increases through price pass-through and operational adjustments. These findings enrich the original Card and Krueger (1994) results by revealing that firm scale — rather than ownership structure — is a meaningful margin of heterogeneity in the employment response to minimum wage policy.

---

## GenAI Disclosure

Generative AI (Claude by Anthropic) was used as a pair-programmer in Phase 3 for:

1. Designing the heterogeneous treatment effects framework (interaction terms for ownership and firm size).
2. Writing the placebo test using Welch's t-test.
3. Constructing the firm-size split using pre-treatment FTE to avoid endogeneity.
4. Creating the dark-themed forest plot with matplotlib.

All AI-generated code was audited, validated against the original paper's methodology, and the econometric logic was verified by the author. Full prompts are documented in the final notebook.

---

## References

- Card, D., & Krueger, A. B. (1994). Minimum Wages and Employment: A Case Study of the Fast-Food Industry in New Jersey and Pennsylvania. *American Economic Review*, 84(4), 772–793.
- Card, D., & Krueger, A. B. (1993). Minimum Wages and Employment: A Case Study of the Fast Food Industry in New Jersey and Pennsylvania. NBER Working Paper No. 4509.
