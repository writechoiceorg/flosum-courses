---
description: >-
  Git operates on file content, not object graphs. Rename a field and the 14
  silent references break.
---

# Mismatch 1: Metadata Interdependency

Git operates on file content. It has no object graph. When you rename a field, Git updates the XML file that defines that field, and nothing else. It doesn't know that `Revenue__c` is referenced by three Apex classes, a validation rule, two workflow rules, a formula field, and seven reports. From Git's perspective, those are strings in independent files, unrelated to one another.

In Salesforce, they are not unrelated. They are semantic dependencies in a live data model. Breaking any of them produces deployment errors, runtime failures, or silent data corruption.

## The MDAPI component graph

The Metadata API exposes org components as typed XML files, but the relationships between those components are not encoded in the XML itself; they exist in the org's runtime model. When you retrieve `Revenue__c` via the API, you get an XML file describing its properties. You do not get a manifest of every component that references it. That dependency graph is maintained by the org's metadata engine, not the serialization format.

This is the structural root of the mismatch. Git manages the XML files. The relationship graph that makes those files meaningful exists only in the org.

## Why XML structure hides cross-component references

Consider a validation rule that references `Revenue__c`:

```xml
<ValidationRule>
    <fullName>Require_Revenue</fullName>
    <errorConditionFormula>
        AND(ISPICKVAL(StageName, "Closed Won"), ISBLANK(Revenue__c))
    </errorConditionFormula>
</ValidationRule>
```

The reference to `Revenue__c` is an untyped string inside an XML element. Git sees it as text content. There is no way for Git to know this string is a reference to a component with its own identity, deployment constraints, and cross-referencing relationships.

When a developer renames the field to `AnnualRevenue__c` in a feature branch, Git correctly updates the `Revenue__c.field-meta.xml` file. It does not update the validation rule XML, the Apex class method signatures that reference the field API name, the formula field definitions, or the report column definitions. The repository becomes internally inconsistent in a way Git cannot detect and will not flag.

## Failure modes by timing

The inconsistency surfaces differently depending on when it's encountered:

* **At deployment:** Salesforce validates component references during deployment. If `Revenue__c` no longer exists but is referenced in an Apex class being deployed in the same change set, the deployment fails with a validation error. This is the visible failure, traceable to the change.
* **Post-deployment (staggered change sets):** If the field rename deploys first and the referencing Apex class was deployed earlier in a different change set, the reference becomes a dangling pointer. Apex compiles against the org's current schema at compile time; if the field was removed after the class last compiled, the class may fail at runtime rather than at compile time.
* **In silent drift:** Formula fields that reference deleted fields evaluate to null. Reports fail with column errors. Flows that reference the field API name break at runtime with no deployment-time warning.

## Solution category

The fix is not a more sophisticated merge strategy. It's a tool that maintains a component graph: tracking which components reference which others, and validates that graph before deployment. A system that can answer "if I rename this field, what breaks?" before the rename is deployed is operating at the semantic level. A system that can only answer "this file changed" is operating at the content level.

_The analogy that maps precisely: editing one recipe without realizing six others call for the same ingredient. The edit is valid in isolation. The cookbook breaks at cook time._
