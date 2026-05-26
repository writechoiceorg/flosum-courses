---
description: >-
  Git is content-addressed; Salesforce is semantically-addressed: what that
  means for merge, diff, and deployment.
---

# Content-Addressed vs. Semantically-Addressed

This framing unifies everything in this Lesson. If one sentence captures why Git and Salesforce are incompatible, it is this: Git is content-addressed; Salesforce is semantically-addressed.

## Content-addressed systems

In a content-addressed system, an object's identity is derived from its content. In Git, every blob, tree, and commit is identified by a SHA-1 hash of its bytes. The system has no model of what the content represents: a function, a field definition, or random bytes are equally opaque.

The consequences for operations:

* **Diff** means comparing byte sequences between two content versions.
* **Merge** means applying concurrent byte-level changes to produce a new byte sequence that satisfies both change sets, with conflict resolution when the sequences overlap.
* **Equality** means identical content hashes.

Git cannot tell you that renaming a field will break the fourteen components that reference it. From Git's perspective, those are strings in different files, unrelated. There is no object graph. There is no semantic model. There are only diffs.

## Semantically-addressed systems

In a semantically-addressed system, a component's identity is its position and role within a data model, not its serialized content. In Salesforce, a `CustomField` named `Revenue__c` on the `Opportunity` object is an entity with an identity, relationships, referential constraints, and behavioral dependencies that exist entirely in the runtime system.

The consequences for operations:

* **Diff** means comparing component state within the org's data model, which may produce no difference even when XML content differs, or a critical difference when XML content is identical.
* **Deploy** means submitting a transaction to the live system that is validated against the org's current state: dependency resolution, test execution, constraint enforcement. The operation either succeeds fully or rolls back.
* **Equality** means equivalent org state, which is not the same as identical XML content.

When you deploy a change to `Revenue__c`, Salesforce resolves the component's identity in the org, validates its relationships across the data model, executes tests that touch it, and either commits the change or rolls the entire transaction back. The system understands what it's operating on.

## The gap

The gap between these two models is the structural source of Salesforce DevOps failures:

| Operation | Git (content)             | Salesforce (semantic)               |
| --------- | ------------------------- | ----------------------------------- |
| Merge     | Byte-level reconciliation | Semantic validity in org context    |
| Diff      | File content comparison   | Component state comparison          |
| Conflict  | Overlapping byte regions  | Referential or constraint violation |
| Success   | Produces valid bytes      | Produces valid org state            |
| Rollback  | Reset branch pointer      | Reconstruct prior org state         |

A successful Git merge does not imply a deployable Salesforce change set. A clean diff does not imply no org impact. An identical XML file does not imply identical component behavior across orgs.

## The diagnostic question

When evaluating any DevOps tooling for Salesforce, one question separates adequate solutions from inadequate ones:

**Does this system reason about Salesforce components at the semantic level, or does it operate at the content level?**

A system that calls `git merge` and post-processes the output is operating at the content level. It has inherited all of Git's blindness to Salesforce's semantic model, and it will encounter the same failure modes regardless of how it wraps the underlying operation.

A system that maintains a component-aware model of org state and validates changes against that model before deployment is operating at the semantic level. That distinction is the technical basis for the architectural argument in the rest of this course.
