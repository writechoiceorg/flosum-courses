---
description: Rename a field; Git updates the field, not the 14 places that reference it.
---

# Mismatch 1: Metadata Interdependency

Imagine you're managing a cookbook. You decide to rename an ingredient: "heavy cream" becomes "double cream." You update your risotto recipe. Done.

What you didn't notice: six other recipes in the same cookbook call for heavy cream. None of them updated. The cookbook now has an inconsistency that only surfaces when someone tries to cook, not when they edited it.

This is what happens when you rename a Salesforce field using Git.

Git sees the change in the field's definition file. It updates that file. But a Salesforce field doesn't exist in isolation. It's referenced by formulas, validation rules, workflows, reports, page layouts, and Apex classes, across dozens of files that Git tracks independently, with no map of how they connect.

Git doesn't know those connections exist. It has no concept of Salesforce's data model. It operates on the content of files, not the relationships between them. So it does what it does: updates the field definition, marks the commit clean, and shows a green checkmark.

The problems emerge at deployment, or after: when a validation rule references a field that no longer exists, when a report breaks silently, when a flow fails in production on a condition it could previously evaluate. The pipeline says nothing went wrong. The org disagrees.

The deeper issue is that Salesforce metadata is a graph, not a collection of independent files. Components reference each other in ways that are implicit in the data model but invisible to any tool that treats them as text. Git is, fundamentally, a text-tracking tool. It doesn't understand "this field is used by these 14 other components." It understands "this file changed."

{% hint style="warning" %}
This mismatch is particularly dangerous because it produces no immediate error. A field rename that breaks downstream components may deploy successfully and fail silently in production; the problem surfaces hours or days after the change.
{% endhint %}

The consequence for release teams is that metadata changes carry invisible blast radius. Every "safe" change to a field, object, or component requires a manual audit of dependencies that Git will never perform automatically: a tax on every release cycle that compounds with org complexity.
