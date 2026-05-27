---
description: Full sensitivity-range spreadsheet for the cost model.
---

# Sensitivity-Range Spreadsheet

The cost model in the previous pages is a function of several inputs. Not all inputs contribute equally to the output. This page documents the sensitivity of the total cost estimate to each input, so analysts can identify which values to source precisely and which ballpark figures are acceptable.

The analysis uses a $358,600 base-case total (from the worked example in the break-even calculation) and measures how the total changes as each input varies across its plausible range.

## Input sensitivity table

| Input                           | Low      | Base     | High     | Total impact (low→high)      | Sensitivity    |
| ------------------------------- | -------- | -------- | -------- | ---------------------------- | -------------- |
| RM loaded annual salary         | $150,000 | $190,000 | $230,000 | $80,000                      | **High**       |
| Developer deployment overhead % | 10%      | 17.5%    | 25%      | $120,000 (6 devs)            | **High**       |
| Developer headcount             | 4        | 6        | 10       | Scales linearly              | **High**       |
| Change failure rate             | 5%       | 12.5%    | 25%      | $23,000                      | **Medium**     |
| MTTR (hours per incident)       | 4        | 6        | 8        | $7,680                       | **Medium**     |
| Compliance overhead             | $20,000  | $40,000  | $75,000  | $55,000                      | **Medium**     |
| Senior dev hourly rate          | $80      | $100     | $120     | $15,360 (at base F and N)    | **Low**        |
| Deployments per year            | 24       | 48       | 104      | $23,040 (at base MTTR and F) | **Low–Medium** |

## High-sensitivity inputs

These three inputs have the biggest effect on the final number. Focus your data collection here first.

### RM loaded annual salary

The Release Manager salary range spans $80,000 in the market data. This single variable accounts for a $80,000 swing in the model output. For organizations where the RM salary is known precisely, replacing the market-range midpoint with the actual figure significantly tightens the model.

**Recommendation:** Source internally. Use the market range only if the role is unfilled or if the function is absorbed informally by multiple developers without a formal allocation.

### Developer deployment overhead percentage

This input (the fraction of developer time consumed by deployment-related tasks rather than feature development) has the highest total impact of any variable in the model. At 6 developers, the swing from 10% to 25% produces a $120,000 difference in the Human Translator Tax formula.

The 10%–25% range comes from Gearset's 2025 State of Salesforce DevOps survey (vendor-published, self-selected respondents; treat as directional). It is the weakest-sourced of the high-sensitivity inputs.

**Recommendation:** Measure internally if possible. A two-week time-tracking exercise across the development team, capturing time spent on deployment prep, post-deployment investigation, sandbox management, and conflict resolution, produces a Tier 1.5 input that replaces the Tier 5 default.

### Developer headcount

The model scales linearly with developer count. This input is always known precisely for internal analysis. It requires no estimation.

## Medium-sensitivity inputs

These inputs contribute real variation, but within a tighter range. Good to get right, not critical to get perfect.

### Change failure rate

The change failure rate input drives the Deployment Loss formula. The range from 5% to 25% produces a $23,000 swing at the base deployment frequency. This is meaningful but less volatile than the high-sensitivity inputs.

**Recommendation:** Source from deployment logs. Most Git-based Salesforce pipelines have deployment history that allows a direct calculation: count deployments requiring post-deployment intervention divided by total deployments over a 12-month period.

### Compliance overhead

The compliance overhead range ($20,000–$75,000) depends primarily on whether the organization has experienced a compliance audit that surfaced the retention gap. For organizations without recent audit history, the lower bound is more likely. For organizations in heavily regulated industries (financial services, healthcare), the upper bound and the probabilistic audit-incident cost term both deserve more weight.

## Low-sensitivity inputs

These two have limited impact under normal conditions. They're included for completeness and become more relevant at high failure rates.

### Senior developer hourly rate and deployments per year

At base values of the other inputs, the senior developer hourly rate and annual deployment count each have limited impact on the total. They become more significant as change failure rate increases: the Deployment Loss formula is a product of all four variables. At high failure rates and high deployment frequency, these inputs become medium-sensitivity.

## Using the sensitivity data

The practical value of this table is in directing where to invest time in data collection. For a first-pass estimate built on market-range defaults, the model is most accurate if the two high-sensitivity inputs that can be sourced internally (developer headcount and developer deployment overhead percentage) are measured rather than estimated.

A model built on:

* Actual developer headcount (always available)
* Two weeks of deployment-overhead time tracking (low effort, high accuracy)
* Actual RM salary or market midpoint

...produces a result accurate within 15%–20% of true annual cost without requiring external benchmarking for the highest-impact variables.

That accuracy level is typically sufficient for a business case. For formal ROI analysis used in procurement approval, replace all Tier 4 and Tier 5 defaults with internally sourced figures before publishing.
