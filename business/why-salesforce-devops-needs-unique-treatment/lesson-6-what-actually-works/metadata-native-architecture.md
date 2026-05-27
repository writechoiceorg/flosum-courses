---
description: >-
  Component-aware version control, semantic merge engine, org-aware deployment,
  first-class backup and rollback, compliance-ready audit trail, platform-native
  security model.
---

# Metadata-Native Architecture

The previous lessons described what doesn't work and why. This lesson describes what does. Not a product or a vendor: an architecture. A set of capabilities that a tool must have to address Salesforce's requirements at the structural level, rather than managing their symptoms with process.

## The six requirements

There are **six requirements**. A tool that satisfies all six addresses the root causes. A tool that satisfies some of them addresses some of the problems.

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><strong>Component-aware version control</strong></td><td>Tracks relationships between metadata components, not just file changes, so dependency questions have authoritative answers.</td><td><a href="metadata-native-architecture.md#id-1.-component-aware-version-control">#id-1.-component-aware-version-control</a></td></tr><tr><td><strong>Semantic merge engine</strong></td><td>Understands what metadata represents, not just what it contains, producing correct org state, not just valid XML.</td><td></td></tr><tr><td><strong>Org-aware deployment</strong></td><td>Queries the target org before writing to it, detecting in-org changes that would be overwritten before the deployment runs.</td><td></td></tr><tr><td><strong>First-class backup and rollback</strong></td><td>Complete org snapshot before every deployment. Restoration is an operation measured in minutes, not a reconstruction project.</td><td></td></tr><tr><td><strong>Audit trail built for compliance</strong></td><td>Answers all four audit questions natively: what changed, when, who authorized it, and through what approval process.</td><td></td></tr><tr><td><strong>Platform-native security model</strong></td><td>Access control runs on Salesforce's own permission model, not a separate auth layer bolted on top.</td><td></td></tr></tbody></table>

### **1. Component-aware version control**

A standard version control system tracks files. A metadata-native system tracks components (flows, permission sets, custom objects, validation rules, page layouts) as typed objects with identities and known relationships.

This distinction matters because the business questions are never about files. "What changed in the permission model between last Tuesday and today?" is a question about components. "Which approval processes depend on this flow?" is a question about relationships. A system that can only answer at the file level cannot give you a useful answer to either.

### **2. Semantic merge engine**

When two developers modify the same component in different environments and their changes need to be reconciled, the reconciliation must produce the right result, not just valid code.

A semantic merge engine understands what metadata components represent. When two versions of a permission set are merged, the system understands the permission grant semantics and produces a combined state that reflects both developers' intentions. A file-based merge produces XML that deploys without errors; a semantic merge produces metadata that means what it should.

### **3. Org-aware deployment**

Before writing anything to a production org, the system must know what the org actually contains, not just what the repo expects it to contain.

Org-aware deployment means the system queries the target org's current state before deploying, compares it against what the deployment would write, and identifies conflicts. If an admin made a change directly in the org after the last sync, the system detects it before deployment rather than silently overwriting it.

### **4. First-class backup and rollback**

Backup and rollback should be platform primitives, not emergency procedures.

In a metadata-native system, the org's complete state is captured before every deployment. If the deployment breaks something, restoring the prior state is an operation, a few minutes of platform work, not a reconstruction project. The rollback capability exists because it was designed in, not because someone built a workaround.

### **5. Audit trail built for compliance**

Meeting a compliance audit requires answering four questions about any change: what changed, when, who authorized it, and through what process. Basic deployment logs answer the first two. A compliance-ready audit trail answers all four, including approval chains, out-of-pipeline change detection, and configurable long-term retention.

This is the difference between a tool that says "audit trail" and a tool that satisfies an auditor.

### **6. Platform-native security model**

Security for the DevOps system should run on Salesforce's own security model: the same permissions, roles, and data residency guarantees that govern the rest of the Salesforce environment. Not an external authentication layer bolted on top.

Platform-native security means the DevOps tool's access controls are governed by the same system that governs the org. Permissions granted there are honored here. Revocations take effect immediately. Data doesn't leave the platform boundary to be managed by a third-party auth system.

## The checklist

If a tool doesn't address all six, it addresses some of the problems from Lessons 3 and 4, not the architecture.

The question to ask in any evaluation is: _which of these six capabilities does this tool provide natively, and which does it delegate to a process, a person, or a workaround?_

The gap between the answers is the portion of the operational cost that remains with the team.
