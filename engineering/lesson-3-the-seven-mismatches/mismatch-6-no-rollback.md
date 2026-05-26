---
description: >-
  Salesforce deployments are not reversible transactions. Rollback means
  reconstructing prior state by hand, in production, while the business waits.
---

# Mismatch 6: No Rollback

In Git, reverting a deployment is a first-class operation: `git revert` creates a new commit that inverts a previous one. The operation is cheap, safe, and history-preserving. The assumption underlying this is that the environment is reproducible: applying a known sequence of commits from any baseline will produce a deterministic result.

Salesforce deployments are not reversible transactions. There is no revert primitive. The platform has no concept of "restore to the state before this deployment." Rollback means deploying a new change that happens to reconstruct the prior configuration, subject to all the same validation rules, test requirements, and failure modes as any other deployment.

## What reconstruction actually requires

To restore a Salesforce org to a prior state after a failed or damaging deployment, a team must:

**1. Identify what changed.** This requires either a backup of the prior component state or the ability to retrieve what was in the org before the deployment and compare it to what's there now. If the repository accurately reflected org state before the deployment, this is tractable. If the repository was drifted (Mismatch 2), it may not be.

**2. Assemble a reverse change set.** This means retrieving the prior versions of affected components from a backup, a sandbox, or the repository, building a new deployment package, and ensuring that the reverse deployment is itself a valid change set. A component that was valid in the prior state may not be deployable as a reversal: if other components have since been deployed that depend on the current state, reverting creates new dependency violations.

**3. Deploy the reconstruction.** The reverse deployment is subject to the same transaction semantics as any other deployment (Mismatch 5): dependency resolution, constraint validation, Apex test execution. It can fail. If it fails, the org remains in the partially-corrected state.

**4. Validate the result.** Restoring metadata to a prior configuration does not restore runtime state: data that was created or modified under the deployed configuration persists. Workflow rules that executed during the deployment window have already run. Events that fired are gone.

**5. Assess irreversibility.** Some deployment changes have no recovery path at all. When data has already moved against a new configuration (records written under a required field, transactions processed through a changed flow, references accumulated against a new relationship type) there is no clean prior state to restore. For certain operations, the platform cannot reverse them by design: converting a lookup field to a master-detail on an object with existing records, removing a picklist value that active records reference, deleting a field that has dependent workflow rules. The Metadata API can redeploy the prior configuration, but the data that accumulated during the deployment window cannot be automatically unwound. For these operations, the reconstruction path doesn't just take hours. It may not exist.

## The hours and risk cost

The practical cost of a production rollback in Salesforce is not the deployment time. It's everything surrounding it. An engineer must:

* Identify the affected components and locate the prior versions (30–90 minutes if the repository is well-maintained; longer if it isn't).
* Build and validate the reverse change set in a sandbox before deploying to production (1–3 hours, including Apex test execution time).
* Deploy to production and verify (30–90 minutes).
* Communicate with the business during the recovery window, when every user on the platform is potentially affected.

Total recovery time for a non-trivial rollback is commonly 4–8 hours. For orgs with large test suites or complex dependency graphs, it can be longer.

{% hint style="warning" %}
There is no safe moment to do a production rollback in a Salesforce org that's in active use. Users, integrations, and automated processes continue running against the broken state during the recovery window. The longer reconstruction takes, the more data and process state has accumulated on top of the configuration that needs to be reversed.
{% endhint %}

## What prevention requires

Because rollback is expensive and unreliable, the effective risk mitigation strategy is reducing the probability that a bad deployment reaches production. This requires pre-deployment validation against the actual target org state, not sandbox state, and first-class backup capabilities that can produce an authoritative snapshot of org configuration before any deployment begins.

A tooling approach that treats backup and rollback as primitives (not procedures) changes the recovery model from "reconstruct manually while the business waits" to "restore from a known-good snapshot while the business waits less."

_The analogy that makes this concrete: Git is Control-Z. Salesforce is "rebuild the sandcastle from memory," while the tide is coming in._
