---
description: >-
  The two categories of tooling built on top of Git for Salesforce, what each
  addresses, and where both hit structural limits.
---

# The Wrapper Landscape

The response to Git's structural mismatch with Salesforce produced two distinct categories of tooling. Understanding the difference between them (and what both have in common) is prerequisite to the diagnostic in the pages that follow.

## Category 1: Git-native Salesforce tools

The first category consists of products specifically designed for Salesforce DevOps, built on a Git foundation with a Salesforce-aware abstraction layer on top. They translate Salesforce concepts (orgs, sandboxes, change sets, metadata components) into Git concepts (branches, commits, merges, pull requests) and provide a purpose-built UI that shields Salesforce practitioners from direct Git interaction.

These tools provide genuine value:

* **Deployment orchestration:** component selection, dependency resolution at the UI layer, pre-deployment validation hooks
* **Pipeline management:** environment promotion flows mapped to org hierarchies
* **Approval workflows:** change management gates between pipeline stages
* **Surface-level metadata awareness:** component-type-specific handling, basic conflict detection

The Salesforce-specific layer is real. It reduces the failure rate compared to raw Git operations. But the underlying version control, merge, and diff operations are still performed by Git, which means the mismatches documented in Lesson 3 remain structurally present. The layer reduces their frequency; it doesn't eliminate their cause.

## Category 2: General-purpose CI/CD pipelines

The second category is broader and, for engineers, often more familiar: general-purpose automation platforms configured to execute Salesforce CLI commands as part of a deployment pipeline.

These tools are not Salesforce products. They are designed for software development generally and adapted to Salesforce through configuration. The adaptation typically involves:

* Calling `sf project deploy` or equivalent CLI commands as pipeline steps
* Managing Salesforce credentials through standard secrets management
* Triggering deployments on branch events (push, pull request merge)
* Reporting deployment results as pipeline pass/fail states

At this level of abstraction, the pipeline has no model of Salesforce at all. It executes commands and observes exit codes. The Salesforce CLI does the work; the pipeline manages the execution context.

This produces the same improvement over manual change sets that the first category provides (automation, repeatability, visibility) but without any of the Salesforce-specific handling. Three specific gaps are structurally unavoidable:

**No overwrite protection.** A Salesforce-aware deployment system validates the target org's current state before writing to it, checking whether org changes made outside the pipeline would be overwritten by the incoming deployment. A general-purpose pipeline has no mechanism to perform this check. It calls the CLI; the CLI deploys; if an admin changed something in production after the last sync, that change is silently lost.

**No Salesforce-native rollback.** A Salesforce deployment is a transaction against a live system, not a file operation. Reversing it requires reconstructing prior org state, a Salesforce-specific capability. General-purpose pipelines have no rollback primitive for this. A `git revert` restores file content; it does not restore org state.

**No semantic metadata resolution.** When merge conflicts arise, a general-purpose pipeline resolves them through Git's content comparison. It has no model of Salesforce component identity, dependency graphs, or deployment semantics. The output of a Git merge on Salesforce metadata may be syntactically valid XML that produces a semantically broken org state.

These are not deficiencies in the tools; they are consequences of applying general-purpose infrastructure to a domain-specific problem. The tools do exactly what they were designed to do. They were not designed for Salesforce.
