---
description: Formulas and documented assumptions behind the economic case.
---

# The Formal Cost Model

This page defines the three formal cost formulas used in this lesson's economic analysis. Each formula includes its variable table, source tier for each input, and the assumptions baked into the structure.

The goal is a replicable model. Market-range defaults are provided for inputs without internal data; replace them with organization-specific values wherever possible.

## Formula 1: Human Translator Tax (HTT)

```
HTT = (RM_FTE × L_RM)
    + (D × L_D × R_dep)
```

| Variable | Definition                                  | Default/Range                       | Source                                            |
| -------- | ------------------------------------------- | ----------------------------------- | ------------------------------------------------- |
| `RM_FTE` | Release Manager full-time equivalents       | 1.0 (adjust for partial allocation) | Internal headcount                                |
| `L_RM`   | Fully loaded annual salary, RM role         | $150K–$230K                         | Salary.com, Glassdoor, ZipRecruiter (Tier 4)      |
| `D`      | Developer headcount                         | -                                   | Internal                                          |
| `L_D`    | Fully loaded annual developer salary        | \~$160K–$180K loaded                | BLS OEWS May 2024: $133,080 base (Tier 1)         |
| `R_dep`  | Fraction of dev time on deployment overhead | 0.10–0.25                           | Gearset 2025 (Tier 5; vendor survey; directional) |

{% hint style="info" %}
**Assumption:** The RM role is entirely attributable to the architectural gap. If the RM performs additional duties (project management, stakeholder coordination), prorate `RM_FTE` to the reconciliation function only.
{% endhint %}

## Formula 2: Deployment Loss (DL)

```
DL = F × N × MTTR × (S × L_S / 2080)
```

| Variable | Definition                                                                       | Default/Range | Source                                                              |
| -------- | -------------------------------------------------------------------------------- | ------------- | ------------------------------------------------------------------- |
| `F`      | Change failure rate (fraction of deployments requiring post-deploy intervention) | 0.10–0.30     | Internal deployment logs; industry reference: Gearset 2025 (Tier 5) |
| `N`      | Deployment count per year                                                        | -             | Internal release calendar                                           |
| `MTTR`   | Mean time to restore, in hours                                                   | 4–8 hours     | Lesson 3 structural analysis; no external citation available        |
| `S`      | Senior developers engaged per incident (typically 2)                             | 2             | Internal                                                            |
| `L_S`    | Fully loaded annual salary, senior developer                                     | \~$175K–$220K | Salary.com/Glassdoor for senior Salesforce developers (Tier 4)      |
| `2080`   | Working hours per year (52 weeks × 40 hours)                                     | Fixed         | -                                                                   |

{% hint style="info" %}
**Assumption:** MTTR represents the hours from failure detection to confirmed org restoration. In Salesforce without native rollback, this includes diagnosis, reverse deployment construction, sandbox validation, and production deployment. The 4–8 hour range is consistent with the manual reconstruction process described in Lesson 3 but is not externally cited.
{% endhint %}

## Formula 3: Compliance Overhead (CO)

```
CO = (E_h × 12 × L_h)
   + C_storage
   + P(audit) × C_incident
```

| Variable     | Definition                                                                                   | Default/Range                          | Source                                           |
| ------------ | -------------------------------------------------------------------------------------------- | -------------------------------------- | ------------------------------------------------ |
| `E_h`        | Hours per month to export and archive Salesforce Setup Audit Trail before 180-day expiration | 4–16 hours depending on org complexity | Internal                                         |
| `L_h`        | Fully loaded hourly rate for compliance/admin staff performing export                        | $50–$100/hour                          | Salary aggregators (Tier 4)                      |
| `C_storage`  | Annual cost of external audit data retention system                                          | $5K–$30K                               | Internal; vendor quotes                          |
| `P(audit)`   | Annual probability of a compliance audit that surfaces the retention gap                     | 0.05–0.20 (estimate)                   | Internal risk assessment                         |
| `C_incident` | Cost to remediate a compliance finding: consulting, legal review, system build-out           | $50K–$200K                             | Industry estimate (no direct citation available) |

{% hint style="info" %}
**Assumption:** This formula covers the gap between Salesforce's 180-day Setup Audit Trail retention and the 5–7 year retention requirement of SOX, HIPAA, and GDPR (Salesforce documentation, Tier 3). The compliance exposure cost (`P(audit) × C_incident`) is probabilistic; organizations with recent audit history or upcoming regulatory reviews should weight it more heavily.
{% endhint %}

## Total Annual Current-State Cost

```
Total = HTT + DL + CO
```

This is the annual cost of operating a Git-based Salesforce DevOps program at a level of complexity where all three cost categories are active. The break-even calculation compares this total against the total cost of ownership of a purpose-built alternative.

## Source tiers applied in this model

| Tier   | Description                                              | Variables using this tier            |
| ------ | -------------------------------------------------------- | ------------------------------------ |
| Tier 1 | BLS OEWS (federal government data)                       | `L_D` base salary                    |
| Tier 3 | Salesforce documentation                                 | Audit trail 180-day retention period |
| Tier 4 | Salary aggregators (Salary.com, Glassdoor, ZipRecruiter) | `L_RM`, `L_S`, `L_h`                 |
| Tier 5 | Vendor-published surveys (Gearset 2025)                  | `R_dep`, `F` defaults                |

Tier 5 inputs require explicit directional caveats in any published analysis. Replace with internal data wherever available; internal data is Tier 1.5 by convention and supersedes market defaults.

When Flosum customer-specific data becomes available (deployment frequency logs, MTTR measurements, Release Manager headcount reduction post-migration), those figures should replace Tier 4 and Tier 5 defaults as primary citations.
