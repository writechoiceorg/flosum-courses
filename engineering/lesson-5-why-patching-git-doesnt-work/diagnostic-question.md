---
description: >-
  A single question that separates metadata-native tools from wrapper tools, and
  how to apply it to any vendor evaluation.
---

# The Diagnostic Question

Vendor positioning in the Salesforce DevOps space is dense with claims about "metadata-awareness," "Salesforce-native" architecture, and "intelligent deployments." Most of these claims are directionally true. Almost none of them distinguish between a tool that understands Salesforce metadata semantically and one that calls Git underneath and adds a Salesforce UX layer.

One question cuts through all of it:

{% hint style="info" %}
**Does your merge engine reason about Salesforce metadata semantically, or does it call `git merge` underneath and post-process the output?**
{% endhint %}

The answer determines whether the tool addresses the root cause or manages its symptoms.

## What semantic reasoning means

A tool that reasons about Salesforce metadata semantically treats metadata components as structured objects with known types, defined relationships, and deployment semantics, not as files containing text.

In practice, this means:

* **Merge** is performed by comparing component graphs, not XML byte sequences. Two versions of a `PermissionSet` are merged by understanding what permissions each version grants and producing a correct combined state, not by line-diffing two XML files.
* **Conflict detection** identifies semantic conflicts (two changes that would produce an invalid org state) not just syntactic ones (two changes to the same line of a file). A semantic conflict may produce no Git conflict marker at all.
* **Dependency resolution** is performed against the org's actual component graph, not inferred from the XML artifacts. The tool knows that renaming a field affects 14 other components because it understands the org's data model, not because it scanned for string matches in XML files.
* **Deployment validation** checks whether a proposed changeset is valid against the target org's current state before sending it. This includes detecting whether in-org changes would be overwritten, a check that requires querying the live org, not comparing files.

## How to apply the diagnostic

Ask the vendor directly. The response pattern is predictable:

{% tabs %}
{% tab title="Wrapper tool" %}
"We have metadata-aware conflict detection," followed by a description of XML parsing, component-type filtering, or UI-level conflict visualization. The resolution mechanism is still Git; the wrapper adds pre- and post-processing.
{% endtab %}

{% tab title="Metadata-native tool" %}
A description of the tool's own merge engine: how it models component identity, how it resolves type-specific conflicts, how it validates against live org state. The answer should be specific about what happens when two developers modify the same permission set in different sandboxes.
{% endtab %}
{% endtabs %}

A useful follow-up: _"When two sandboxes have diverged and we need to reconcile them, walk me through what your system does at the merge step."_ A wrapper will eventually describe calling Salesforce CLI commands against Git-managed artifacts. A metadata-native system will describe reasoning about org state directly.

## The overwrite check

A second diagnostic is more direct and operationally verifiable:

{% hint style="info" %}
**Before deploying to an org, does your system check whether the org contains changes that would be overwritten, and prevent the deployment if they exist?**
{% endhint %}

This is overwrite protection. It requires the tool to query the target org's current metadata state, compare it against the last known state, and identify divergence before a deployment writes to the org.

A general-purpose pipeline cannot perform this check; it has no model of org state. A basic wrapper may not perform it either. A system with genuine org-state awareness will perform it by default, because deploying without this check is structurally unsafe in any environment where admins can modify the org directly.

The presence or absence of this check is a reliable proxy for whether the tool understands Salesforce's operating model at a structural level.
