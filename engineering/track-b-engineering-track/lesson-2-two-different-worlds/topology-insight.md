---
description: >-
  Branches descend from main; sandboxes descend from production: the inversion
  and its technical consequences.
---

# The Topology Insight

There is a structural difference between Git and Salesforce that is easy to overlook and difficult to overstate.

**In Git**, branches descend from main. The default topology is a trunk-and-branches model: production is at the tip of the main branch; feature development happens in branches that fork from it and eventually merge back. The canonical baseline that everyone works from is maintained in the central repository.

**In Salesforce**, sandboxes descend from production. When you create a sandbox, you're taking a point-in-time copy of the production org. Production is the parent; everything else is a derivative. You cannot provision a Salesforce environment from a repository the way you can clone a Git repo.

The shapes are inverted.

## Why the inversion matters technically

Git's entire toolchain (branching strategies, merge workflows, CI/CD pipelines) is built on the assumption that the canonical baseline lives in the repository, and environments are derived from it on demand.

In Salesforce:

* **Production is the canonical baseline**, not the repository. The repo is an approximation that diverges from the org the moment any change is made outside the pipeline.
* **Sandboxes drift from creation.** A sandbox is a snapshot of production at a point in time. It is not a reproducible, ephemeral environment. From the moment it's created, it accumulates changes that distinguish it from both production and other sandboxes.
* **There is no "deploy from main."** You deploy _to_ production. Production is already the authoritative state; the deployment is an attempt to push a set of changes into it from a lower environment.
* **Rollback has no native implementation.** In Git, reverting means resetting the branch. In Salesforce, reverting means restoring the org to a prior state, which requires knowing precisely what that state was, constructing the inverse change set, and deploying it under live-system constraints.

## The workflow collision

When you impose a Git-style workflow on Salesforce's topology, you introduce a persistent misalignment: the workflow treats the repository as authoritative, but the org is authoritative. Every time these two diverge, which happens continuously in any active org, the workflow produces incorrect or incomplete results.

This misalignment explains several failure modes that otherwise appear unrelated:

* Why "it's in the repo" and "it's in the org" are not the same claim
* Why sandbox environments are unreliable test beds even when freshly refreshed
* Why rollback procedures that work in standard deployment pipelines don't exist natively in Salesforce
* Why drift accumulates even on teams with disciplined change management processes

The topology inversion cannot be resolved by process discipline or tooling configuration. Any DevOps approach for Salesforce must account for the fact that production is the source of truth, not the repository.
