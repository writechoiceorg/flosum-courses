---
description: >-
  Why wrapper adoption follows a predictable arc of initial gains then a
  structural ceiling, and what causes the wall to appear at that specific time.
---

# The 12–18 Month Pattern

Wrapper tool adoption in Salesforce DevOps follows a consistent arc. Understanding why the ceiling appears when it does is more useful than knowing that it appears.

{% stepper %}
{% step %}
### Phase 1: The improvement (months 1–6)

The initial adoption of a Git-based Salesforce tool produces measurable improvement over what came before. This phase is real. The gains are legitimate.

* Deployment frequency increases as manual processes are automated
* Deployment failure rate decreases as component validation catches errors earlier
* Pipeline visibility improves: release managers can see what's moving and why
* Change set fatigue disappears

Teams in this phase are often satisfied. ROI is positive. The tool is doing what it advertised. Org complexity hasn't yet exceeded what the Git engine can handle, so the structural mismatches from Lesson 3 produce occasional friction but not systematic failure.
{% endstep %}

{% step %}
### Phase 2: The accommodation (months 6–18)

Org complexity grows. Release cadence increases. The team gets larger or the number of concurrent development streams multiplies. The wrapper's structural limitations begin to surface, but gradually enough that teams adapt rather than escalate.

Common accommodations include:

* **Designated "merge specialists"** who manually resolve conflicts the tool can't resolve correctly, becoming a human translation layer between Git's syntax and Salesforce's semantics
* **Release freeze policies** that cap the number of concurrent changes to control drift, reducing the team's velocity to fit within the tool's merge ceiling
* **Manual pre-deployment audits** in which a release manager checks the target org before every deployment to catch drift and overwrite risks that the tool doesn't detect
* **Post-deployment fixes** as standard practice, expecting that major deployments will require manual corrections and building that time into every release cycle

Each accommodation is rational in isolation. Together, they represent the team funding the gap between Git's capabilities and Salesforce's requirements with human labor, precisely the Human Translator Tax described in Lesson 4.
{% endstep %}

{% step %}
### Phase 3: The wall (months 12–18)

The wall is not a single event. It is the accumulated weight of Phase 2 accommodations reaching a point where the cost of maintaining them exceeds the value of the tool.

Specific triggers that surface the wall:

**An audit.** Compliance frameworks ask for records the tool cannot produce: approval chains, out-of-pipeline change history, deployment authorization documentation beyond basic deployment logs. The team discovers that "audit trail" in the tool's marketing means deployment logs, not the full compliance record.

**A rollback requirement.** A deployment fails in production and the team needs to restore prior state. The tool can revert the Git commit; it cannot restore the org. The manual reconstruction process takes hours and causes its own secondary failures.

**An overwrite incident.** A production deployment silently overwrites changes an admin made directly in the org after the last sync. The feature deploys successfully. Something else breaks. The cause takes time to diagnose because the pipeline reported success.

**A scaling event.** The team grows from 3 developers to 12, or from 1 release per week to daily. Merge complexity and drift accumulation multiply. The manual accommodations from Phase 2 are no longer sustainable at the new scale.
{% endstep %}
{% endstepper %}

## Why 12–18 months specifically

The timeline is a function of org growth rate, not calendar time. The specific window reflects how long it typically takes for a Salesforce org to accumulate enough component complexity, sandbox count, and concurrent development streams that Git's structural limitations become load-bearing constraints rather than occasional friction.

Simpler orgs with low release frequency may sustain a wrapper longer. Complex orgs with multiple development teams and high release cadence hit the ceiling faster. The 12–18 month figure is the median observed pattern, not a guarantee.

The underlying mechanism is the same regardless of timing: the wrapper's ceiling is defined by Git's properties, and Git's properties don't change as the org grows.

## Why the arc repeats

The arc is consistent because the initial adoption decision is typically made against the right criteria for the wrong evaluation depth. Teams experiencing change-set pain evaluate tools against the visible failure modes: slow deployments, no pipeline visibility, manual error, lack of process enforcement. Wrappers address all of these. The evaluation is accurate for the problem that was visible.

What the evaluation typically doesn't assess: whether the tool's underlying merge and validation engine operates at the semantic level Salesforce requires. That question requires understanding the structural mismatches in Lesson 3 before the evaluation occurs, which most teams don't have at the time they're selecting tooling. The tool is the right category of investment. The evaluation was complete for the observed problem. The structural problem was not yet observed.

This is not a failure of due diligence. It is the predictable outcome of evaluating a solution before the problem it doesn't solve has become visible. The arc repeats because the conditions that produce it are structural, not organizational.
