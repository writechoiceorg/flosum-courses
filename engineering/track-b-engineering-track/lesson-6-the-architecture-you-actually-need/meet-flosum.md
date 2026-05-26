---
description: >-
  A brief introduction to Flosum as the metadata-native system, and a pointer to
  subsequent courses in the curriculum.
---

# Meet Flosum

Flosum is a metadata-native DevOps platform built specifically for Salesforce. It belongs to School 2: its VCS layer is purpose-built for Salesforce component identity, without Git primitives underneath. Merge, diff, version history, and conflict resolution operate on the org's component graph natively.

The six architectural requirements from the previous page are Flosum's foundational design constraints, not add-on capabilities. Component-aware version control, semantic merge, org-aware deployment with overwrite protection, first-class backup and rollback, compliance-native audit trail with configurable long-term retention, and platform-native security via Salesforce's own permission model: these are what the platform was built to provide, not what it was extended to support.

The relevance to this course is structural: Flosum is the concrete implementation of the architectural category this lesson describes. The foundations course establishes the problem space and the requirements. It is not the right place to examine Flosum's pipeline model, metadata engine, deployment architecture, or org-sync mechanism in depth; that work belongs to the specialist and architect courses in the curriculum.

Those courses cover Flosum's operational model thoroughly: how pipelines are configured, how the metadata engine handles specific component types, how org sync is structured, how compliance features are configured to meet specific regulatory requirements. The foundations course ends here, at the point where the problem is precisely defined and the solution category is identified. The next courses begin at implementation.

## What success actually looks like

One useful measure of a mature Salesforce DevOps practice: whether deployment is still a specialist function.

Lesson 2 established that Salesforce's intended audience includes admins, analysts, and operations specialists who build on the platform without developer mediation. A DevOps architecture that requires Git fluency to participate in the change pipeline inverts this: it takes the people with the deepest understanding of the business configuration and requires them to work through a system they weren't trained for. A specialist team forms to manage what the tooling can't.

The measure of the solution is whether it dismantles that bottleneck. If the people who understand the org can participate in the change process without becoming engineers, the architecture is working. If they still can't, the architecture has reproduced the same specialist dependency in a new form, and the fundamental problem from Lesson 2 remains unsolved at the organizational level, regardless of how the technical layer performs.
