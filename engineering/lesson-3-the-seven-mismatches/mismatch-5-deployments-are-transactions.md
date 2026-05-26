---
description: >-
  Validation rules, test execution, and dependency resolution at deploy time
  mean "it merged" doesn't mean "it will deploy."
---

# Mismatch 5: Deployments Are Transactions

A Git push is a file operation. The operation succeeds when the files are transferred. The receiving system stores them. No validation. No constraint checking. No side effects on the running application. Success means the bytes arrived.

A Salesforce deployment is a transaction against a live, stateful system. Success means the org accepted the change, validated it against every relevant constraint, executed the required tests, resolved all dependencies, and committed the result atomically. Failure means the org rolled back to its state before the deployment started.

These are not equivalent operations. Treating them as equivalent is the source of most Salesforce deployment surprises.

## Transaction semantics of the Metadata API

When a deployment request is submitted to the Metadata API, the platform executes a multi-phase process:

**Dependency resolution.** Every component in the change set is validated against the target org's current metadata graph. Does the component reference objects, fields, classes, and record types that exist in the target org? If not, the deployment fails immediately with a dependency error.

**Constraint validation.** The platform evaluates whether the submitted configuration is valid in the target org's context, not the source sandbox's context. A validation rule that was valid in the source org may be invalid in the target if the underlying object configuration differs.

**Apex test execution.** Any deployment that includes Apex classes or triggers triggers test execution. All tests in the org that cover the deployed code must pass. This is not optional and cannot be bypassed in production. Test execution time adds directly to deployment duration. A full test suite in a large org can take 30–60 minutes or more.

**Atomic commit or rollback.** If all phases succeed, the change is committed. If any phase fails, the entire deployment rolls back: the org returns to its state before the deployment began. There is no partial commit.

## Partial deployment failures and their consequences

The atomic rollback guarantee is protective, but its consequences are not always simple:

**Deployment ordering dependencies.** Some component types must be deployed before others: Custom Objects before Custom Fields, Custom Fields before Validation Rules that reference them. If a change set doesn't account for deployment order, the API may reject components that have valid dependencies that simply weren't deployed first. The error message points to the dependency, not the ordering issue.

**Test failures in unrelated code.** The platform runs all tests that touch the deployed code, including tests written by other developers for other features. A deployment can fail because a pre-existing test in the org is broken, even if the deployed code is correct. Untreated test debt in the org becomes a deployment blocker.

**Org-specific failure modes.** A deployment that passes validation in Sandbox A may fail in production because the target org has active business processes, field-level security configurations, or data constraints that Sandbox A didn't replicate. The sandbox is not a reliable predictor of production deployment success.

{% hint style="warning" %}
"Validated successfully in sandbox" is a necessary but not sufficient condition for production deployment success. The sandbox validates against sandbox state. Production validates against production state. The two can differ in ways that are invisible until deployment time.
{% endhint %}

## Multi-deployment sequencing

The transaction model does more than determine whether a deployment succeeds. It also governs how many deployment transactions a single logical change requires.

Some changes that appear logically atomic cannot be completed in a single deployment. Changing the type of a field that is already in use, converting a lookup to a master-detail relationship on an object with existing records, or restructuring shared permission sets across a large component set can require a defined sequence of deployments, each one a separate Metadata API transaction with its own dependency resolution, constraint validation, and Apex test execution cycle.

The Metadata API does not surface the required sequencing in its error messages. It reports the immediate constraint violation, not the series of steps required to navigate around it. Engineers designing pipeline architectures for Salesforce need to account for multi-deployment sequences as a first-class design consideration. A pipeline that assumes one logical change maps to one deployment transaction will encounter these sequences as unexpected failures, not anticipated edge cases.

## What this requires of the pipeline

A pipeline that treats Salesforce deployments as file operations will treat deployment failures as surprises. A pipeline designed around Salesforce's transaction semantics treats deployment failures as the natural output of an incomplete validation process and builds pre-deployment validation directly into the pipeline: running org-aware dependency checks, reviewing test coverage, and validating against the target org's state before the deployment transaction begins.

_The analogy that maps precisely: writing a check versus the bank clearing it. The check is the file operation. The bank's clearing process is the transaction, complete with balance verification, fraud detection, and the possibility of the whole thing failing at any step._
