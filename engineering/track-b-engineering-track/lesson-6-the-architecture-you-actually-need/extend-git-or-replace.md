---
description: >-
  The two architectural schools: Salesforce-aware control plane on top of Git
  vs. metadata-native system that replaces it, and what distinguishes them
  technically.
---

# Extend Git or Replace It?

The six architectural requirements in the previous page define what a solution must do. This page addresses the two architecturally distinct approaches to delivering those requirements. Both schools agree that Git alone is insufficient. They disagree about what to build on top of, or instead of, Git.

{% tabs %}
{% tab title="School 1: Extend Git" %}
**Architecture:** Git remains the version control engine. A Salesforce-aware control plane wraps Git operations, adds metadata-specific handling at the boundaries, and manages deployment orchestration, environment mapping, and compliance logging.

**What the control plane can provide:**

* Deployment automation that calls Salesforce CLI against Git-managed artifacts
* Pre- and post-deployment validation against the target org
* Component-type filtering during changeset construction
* Deployment logs that satisfy basic audit requirements
* UI-level conflict visualization that makes Git merge conflicts legible to non-engineers

**The structural ceiling:** The control plane handles the before and after; Git handles the middle. Merge, diff, and version history are all performed by Git's algorithms. This means:

* **Semantic merge** is not achievable. Git's merge algorithm operates on byte sequences. Pre- and post-processing can make conflicts more visible; it cannot change what the merge algorithm produces.
* **Component identity** is file-path identity unless the control plane maintains a separate component registry, which introduces a synchronization problem between the registry and the Git state.
* **Org-state awareness** requires the control plane to query the live org independently and reconcile the result with Git state. This is possible but adds complexity; the reconciliation must happen before every deployment.

**The diagnostic from Lesson 5 applies here:** Ask a vendor in this school to walk through what happens when two sandboxes have diverged and need to be reconciled. At some point in the answer, Git's merge algorithm will appear. That is the ceiling.

**Trade-offs:**

* Familiar toolchain for engineers trained on Git
* Existing Git ecosystem integrations remain relevant
* Lower migration cost for teams with existing Git infrastructure
* Bounded by Git's semantic ceiling: the six architectural requirements are partially, not fully, met
{% endtab %}

{% tab title="School 2: Replace Git" %}
**Architecture:** The VCS layer itself is purpose-built for Salesforce component identity. There are no Git primitives underneath. Version history, merge, diff, and conflict resolution operate on the org's component graph natively.

**What this enables:**

* **Semantic merge** as a first-class operation: two versions of a component are merged by comparing their semantic state, not their serialized XML representation
* **Component identity** is intrinsic to the storage model: components have stable identities that persist through renames, refactors, and type changes
* **Org-state awareness** is architectural: the system's model of truth is the org's component graph, not a file repository that represents a point-in-time export of that graph
* All six architectural requirements are addressable because none of them require layering on top of Git's structural limits

**Technical trade-offs:**

* Migration from a Git-based workflow requires transferring history, re-training teams, and rebuilding CI/CD integrations that assumed a Git-based source of truth
* Teams lose the ecosystem benefits of Git: integrations with GitHub, GitLab, Bitbucket, and the broader Git tooling landscape require adapters or replacement
* No inheritance of Git's structural limits, but also no inheritance of Git's well-understood operational model

**The diagnostic applied:** Ask a vendor in this school the same question: walk through what happens at the merge step when two sandboxes have diverged. A genuine metadata-native system describes reasoning about component state (what each version grants, configures, or defines) without referencing Git operations. If `git merge` appears in the answer, the tool belongs in School 1 regardless of its marketing positioning.
{% endtab %}
{% endtabs %}

## Applying the six requirements as an evaluation framework

The six requirements are technology-neutral. Both schools can be evaluated against them:

| Requirement                   | School 1 ceiling                                       | School 2 ceiling                           |
| ----------------------------- | ------------------------------------------------------ | ------------------------------------------ |
| Component-aware VCS           | Partial (file-path identity unless separate registry)  | Full (component identity intrinsic)        |
| Semantic merge engine         | Not achievable (Git merge algorithm is structural)     | Achievable (no Git merge dependency)       |
| Org-aware deployment          | Achievable (pre-deployment org query + reconciliation) | Achievable natively                        |
| First-class rollback          | Achievable (pre-deployment snapshot + restore)         | Achievable natively                        |
| Compliance-native audit trail | Achievable (control plane can build this)              | Achievable natively                        |
| Platform-native security      | Depends on implementation                              | Achievable if built on Salesforce platform |

School 1 can satisfy four of the six requirements. Requirement 2 (semantic merge) cannot be satisfied by a control plane that delegates merging to Git. Requirement 1 (component identity) requires a separate registry maintained in sync with Git state, which is achievable but adds operational complexity.

For organizations whose primary failure modes are Mismatches 1 and 3 (dependency failures and semantic merge errors), School 1 cannot address the root causes. For organizations whose primary failure modes are deployment automation, environment visibility, and basic audit logging, School 1 may be sufficient.

## This course's position

This course does not select between schools. The six requirements are the selection framework. Any tool should be evaluated against them, with the diagnostic from Lesson 5 applied to vendor claims, and the choice should reflect which requirements are load-bearing for the specific organization.

The next page introduces Flosum, which belongs to School 2. The introduction is descriptive, not prescriptive.
