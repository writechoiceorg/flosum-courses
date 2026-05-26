---
description: >-
  Component-aware version control, semantic merge engine, org-aware deployment,
  first-class backup and rollback, compliance-ready audit trail, platform-native
  security model.
---

# Metadata-Native Architecture

The mismatches in Lesson 3 are not a list of bugs. They are properties of the Git engine applied to a domain it wasn't designed for. Each mismatch has an architectural resolution: a system capability that addresses the root cause rather than managing the symptom. This page defines those six capabilities precisely.

<table data-view="cards"><thead><tr><th></th><th></th></tr></thead><tbody><tr><td><strong>Component-aware version control</strong></td><td>Metadata stored as typed objects with identity and relationship graphs, not serialized XML files indexed by path.</td></tr><tr><td><strong>Semantic merge engine</strong></td><td>Merge performed by comparing component state semantically: resolves permission set conflicts by grant semantics, not XML structure.</td></tr><tr><td><strong>Org-aware deployment</strong></td><td>Pre-deployment org state query, overwrite detection, validation against live dependency graph before any write to the org.</td></tr><tr><td><strong>First-class backup and rollback</strong></td><td>Full org snapshot before each deployment. Restore is a first-class operation with predictable completion time, not manual reconstruction.</td></tr><tr><td><strong>Compliance-native audit trail</strong></td><td>Approval chain, deployment authorization, out-of-pipeline change detection. Retention configurable to SOX/HIPAA/GDPR requirements.</td></tr><tr><td><strong>Platform-native security model</strong></td><td>Data residency and access control via Salesforce's own security model: permissions, revocations, and data residency governed by the platform.</td></tr></tbody></table>

## Requirement 1: Component-aware version control

**Resolves: Mismatch 1 (Metadata interdependency)**

A component-aware VCS stores metadata as typed objects with stable identity, known type schemas, and explicit relationship graphs, not as serialized XML files indexed by path.

The operational distinction: when a developer queries "which components reference this field?" the system traverses the component relationship graph. The answer is authoritative because the graph is maintained as a first-class data structure, not inferred by scanning file contents for string matches. Dependency resolution at deployment time uses the same graph, eliminating the class of reference-breakage failures that file-based systems cannot detect before production.

Component identity is stable across renames and refactors. Version history is a history of component state transitions, not a history of file diffs.

## Requirement 2: Semantic merge engine

**Resolves: Mismatch 3 (Merged ≠ Works)**

A semantic merge engine compares component state as structured objects and resolves conflicts by understanding what the metadata represents, not by comparing byte sequences.

The canonical example: two developers modify the same `PermissionSet`. Developer A adds a field-level security grant for `Opportunity.Amount`. Developer B adds an object permission for `Account`. In Git, this is a merge of two XML files. The syntactic merge produces valid XML that may grant or deny access incorrectly depending on the merge algorithm's handling of sibling elements. In a semantic merge, the engine understands that both changes are additive grants to the same permission set, resolves them by producing a combined state that includes both grants, and validates that the result is a coherent permission set.

Semantic merge does not produce merge conflicts for changes that are semantically non-overlapping. It produces conflicts only when two changes genuinely cannot be combined without a human decision, which is the only case where human review is actually required.

## Requirement 3: Org-aware deployment

**Resolves: Mismatch 2 (Org as source of truth), Mismatch 7 (Audit gap)**

Org-aware deployment queries the target org's current metadata state before writing any changes. The query produces a diff between the org's actual current state and the last known state in the deployment system. If in-org changes have occurred since the last sync (admin modifications, declarative configuration changes, emergency patches), the system surfaces them before deployment.

The deployment decision is then made with full information: not just "what does the repo say to deploy" but "what would this deployment overwrite, and is that intended?"

Overwrite protection is the operational manifestation of this requirement. A deployment that would silently revert in-org changes is either blocked or flagged for explicit confirmation. This check is not optional: deploying without it is structurally unsafe in any org with admin access.

## Requirement 4: First-class backup and rollback

**Resolves: Mismatch 6 (No native rollback)**

First-class rollback requires two capabilities: a complete org snapshot captured before each deployment, and a restore operation that applies that snapshot to return the org to its prior state.

Both must be platform primitives, not procedures. A "backup" that requires manual export and reconstruction is not first-class rollback; it is a workaround that extends MTTR to hours. A first-class restore operation has a defined execution path, a predictable completion time measured in minutes, and does not require senior engineering judgment to execute.

The technical requirement for the snapshot: it must capture not just the changeset being deployed but the complete org state: all metadata components, their current values, and their relationship graph. A partial snapshot that captures only the deployment diff cannot support full rollback.

## Requirement 5: Compliance-native audit trail

**Resolves: Mismatch 7 (Audit gap)**

A compliance-native audit trail answers the four audit questions natively: what changed, when, who authorized it, and through what approval process. It also captures out-of-pipeline changes (org modifications that occurred outside the deployment system) and supports configurable long-term retention.

The technical requirements:

* **Approval chain capture:** Each deployment record includes the full approval chain: who requested it, who approved each stage, timestamp and identity at each step
* **Out-of-pipeline change detection:** The system detects when the org's state diverges from the last known deployment state and records the divergence, even if the change was made directly in the org, outside any deployment workflow
* **Configurable retention:** Retention period is configurable independently of Salesforce's 180-day Setup Audit Trail limit; the compliance system maintains its own retention store

The audit trail is not a deployment log with extra fields. It is a purpose-built compliance record designed to satisfy the specific questions auditors ask under SOX, HIPAA, and GDPR.

## Requirement 6: Platform-native security model

**Architectural property: not a mismatch resolution**

Platform-native security means the DevOps system's access control, permissions model, and data residency operate within Salesforce's own security architecture, not as a separate external system that Salesforce authenticates against.

The technical implications:

* **Permission management** uses Salesforce profiles, permission sets, and sharing rules, the same constructs that govern org access. A permission granted in the org is honored by the DevOps system without a separate grant in an external auth layer.
* **Data residency** is governed by the Salesforce platform's data residency guarantees. Metadata artifacts, deployment logs, and audit records do not transit to external storage unless explicitly configured.
* **Revocation** is immediate: removing a user's org access removes their DevOps access by the same mechanism, with no propagation delay to a third-party auth system.

This is both a security property and an operational simplification. The DevOps system's access model is not a separate administrative domain to maintain.

## Mismatch resolution summary

| Requirement                   | Mismatch resolved                                                            |
| ----------------------------- | ---------------------------------------------------------------------------- |
| Component-aware VCS           | Mismatch 1: Metadata interdependency                                         |
| Semantic merge engine         | Mismatch 3: Merged ≠ Works                                                   |
| Org-aware deployment          | Mismatch 2: Org as source of truth; Mismatch 5: Deployments are transactions |
| First-class backup/rollback   | Mismatch 6: No native rollback                                               |
| Compliance-native audit trail | Mismatch 7: Audit gap                                                        |
| Platform-native security      | Architectural property across all mismatches                                 |

Mismatch 4 (Branching breaks at org boundary) is addressed architecturally by the combination of org-aware deployment and continuous org sync. The system tracks org state continuously rather than treating org state as something a branch maps to.
