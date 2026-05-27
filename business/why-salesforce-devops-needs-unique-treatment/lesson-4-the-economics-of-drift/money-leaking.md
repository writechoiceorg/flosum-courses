---
description: >-
  The five places the Salesforce DevOps mismatch turns into a measurable cost:
  time, headcount, and risk.
---

# Where the Money Leaks

The seven mismatches in Lesson 3 each have a business consequence. Most of them are invisible until they compound. This lesson makes them visible, not as abstract risk, but as five specific cost categories that every Salesforce team pays, whether or not they've named them.

None of these costs show up on an invoice. They show up in a release that takes three days instead of one. In a rollback that ties up two engineers for half a week. In an audit finding that requires external consultants to remediate. In a senior developer who leaves because they're exhausted.

The five categories:

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><strong>Failed deployments</strong></td><td>Engineering time, business impact, and schedule cascade from every deployment that breaks production.</td><td><a href="money-leaking.md#id-1.-failed-deployments">#id-1.-failed-deployments</a></td></tr><tr><td><strong>Rollback hours</strong></td><td>Manual reconstruction of prior org state: 4 to 8 hours of senior engineering time, in production, every time.</td><td><a href="money-leaking.md#id-2.-rollback-hours">#id-2.-rollback-hours</a></td></tr><tr><td><strong>Release delays</strong></td><td>Slower cadence, bigger batches, compounding risk, the chronic version of the deployment failure cost.</td><td><a href="money-leaking.md#id-3.-release-delays">#id-3.-release-delays</a></td></tr><tr><td><strong>Audit risk</strong></td><td>A 6.5-year gap between what Salesforce logs natively and what compliance frameworks require.</td><td><a href="money-leaking.md#id-4.-audit-risk">#id-4.-audit-risk</a></td></tr><tr><td><strong>Developer burnout</strong></td><td>The cost of asking experienced engineers to absorb architectural friction indefinitely through manual effort.</td><td><a href="money-leaking.md#id-5.-developer-burnout">#id-5.-developer-burnout</a></td></tr></tbody></table>

## 1. Failed deployments

Every deployment that fails in production carries a cost beyond the immediate fix. There's the engineering time to diagnose the failure and reconstruct what went wrong. There's the business impact while the system is degraded or the feature is unavailable. And there's the downstream cost to the release schedule: everything behind the failed deployment is now delayed.

Eighty-two percent of Salesforce teams report experiencing deployment delays regularly (Gearset State of Salesforce DevOps, 2025; vendor-published survey, self-selected respondents; treat as directional). The average delay across those teams adds up to roughly 3.8 months of lost release capacity per year.

That's not a minor inefficiency. That's a significant fraction of annual delivery capacity spent on failure, recovery, and rescheduling.

## 2. Rollback hours

When a deployment breaks something in production, the team needs to restore the prior state. In a standard software environment, this is a version control operation: minutes. In Salesforce, it's manual reconstruction: identifying what changed, building a reverse deployment, validating it in a sandbox first, then deploying to production while the business waits.

This process typically takes four to eight hours. It requires senior engineering attention. And it happens in production, meaning real users are affected for the duration.

The cost isn't just the hours. It's the disruption: the engineers pulled off their current work, the incidents triggered in downstream systems, the communication overhead with stakeholders who want updates every thirty minutes.

## 3. Release delays

Deployment failures are the acute version of this cost. Release delays are the chronic one.

Teams that can't trust their pipeline slow down. They add manual review steps. They schedule releases less frequently to reduce the blast radius if something goes wrong. They build larger releases because "it's not worth the coordination overhead for a small change," which makes each release riskier, which justifies slowing down further.

This cycle is self-reinforcing. The direct cost is reduced delivery velocity. The indirect cost is opportunity: features delayed, business value deferred, competitive response slowed.

## 4. Audit risk

Salesforce's Setup Audit Trail retains 180 days of configuration history. Most compliance frameworks (SOX, HIPAA, GDPR) require records retention for five to seven years. That's a gap of roughly 6.5 years between what Salesforce logs natively and what an auditor will ask for.

Filling that gap manually means exporting audit data regularly, storing it in a separate system, maintaining that system, and being able to produce it under legal hold conditions. Teams that haven't built that process before an audit are exposed.

The cost of the gap isn't just the compliance consulting bill when it surfaces. It's the ongoing cost of operating without an adequate compliance posture, and the potential liability if an incident occurs during that window.

## 5. Developer burnout

This is the hardest to put a number on and the most expensive in the long run.

Experienced Salesforce developers are scarce and expensive. When their time is consumed by manual conflict resolution, sandbox management, emergency rollbacks, and deployment investigation, they're not doing the work they were hired for. Over time, the friction accumulates. The best engineers, the ones with options, leave.

Replacing a senior Salesforce developer costs a minimum of one to two times their annual salary in recruiting, onboarding, and ramp-up time. More importantly, the knowledge they carry about the org, the release process, and the history of past failures leaves with them.

Burnout isn't a people problem. It's what happens when a team is asked to absorb the cost of an architectural mismatch indefinitely through manual effort.

## The combined picture

Each of these five costs seems manageable in isolation. Teams rationalize them: "deployments fail sometimes," "rollbacks are just part of the job," "we always miss the deadline a bit." The problem is that they don't occur in isolation. They compound.

A team experiencing all five costs (release delay tax, rollback overhead, compliance exposure, and senior engineer burnout) is operating at a significant structural disadvantage. The rest of this lesson translates each category into a number you can put in front of a CFO.
