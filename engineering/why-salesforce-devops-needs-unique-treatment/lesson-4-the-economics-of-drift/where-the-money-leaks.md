---
description: >-
  Failed deployments, rollback hours, release delays, audit risk, developer
  burnout.
---

# Where the Money Leaks

The seven mismatches in Lesson 3 are not abstract design observations. Each one has a direct cost mapping to DORA's four key metrics and to the operational load on your engineering team. This lesson maps the mismatches to measurable cost categories.

The five categories below are not comprehensive; they are the highest-signal cost indicators. Each can be quantified, tracked, and presented to stakeholders as the operational tax of the current architecture.

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><strong>Failed deployments</strong></td><td>Elevated Change Failure Rate, caused by semantic merge errors and overwrite incidents the pipeline cannot detect before deployment.</td><td><a href="where-the-money-leaks.md#id-1.-failed-deployments">#id-1.-failed-deployments</a></td></tr><tr><td><strong>Rollback hours</strong></td><td>MTTR locked at Low DORA tier: no native rollback means 4–8 hours of manual org reconstruction per incident.</td><td><a href="where-the-money-leaks.md#id-2.-rollback-hours">#id-2.-rollback-hours</a></td></tr><tr><td><strong>Release delays</strong></td><td>Deployment Frequency and Lead Time degraded by drift accumulation, manual pre-deploy audits, and trust-eroded gate insertion.</td><td><a href="where-the-money-leaks.md#id-3.-release-delays">#id-3.-release-delays</a></td></tr><tr><td><strong>Audit risk</strong></td><td>180-day Setup Audit Trail vs. 5–7 year compliance retention requirements: a gap that must be closed manually or accepted as exposure.</td><td><a href="where-the-money-leaks.md#id-4.-audit-risk">#id-4.-audit-risk</a></td></tr><tr><td><strong>Developer burnout</strong></td><td>Scarcity multiplied by attrition: replacing a senior Salesforce developer costs 1–2× annual salary and removes institutional org knowledge.</td><td><a href="where-the-money-leaks.md#id-5.-developer-burnout">#id-5.-developer-burnout</a></td></tr></tbody></table>

## 1. Failed deployments

**DORA metric: Change Failure Rate**

Deployment failures in Salesforce Git pipelines arise from two structural sources: semantic merge errors (valid XML that deploys but produces incorrect org state) and overwrite incidents (a deployment succeeds but silently reverts an in-org change). Both register as production failures; neither is detectable by the pipeline before deployment.

Eighty-two percent of Salesforce teams report experiencing deployment delays regularly, with an average of 3.8 months of lost release capacity per year (Gearset State of Salesforce DevOps 2025; vendor-published survey, self-selected respondents; treat as directional). At the BLS median developer salary of $133,080 (BLS OEWS, May 2024), 3.8 months of developer time is approximately $42,000 per developer in diverted capacity.

Change Failure Rate elevation is the DORA signature of a semantically unreliable deployment pipeline.

## 2. Rollback hours

**DORA metric: Mean Time to Restore (MTTR)**

Salesforce has no native transaction rollback. When a deployment produces an incorrect org state, restoring the prior state requires manual reconstruction: identify what changed, build a reverse deployment, validate against a sandbox, deploy to production. This process typically takes 4–8 hours of senior engineering time.

In high-DORA-performance organizations (Elite tier), MTTR is measured in minutes, not hours. Git-based Salesforce pipelines cannot achieve Elite-tier MTTR because they lack the org snapshot infrastructure that a first-class rollback capability requires. They are structurally constrained to Low or Medium DORA tier on this metric.

## 3. Release delays

**DORA metric: Deployment Frequency and Lead Time for Changes**

Release delays compound from two sources:

**Drift accumulation.** As the number of active sandboxes increases and admin changes occur between releases, the pre-deployment reconciliation work grows. Teams add manual review cycles to catch drift. Each review adds lead time.

**Manual gate insertion.** Teams that have experienced deployment failures insert manual approval checkpoints into the pipeline to reduce risk. These gates are rational individually but erode the deployment frequency and lead time metrics that distinguish Elite from Low performers.

The combined effect on DORA: deployment frequency decreases, lead time for changes extends. Teams in this pattern move from Medium toward Low performer tier.

## 4. Audit risk

**Not a DORA metric: a compliance exposure**

The Salesforce Setup Audit Trail retains 180 days of configuration history. SOX, HIPAA, and GDPR each require 5–7 years of records retention. That gap of approximately 6.5 years represents both a compliance exposure and an ongoing operational cost.

The cost has two components. First, the engineering and administrative effort to build and maintain a separate audit data retention system: exporting audit data regularly, storing it in a compliant repository, maintaining chain of custody for legal hold. Second, the incident response cost when the gap surfaces during an audit, typically the most expensive time to discover it.

## 5. Developer burnout

**Indirect DORA impact: all four metrics**

Experienced Salesforce developers are both scarce and expensive. When their time is consumed by manual conflict resolution, drift management, deployment investigation, and post-deployment fix cycles, they are not doing the work they were hired for.

Replacing a senior Salesforce developer costs a minimum of one to two times their annual salary in recruiting, onboarding, and ramp-up time. The institutional knowledge loss doesn't appear in any cost model. A team that loses two senior developers in a 12-month period due to tooling-induced burnout has degraded every DORA metric simultaneously: fewer deployments, longer lead times, higher failure rates, slower recovery.

## The combined DORA picture

A team paying all five costs is operating at Low DORA tier across multiple metrics simultaneously. The goal of this lesson is to quantify that precisely enough to build a business case. The subsequent pages provide the formula structure and variable definitions needed to do that.
