---
description: >-
  Org state and repo state diverge constantly. Git has no mechanism to reconcile
  them. Drift born here compounds every other mismatch.
---

# Mismatch 2: Org as Source of Truth

In standard software development, the repository is authoritative. What's in the repository is what's deployed, what's tested, and what's recoverable. The running environment is an instance of what the repository describes.

In Salesforce, this relationship is inverted. The live org is authoritative. The repository is a partial, point-in-time snapshot of some of what's in the org, captured whenever a retrieve was last run. The gap between the two is continuous, invisible, and compounding.

## Why the inversion is structural

The Metadata API operates as a retrieve-then-commit workflow: to track a change in the repository, you must first retrieve the current state of the component from the org, then commit the retrieved XML. The repository is always downstream of the org: it reflects a past state, not the current one.

This would be manageable if the org only changed through the pipeline. It doesn't. The Metadata API has no push notifications, no change feed, no mechanism for broadcasting changes to any external system. Admins configure components directly in production. Developers modify sandboxes interactively. Every change that happens outside the retrieve-commit cycle creates immediate drift, silently.

## Annotated scenario: drift across a sprint cycle

**Day 1.** Developer A creates a sandbox from a production refresh. Sandbox is consistent with production. Developer A begins a feature branch.

**Day 2.** Admin B, responding to a sales team request, modifies the Opportunity page layout directly in production. No ticket. No Git commit. The change is live in production and nowhere else.

**Day 4.** Developer C pushes an Apex trigger to the `develop` branch. Developer C's sandbox was created two weeks ago. It does not reflect Admin B's page layout change, and Developer C doesn't know Admin B's change exists.

**Day 8.** Developer A completes the feature. Pull request is reviewed, approved, merged. The pipeline deploys to QA → staging → production, pushing the page layout version from the repository. Admin B's change is overwritten with the older version that was in Git.

Admin B's change is silently lost. No error. No conflict. The pipeline behaved correctly with respect to what was in the repository. The repository was wrong.

This is code clobber, the canonical Salesforce DevOps failure mode. It's not a discipline failure; Admin B was responding to a legitimate priority. It's a structural consequence of operating two independent state stores with no reconciliation layer.

## Drift compounds across mismatches

Drift doesn't accumulate at a constant rate; it accelerates. Every week of divergence widens the gap between what the pipeline knows and what the org contains. The downstream effects:

* **Mismatch 3:** Merge conflicts between branches are based on stale sandbox state. The merged result may be invalid against the actual production org state neither sandbox reflects.
* **Mismatch 4:** Branch-environment mappings become increasingly fictional as the gap between branch state and sandbox state grows.
* **Mismatch 6:** Rollback requires reconstructing "prior state." What is the prior state when the repository doesn't accurately reflect what was in the org before the last deployment?
* **Mismatch 7:** Out-of-pipeline changes are definitionally invisible to any audit trail that relies on the repository as its record.

## The Release Manager as architectural symptom

The existence of the Release Manager role in Git-based Salesforce shops is a direct structural symptom of this mismatch. The job description, reconciling what's in Git with what's actually in the org before each release, is the manual implementation of a reconciliation layer the tooling doesn't provide.

{% hint style="info" %}
The cost of the Release Manager role is not primarily salary. It's the bottleneck it creates: changes queuing behind a single reconciliation point, release cadence limited by manual verification capacity, and the cognitive load of maintaining a mental model of org state that no tool tracks reliably.
{% endhint %}

_The analogy that maps precisely: keeping a shared document in sync by emailing copies around. Every recipient has a version. Nobody has the version._
