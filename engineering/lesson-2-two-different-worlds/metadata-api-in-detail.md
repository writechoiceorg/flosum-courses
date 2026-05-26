---
description: >-
  How it exposes org state, component-type quirks, and why XML creates a false
  sense of Git-compatibility.
---

# The Metadata API in Detail

Understanding why Git and Salesforce are architecturally incompatible requires understanding what the Metadata API actually exposes, and what it doesn't.

## What the Metadata API is

The Salesforce Metadata API is the programmatic interface through which org configuration can be retrieved, deployed, and described. It is the mechanism that DevOps tooling uses to move changes between orgs and is the foundation of any Salesforce CI/CD pipeline.

The API represents org components as typed XML files: custom objects, fields, flows, Apex classes, permission sets, profiles, and dozens of other component types. A retrieve operation returns a directory of XML files reflecting the current state of the requested components in the org.

## Why XML creates a false sense of Git-compatibility

When developers first work with the Metadata API, the XML output looks tractable: it's text, it diffs, it can be committed to a repository and merged using standard tooling. This produces a reasonable hypothesis: if org state serializes to text files, can't we treat it like a codebase?

The hypothesis breaks down on several axes.

**The XML is a projection, not the source.** The org's actual state is the live system. The XML is what the API returns when queried: a partial serialization of certain component properties at a point in time. Two orgs can have different runtime behaviors that produce identical XML output, because the API does not serialize all runtime state.

**Component types have inconsistent and incomplete retrieval behavior.** Profiles and permission sets are the canonical example: not all permissions are returned in a retrieve unless explicitly specified in the package manifest. Some fields are conditionally included depending on org configuration. Some metadata types, including certain UI customizations and some platform settings, cannot be retrieved via the API at all and require manual configuration. This behavior is underdocumented and changes across Salesforce releases.

**Partial retrieval is structural, not incidental.** Because a full org retrieve is expensive and slow, pipelines retrieve subsets of metadata defined by a package manifest. The repository therefore contains an intentionally incomplete representation of the org by design. The repo is not a mirror of the org; it is a selective, point-in-time approximation that omits unknown portions of the configuration.

The practical scale of this gap is significant. The Metadata API supports 873 component types as of API v66 _(Salesforce Metadata Coverage Report, developer.salesforce.com/docs/metadata-coverage/66, retrieved May 2026)_. A typical CI retrieve manifest is scoped to the types a development team actively manages in source code: Apex classes, triggers, custom objects and fields, and a limited set of declarative automation types. The remaining component types (platform settings, UI customizations, sharing configurations, and many other categories) exist in the org whether or not they appear in any retrieve list. The repository's partial coverage is not an unusual edge case. It is the baseline state for any team using a selective retrieve workflow.

**XML merges produce semantically invalid results.** When two changes to the same component are merged by Git, the result is a syntactically valid XML document that may be semantically broken. The canonical case: two developers modify a Profile or Permission Set concurrently. Git produces a merged XML document. Salesforce rejects it at deployment with a validation error, because the merged document references fields that don't exist in the target org, or contains conflicting permission entries that violate org constraints.

## The practical implication

The Metadata API gives teams a programmatic mechanism for moving configuration between orgs. It is not a version control system, and retrieving to XML does not make Salesforce a codebase.

Treating the API output as a Git-managed source of truth means building a pipeline on an incomplete, inconsistently-serialized, point-in-time approximation of org state, expecting merges of that approximation to produce deployable results. The gap between that expectation and reality is where most Salesforce CI/CD failures originate.
