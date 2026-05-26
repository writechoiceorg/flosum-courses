---
description: >-
  What Git and Salesforce are and their fundamental differences using the
  library catalog vs. living garden analogy.
---

# Git vs. Salesforce

The incompatibility between Git and Salesforce isn't incidental. It's structural: the product of two systems built on different assumptions about what "a change to software" means.

## Git's design assumptions

Git is a content-addressable storage system. Every object in a Git repository (commits, trees, blobs) is identified by a SHA-1 hash of its content. There is no semantic identity: objects are what they contain, and nothing more.

The practical implications:

* **Changes are file-level operations.** A diff in Git is a comparison of text content between two states of a file. The system has no model of what that content represents: a function, a field definition, a permission rule, or random bytes are all equally opaque.
* **Environments are interchangeable.** Git assumes that any clone of a repository at the same commit is semantically equivalent to any other. The "environment" is a local checkout, and it can always be reconstructed from the commit graph.
* **The repository is the source of truth.** State lives in the commit history. What's in the repo is the canonical record of what exists.

These are not limitations: they are the properties that make Git fast, distributed, and universally applicable. They are also the properties that make Git wrong for Salesforce.

## Salesforce's architectural reality

Salesforce is a deployed system: a live org with a structured metadata model, object-relational schema, and runtime state that changes continuously. Its configuration is exposed via the Metadata API as XML-serialized component representations, but that XML is a lossy projection of the org's actual state, not the state itself.

The key properties:

**Metadata components are semantically addressed.** A `CustomField` component has an identity within the org's data model: references, constraints, and relationships that exist only in the runtime system and are not fully represented in its XML. When Git compares two versions of `Account.object-meta.xml`, it compares bytes. It has no awareness that the field it's looking at is referenced by fourteen validation rules, three flows, and two Apex classes.

**The org is the source of truth.** Unlike a Git repository, the org's state is not derivable from any serialized artifact. Admins change production directly. Automated processes modify records. Configuration drift accumulates continuously, outside any pipeline. The Metadata API returns a point-in-time snapshot of what the org contains, not what any repository contains.

**Components have deployment semantics.** Deploying metadata to an org is not a file copy. It is a transaction against a live system that validates references, executes tests, and enforces constraints at deploy time. The same XML that deploys cleanly to one org may fail in another because the org's dependency graph differs.

**Most of the org's configuration surface is outside the repository by design.** The repository contains what teams explicitly retrieved and committed. The Metadata API supports a wide range of component types, and a typical pipeline retrieve covers a small fraction of them. Automations, permission structures, platform settings, and UI configurations exist in the org regardless of how disciplined the team is. The repository's partial coverage is not a process failure. It is structural.

## The library catalog vs. the living garden

Git is a library catalog. Every item has a record, a location, and a history. The catalog is authoritative, and it is updated by explicit operations.

Salesforce is a living garden. Components grow, intertwine, and affect each other in ways no catalog fully captures. The garden itself is the source of truth, and it changes continuously, whether or not you're watching.

This analogy anchors the technical distinction: Git operates at the content level; Salesforce operates at the semantic level. That difference is not philosophical. It is the origin of every failure mode this course examines.
