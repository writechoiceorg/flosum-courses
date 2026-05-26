---
description: >-
  Two schools of thought: one extends Git with a Salesforce-aware control plane;
  the other replaces it with a metadata-native system. This course doesn't pick
  a side.
---

# Extend Git or Replace It?

The six requirements in the previous page describe what needs to be true. This page describes the two schools of thought on how to get there.

Both schools agree that Git alone is insufficient for Salesforce. The disagreement is about what to do about it.

{% tabs %}
{% tab title="School 1: Extend Git" %}
The first school treats Git as the right foundation and builds a Salesforce-aware control plane on top of it. Git handles version history, branching, and the core mechanics of change management. The control plane adds Salesforce-specific handling: metadata-aware deployment, org state tracking, environment mapping, compliance logging.

The case for this approach: Git is deeply understood, widely used, and already embedded in most organizations' toolchains. A control plane that enhances Git rather than replacing it preserves that investment. Engineers trained on Git don't need to learn a new version control model. Integrations with existing CI/CD infrastructure remain relevant.

The question to ask about any tool in this school: _which of the six architectural requirements does the control plane provide natively, and which ones still rely on Git's underlying capabilities?_ The answer determines how much of the structural gap actually closes.
{% endtab %}

{% tab title="School 2: Replace Git" %}
The second school argues that the structural limitations of Git (its file-based model, its syntactic merge algorithm, its lack of org-state awareness) are not gaps that a control plane can fully bridge. The only way to meet all six requirements is to build a system that wasn't built on top of Git in the first place.

In this approach, the version control layer itself understands Salesforce component identity. Merge operations reason about component semantics, not file bytes. Org state is tracked natively, not inferred from repository contents. The system isn't Salesforce-aware as an add-on; it's Salesforce-aware by design.

The case for this approach: if the root causes are architectural, a purpose-built architecture addresses them more completely than an enhanced wrapper. The operational ceiling is higher. The accommodations described in Lesson 5 are no longer necessary.

The question to ask about any tool in this school: _does the system actually operate without Git primitives underneath, or is "metadata-native" a marketing claim over a Git-based foundation?_ The diagnostic question from Lesson 5 applies directly.
{% endtab %}
{% endtabs %}

## What this course doesn't decide

Both schools can satisfy some organizations' requirements. The right choice depends on the specific combination of org complexity, compliance obligation, release cadence, team structure, and tolerance for migration overhead.

This course doesn't pick a side. The six architectural requirements are the standard. Any tool, from either school, should be evaluated against them.

The next page introduces Flosum, which belongs to the second school. The goal there is not to make the selection decision for you, but to make it concrete: here is what a metadata-native system built specifically for Salesforce looks like in practice.
