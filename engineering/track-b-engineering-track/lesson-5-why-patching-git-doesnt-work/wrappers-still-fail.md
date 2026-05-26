---
description: >-
  Why the Salesforce-aware layer doesn't resolve the structural mismatches: the
  underlying engine is still wrong.
---

# Where Wrappers Still Fail

The Git-native Salesforce tools described in the previous page provide a meaningful layer of Salesforce-specific handling. They are categorically better than raw Git or general-purpose pipelines for Salesforce operations. And they still fail at the same structural boundary.

The reason is that the Salesforce-aware layer sits on top of the Git engine; it doesn't replace it. Merge, diff, version history, and conflict resolution are all still performed by Git. The wrapper handles the before and after; Git handles the middle. And the middle is where Salesforce's mismatches live.

## The syntactic vs. semantic gap

Git's merge algorithm operates on content: it compares two versions of a file as byte sequences and produces a merged output. This is syntactic merging. It produces valid text. It has no awareness of what that text represents.

Salesforce metadata merging requires semantic understanding. Consider `PermissionSet.permissionset-meta.xml`:

* Two developers modify the same permission set in different sandboxes
* Git performs a syntactic merge of the XML files
* The output is valid XML (no merge conflict was flagged)
* The resulting permission set grants elevated access that neither developer intended
* The permission set deploys successfully to production
* The security configuration is now incorrect

Git did exactly what it was designed to do. The merge was technically valid. The semantic result was wrong. No wrapper that delegates merging to Git can prevent this outcome, because the prevention requires understanding what the metadata represents, which Git does not.

The same gap applies across component types: flows, profiles, custom objects with complex field configurations, sharing rules. Each has semantic dependencies that XML comparison cannot model.

## The inherited mismatches

Each mismatch from Lesson 3 has a specific relationship to the wrapper's capabilities:

| Mismatch                         | What the wrapper provides                                   | What remains unresolved                                                                 |
| -------------------------------- | ----------------------------------------------------------- | --------------------------------------------------------------------------------------- |
| Metadata interdependency         | Component selection helpers, basic dependency detection     | Silent reference breakage at deploy time for undetected dependencies                    |
| Org as source of truth           | Repo-centric workflow, deploy-from-branch                   | No continuous sync from org to repo; out-of-band admin changes remain invisible         |
| Merged ≠ works                   | Pre-deployment validation against a target org (some tools) | Semantic merge correctness; valid XML ≠ valid metadata                                  |
| Branching breaks at org boundary | Environment-mapped branch model                             | Sandbox refresh cycles, drift accumulation, and refresh costs are unchanged             |
| Deployments are transactions     | Deployment scheduling, retry logic                          | Transaction semantics unchanged; rollback still unavailable                             |
| No native rollback               | Backup snapshots (some tools)                               | Org-state reconstruction is still manual or incomplete                                  |
| Audit gap                        | Basic deployment logs                                       | Approval chain depth, out-of-pipeline change tracking, long-term retention requirements |

The wrapper fills in some gaps. The structural ones remain.

## The steering wheel cover

The analogy from the outline is precisely right: a leather steering wheel cover on a car that has no brakes.

The cover is not a trivial improvement. A good grip matters. Comfort reduces fatigue. The car is genuinely better to drive. But the improvement is in the interface, not the safety system. When conditions require braking, the cover is irrelevant.

Wrapper tools improve the Salesforce DevOps interface, and that improvement has real value. When conditions require semantic merge correctness, org-aware rollback, or continuous bi-directional sync between org and repo, the interface layer is irrelevant. The engine underneath determines what's possible, and the engine is still Git.
