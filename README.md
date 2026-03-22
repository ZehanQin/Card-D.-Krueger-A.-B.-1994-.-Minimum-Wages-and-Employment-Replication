# Minimum Wages and Employment — Replication & Extension
### Card, D. & Krueger, A. B. (1994) | Fast-Food Industry in New Jersey and Pennsylvania

---

## Executive Memo

### The Bottom Line

**Raising the minimum wage did not kill jobs.** When New Jersey increased its minimum wage by 19% in 1992, fast-food restaurants in the state actually added workers compared to identical restaurants across the border in Pennsylvania. However, this employment gain was not shared equally — it was driven almost entirely by larger restaurants, while smaller establishments saw virtually no change.

### How We Isolated the Effect

Imagine two nearly identical neighborhoods separated by a state line. The restaurants, the customers, and the economy are all the same — except one side raises its minimum wage and the other doesn't. By tracking 410 fast-food stores on both sides of the New Jersey–Pennsylvania border before and after the April 1992 wage increase, we can attribute any divergence in hiring patterns specifically to the policy change, since everything else remained constant.

This "border comparison" approach works because eastern Pennsylvania restaurants serve as a natural benchmark for what would have happened in New Jersey without the wage increase. Both groups of stores belong to the same chains (Burger King, KFC, Roy Rogers, Wendy's), face the same regional economy, and experience the same seasonal hiring patterns. The only difference is the policy.

To double-check, we also compared stores *within* New Jersey: those already paying above the new minimum (unaffected by the law) versus those forced to raise wages. The results were consistent — confirming that the employment gains are tied to the wage policy, not to unrelated local conditions.

### The Evidence

The chart below shows our estimated employment effects across different types of restaurants, with confidence ranges. The red dashed line at zero represents "no effect." Points to the right of the line indicate employment gains.

![Forest Plot — DID Estimates by Subgroup](forest_plot_final.png)

**Key takeaways from the chart:**
- **Overall**, New Jersey stores gained roughly 2.8 workers per store relative to Pennsylvania.
- **By ownership**: Both company-owned (+2.0) and franchise (+3.1) stores showed gains — ownership structure did not matter.
- **By size**: Large restaurants gained +5.1 workers (a clear, statistically reliable effect), while small restaurants showed no meaningful change (−0.5). This difference is not due to chance — the gap between large and small is statistically significant.

### What This Means for Policy

1. **Minimum wage increases do not automatically lead to job losses in the fast-food sector.** Policymakers considering moderate minimum wage increases should not assume that employment will decline — the evidence from this natural experiment shows the opposite.

2. **Larger firms absorb wage increases more easily.** The employment gains are concentrated among bigger restaurants, likely because they can spread costs across more revenue, adjust prices, or restructure operations more flexibly. Policy design should consider that smaller establishments may need targeted support (e.g., phased implementation or tax credits) to adjust.

3. **The cost is passed to consumers, not workers.** Meal prices rose approximately 4% faster in New Jersey than Pennsylvania — consistent with restaurants passing the wage increase through to product prices rather than cutting staff. Stakeholders should anticipate modest price increases as part of the adjustment mechanism.

4. **One-size-fits-all analysis misses the full picture.** The headline finding of "no job loss" is accurate on average, but masks important variation. Future minimum wage impact assessments should disaggregate results by firm size to capture differential effects.

---

## Technical Appendix

### Project Overview

**Track**: Track A — The Causal Policy Track (Difference-in-Differences)

**Core Research Question**: Does a rise in the minimum wage reduce employment?

Using the April 1, 1992, increase in New Jersey's minimum wage (from $4.25 to $5.05), Card and Krueger surveyed 410 fast-food restaurants in New Jersey and eastern Pennsylvania before and after the policy change. Their difference-in-differences (DID) estimates found no evidence that the higher minimum wage reduced employment — in fact, New Jersey stores gained roughly 2.76 FTE workers relative to Pennsylvania.

### Data Source

- **Original data**: [David Card's data archive](https://davidcard.berkeley.edu/data_sets.html) (njmin.zip)
- **Original paper**: Card & Krueger (1994) — *American Economic Review*

The raw data files are stored in `data/raw/` and remain unmodified from the original source. The codebook provided by David Card documents the variable definitions and file structure.

### Repository Structure

```
├── README.md                             # Executive Memo + Technical Appendix
├── requirements.txt                      # Python dependencies
├── data/
│   ├── raw/                              # Original njmin data files (immutable)
│   └── processed/                        # Cleaned dataset (card_krueger_clean.csv)
├── notebooks/
│   ├── 01_Data_Cleaning.ipynb
│   ├── 02_Replication_Analysis.ipynb
│   └── 03_Extension_and_Results.ipynb    # Phase 2 + Phase 3 combined
└── forest_plot_final.png
```

### Phase 1: Data Audit & Validation

- Imported the fixed-width `public.dat` file using the codebook specifications.
- Converted placeholder values (`.`) to NaN for numeric columns.
- Verified variable types, missing-value counts, and the distribution of Wave 2 store statuses (open, permanently closed, temporarily closed, refused interview).
- Constructed FTE employment: FTE = full-time workers + managers + 0.5 × part-time workers.
- Handled store closures per the original paper's methodology: permanently closed stores set to 0 FTE; temporarily closed and non-responding stores treated as missing.

### Phase 2: Baseline Replication

Replicated the core results from Card & Krueger (1994):

- **Table 2** — Summary statistics (FTE employment, starting wages, full meal prices) by state, confirming pre-treatment comparability.
- **Table 3** — Simple DID: New Jersey stores gained +2.76 FTE relative to Pennsylvania (t = 2.03), consistent with the paper's reported value.
- **Table 4** — Reduced-form regressions:
  - Model (i): NJ dummy = +2.28 (paper: 2.33)
  - Model (iv): GAP variable with chain/ownership controls = +16.36 (paper: 14.92)
- DID regression with cluster-robust standard errors — Interaction coefficient (treat × post) = +2.75 (p = 0.035).

All replicated estimates closely match the original paper's findings.

### Phase 3: Extension — Heterogeneous Treatment Effects

**Motivation**: The original paper reports a single average treatment effect. This extension asks: does the minimum wage affect all restaurants equally, or do employment responses vary by firm characteristics?

**Extension 1: Ownership Structure (Company-Owned vs. Franchise)**

- Hypothesis: Company-owned stores (centralized HR) and franchise stores (independent owners) may respond differently to wage mandates.
- Method: Interaction regression — dFTE ~ NJ + CO_OWNED + NJ × CO_OWNED + chain controls (HC1 robust SEs).
- Results:
  - Franchise DID: +3.13 FTE (SE = 1.89)
  - Company-Owned DID: +2.04 FTE (SE = 1.55)
  - Interaction term: −0.48 (p = 0.85) → not statistically significant
- Placebo test (PA only): Welch t-test comparing ownership types within Pennsylvania yields p = 0.87, confirming ownership is not a confound.
- Conclusion: Both ownership types show non-negative employment effects; no statistically significant heterogeneity by ownership.

**Extension 2: Firm Size (Small vs. Large Restaurants)**

- Definition: Stores classified as "Small" or "Large" based on the median pre-treatment (Wave 1) FTE to avoid endogeneity.
- Method: Interaction regression — dFTE ~ NJ + large + NJ × large + chain/ownership controls.
- Results:
  - Small restaurants DID: −0.45 FTE (t = −0.34) → essentially zero effect
  - Large restaurants DID: +5.09 FTE (t = 2.54) → statistically significant
  - NJ × Large interaction: +6.77 (p = 0.006)
- Conclusion: The positive employment effect of the minimum wage is concentrated among larger restaurants. Smaller stores were largely unaffected.

### Executive Summary

This extension both **supports and contextualizes** the original Card and Krueger (1994) findings. The core conclusion — that minimum wages do not reduce employment — is upheld across all subgroups. Both ownership types exhibit non-negative employment responses, with the franchise DID at +3.13 and company-owned at +2.04; however, the difference is not statistically significant (interaction p = 0.85). A placebo test within Pennsylvania (p = 0.87) confirms that ownership structure does not confound the results.

The more striking finding is the heterogeneity by firm size. Large restaurants (above-median pre-treatment FTE) show a statistically significant positive DID of +5.09 (t = 2.54), while small restaurants show essentially no effect (−0.45). The NJ × Large interaction is significant at p = 0.006. This suggests the overall positive DID of +2.76 is driven primarily by larger establishments, which may have greater capacity to absorb wage increases through price pass-through and operational adjustments. These findings enrich the original results by revealing that firm scale — rather than ownership structure — is a meaningful margin of heterogeneity in the employment response to minimum wage policy.

### GenAI Disclosure

Generative AI (Claude by Anthropic) was used as a pair-programmer in Phase 3 for:

- Designing the heterogeneous treatment effects framework (interaction terms for ownership and firm size).
- Writing the placebo test using Welch's t-test.
- Constructing the firm-size split using pre-treatment FTE to avoid endogeneity.
- Creating the dark-themed forest plot with matplotlib.

All AI-generated code was audited, validated against the original paper's methodology, and the econometric logic was verified by the author. Full prompts are documented in the final notebook.

### References

- Card, D., & Krueger, A. B. (1994). Minimum Wages and Employment: A Case Study of the Fast-Food Industry in New Jersey and Pennsylvania. *American Economic Review*, 84(4), 772–793.
- Card, D., & Krueger, A. B. (1993). Minimum Wages and Employment: A Case Study of the Fast Food Industry in New Jersey and Pennsylvania. NBER Working Paper No. 4509.
