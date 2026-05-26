---
description: >-
  Profiles, permission sets, and other metadata types produce semantically
  invalid results from a Git-valid merge.
---

# Mismatch 3: Merged Doesn't Mean Works

A valid Git merge is a syntactic operation. It produces a result that satisfies the merge algorithm: no overlapping byte regions, no unresolved conflict markers. The algorithm makes no assertion about whether the merged content is semantically correct for the target system.

In Salesforce, a successful Git merge can produce a change set that Salesforce rejects at deployment, or accepts at deployment but fails at runtime. The two systems are measuring different properties.

## Root cause: semantic dependencies Git can't model

Git's merge algorithm operates on file content. It tracks which bytes changed in which branch and combines them according to a defined strategy. It has no model of types, references, or constraints.

Salesforce metadata components carry implicit semantic dependencies that are invisible to any content-level tool:

* A **Profile** references objects, fields, Apex classes, and Visualforce pages, each of which must exist in the target org at deployment time.
* A **Permission Set** has referential dependencies on every object and field it grants access to. Granting access to a field that doesn't exist in the target org is a deployment error.
* A **Flow** references record types, object fields, and Apex methods by API name. API names are org-specific and may differ between sandboxes.
* An **Apex class** compiles against the org's schema at compile time. A class that references a field or object that was removed after the class was last compiled will fail at runtime.

When two branches independently modify any of these components, Git can merge the XML files without raising a conflict. The resulting document is syntactically valid XML. It may be semantically broken.

## Profiles and Permission Sets: the canonical case

Profiles and Permission Sets are sometimes called "god-objects" in the Salesforce architecture community: shared containers that aggregate every access and permission decision in the org. Every new custom object, every new field, every new Apex class adds a potential entry. In a multi-team org, nearly every piece of work simultaneously modifies the same files. The merge collision isn't an edge case; it's the normal state of any active development pipeline.

Profiles illustrate the failure mode most clearly because their retrieval behavior is partial by design.

The Metadata API only returns permissions for objects and fields that are explicitly listed in the package manifest of the retrieve request. Permissions for objects not in the manifest are not returned. They are not serialized as `false`; they are simply absent from the XML. This creates a structural asymmetry:

{% stepper %}
{% step %}
### Developer A retrieves

Developer A retrieves the Profile in Sandbox A, which includes Objects X, Y, Z. The Profile XML contains permission entries for X, Y, Z.
{% endstep %}

{% step %}
### Developer B retrieves

Developer B retrieves the Profile in Sandbox B, which includes Objects X, Y, W (a new object B is building). The Profile XML contains permission entries for X, Y, W.
{% endstep %}

{% step %}
### Git merges

Git merges the two files. The merged result contains entries for X, Y, Z, and W. No conflict was raised. The merge is syntactically clean.
{% endstep %}

{% step %}
### Target org mismatch

The target org has Objects X and Y only. Z was removed in a prior sprint; W has not been deployed yet.
{% endstep %}

{% step %}
### Deployment fails

Salesforce rejects the deployment: referenced objects don't exist. No Git conflict was raised. The merge was clean. The failure is semantic, not syntactic.
{% endstep %}
{% endstepper %}

No Git conflict was raised. The merge was syntactically clean. The deployment fails on semantic validation.

## Non-deterministic serialization amplifies the problem

The Metadata API does not guarantee consistent XML element ordering across retrievals. The same Profile retrieved from the same org on two different days may serialize its field permissions in a different order. This causes Git to generate false conflicts: changes that appear to conflict because the surrounding context lines differ, not because any permission actually changed.

False conflicts require manual resolution. Each manual resolution is an opportunity to introduce a real error. Teams that deploy Profiles frequently report that Profile conflicts are among the most common causes of blocked releases, and that the volume of false conflicts creates pressure to exclude Profiles from version control entirely, which creates a different set of governance problems.

{% hint style="warning" %}
Excluding metadata types from version control to avoid merge conflicts is a common workaround. It trades one problem (merge failures) for another (unversioned, unreviewed changes). Neither approach addresses the root cause: Git is operating below the semantic layer Salesforce requires.
{% endhint %}

## Solution category

The fix is a merge engine that operates at the semantic level, one that understands that two Profile changes both granting access to a field produce a single `true` entry, not a conflict, and that a Permission Set referencing a non-existent field should fail validation before deployment rather than at deployment.

_The analogy that makes this concrete: merging two house blueprints line by line. Each architect's changes merge cleanly. The combined blueprint has two front doors and no kitchen, syntactically valid but architecturally incoherent._
