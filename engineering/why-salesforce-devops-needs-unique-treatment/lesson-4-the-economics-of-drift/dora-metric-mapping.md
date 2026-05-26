---
description: >-
  Git-only Salesforce pipelines typically operate two tiers below DORA Elite
  (DORA 2024 State of DevOps Report).
---

# DORA Metric Mapping

DORA's four key metrics (Deployment Frequency, Lead Time for Changes, Change Failure Rate, and Mean Time to Restore) are the industry standard for measuring software delivery performance (DORA State of DevOps Report, 2024). Each metric has a direct mapping to the structural failure modes of Git-based Salesforce pipelines.

This page documents that mapping explicitly. The claim is not that every Git-based Salesforce team operates at Low tier; it is that the structural characteristics of this architecture create pressure on each metric in a predictable direction, and that pressure is architectural, not organizational.

{% tabs %}
{% tab title="Deployment Frequency" %}
**DORA Elite:** Multiple deployments per day **Typical Git-based Salesforce:** 1–4 per month (Medium tier) to fewer than 1 per month (Low tier)

**Structural causes:**

1. **Drift accumulation.** As sandboxes diverge over time, the pre-deployment reconciliation effort increases. Teams that cannot automate this reconciliation add manual review cycles before each deployment, extending the minimum time between releases.
2. **Trust-eroded gate insertion.** After deployment failures caused by semantic merge errors or overwrite incidents, teams add manual approval checkpoints. These gates add elapsed time per deployment and effectively cap deployment frequency.
3. **Large-batch pressure.** Teams that cannot manage fine-grained deployments efficiently aggregate changes into larger batches to amortize the per-deployment overhead. Larger batches carry higher failure risk, which reinforces the slower cadence.

**Metadata-native resolution:** A system that continuously syncs org state, detects drift automatically, and validates deployments against live org state eliminates the pre-deployment manual review requirement. Deployment frequency increases because each individual deployment is cheaper to validate and execute.
{% endtab %}

{% tab title="Lead Time for Changes" %}
**DORA Elite:** Less than 1 hour **Typical Git-based Salesforce:** 1 week to 1 month (Medium tier)

**Structural causes:**

1. **Org-vs-repo reconciliation.** Before each deployment, someone must compare what the repo expects to what the org contains. This step is not automatable in a wrapper architecture; it requires human judgment about drift and conflict resolution. Typical duration: 2–8 hours per release cycle.
2. **Dependency auditing.** The deployment changeset must be audited for undetected dependencies before reaching production. This step catches the errors that pre-deployment validation in the pipeline doesn't detect, but it adds lead time proportional to changeset complexity.
3. **Multi-environment promotion chain.** Changes must travel through Dev → QA → Staging → Production, with manual validation at each stage. In an org with sandbox drift, each promotion step may require additional reconciliation.

**Metadata-native resolution:** Org-aware validation performs the reconciliation step automatically before each deployment. Dependency resolution against the live org dependency graph eliminates the manual audit. Lead time decreases because the automation reliably catches what was previously caught manually.
{% endtab %}

{% tab title="Change Failure Rate" %}
**DORA Elite:** 0%–15% **Typical Git-based Salesforce:** Varies significantly; elevated by semantic merge failures and overwrite incidents

**Structural causes:**

1. **Semantic merge failures.** Git merges two versions of a metadata component successfully (no conflict marker, valid XML) but produces an incorrect semantic result. The deployment succeeds; the org state is incorrect. This registers as a change failure only when the incorrect state is detected, which may be hours or days after deployment.
2. **Overwrite incidents.** A deployment that does not check the target org's current state before writing may silently overwrite in-org changes made after the last sync. The pipeline reports success; a previously working configuration is reverted.
3. **Dependency failures at deploy time.** Changesets that pass pre-deployment validation in a wrapper tool may fail in production due to undetected cross-component dependencies. The failure mode is not preventable at the wrapper layer because it requires org-state awareness the wrapper doesn't have.

**Metadata-native resolution:** Semantic merge eliminates the first category. Overwrite protection (pre-deployment org state query) eliminates the second. Live dependency graph validation eliminates the third. Each is a first-class capability in a metadata-native system, not a workaround.
{% endtab %}

{% tab title="Mean Time to Restore" %}
**DORA Elite:** Less than 1 hour **Typical Git-based Salesforce:** 4–24 hours (Low tier)

**Structural cause:** Salesforce has no native transaction rollback. When a deployment produces an incorrect org state, restoration requires manual reconstruction: identify the delta between current state and desired prior state, construct a reverse deployment, validate it in a sandbox, then deploy to production while the business operates on the degraded state.

This process is irreducibly manual in the absence of a pre-deployment org snapshot. It requires senior engineering attention and occupies 4–8 hours minimum for a non-trivial failure. Incidents involving interrelated metadata components, where the incorrect state has propagated to dependent components, can extend MTTR to 12–24 hours.

**Metadata-native resolution:** First-class rollback requires a full org snapshot before each deployment. With a pre-deployment snapshot, restoration is an operation with a defined execution path and a predictable time-to-completion measured in minutes, not hours. DORA Elite-tier MTTR becomes achievable because the infrastructure exists.
{% endtab %}
{% endtabs %}

## Summary table

| DORA Metric           | Git-based Salesforce tier | Primary structural cause                    | Metadata-native resolution                       |
| --------------------- | ------------------------- | ------------------------------------------- | ------------------------------------------------ |
| Deployment Frequency  | Medium–Low                | Drift accumulation, manual gates            | Automated org sync, org-aware validation         |
| Lead Time for Changes | Medium                    | Manual reconciliation, dependency audits    | Org-aware validation, live dependency resolution |
| Change Failure Rate   | Elevated vs. Elite        | Semantic merge, overwrites, dependency gaps | Semantic merge engine, overwrite protection      |
| MTTR                  | Low (4–24 hours)          | No native rollback, manual reconstruction   | Pre-deployment snapshots, first-class rollback   |

## Goodhart's Law and the limits of DORA as a target

The DORA framework is a measurement tool, not a goal. When an organization treats DORA tier as a target rather than an indicator, measurement pressure distorts the signal.

The canonical form of this distortion: a team raises Deployment Frequency by deploying smaller, lower-risk changes more often, without addressing the semantic merge errors, overwrite incidents, or sandbox drift that cause Change Failure Rate elevation. The frequency metric improves. The underlying pipeline reliability does not. DORA tier advances. The structural failure modes remain in place.

Using DORA metrics as a business case for tooling investment requires measuring the right baseline: not what the team has optimized, but what the architecture constrains. A team that has responded to pipeline unreliability by restricting deployments to low-risk changes has suppressed the deployment frequency and lead time metrics, but the constrained numbers accurately reflect the actual pipeline confidence, not a performance failure of the team.

The structural argument is: if this team removed the constraints and deployed at the cadence a reliable pipeline would support, what would the DORA metrics look like? That counterfactual is the real signal. The measured current state is the suppressed, trust-compensated version.

{% hint style="warning" %}
**Note on data:** DORA's annual reports do not publish Salesforce-specific cohort data. The "two tiers below Elite" characterization is derived from the structural analysis in this lesson applied to DORA tier definitions, not from a direct DORA-published Salesforce benchmark. Treat as a structural inference, not a DORA citation.
{% endhint %}
