---
description: >-
  What Git and Salesforce are and why they don't mix: Git tracks files,
  Salesforce tracks live relationships between components.
---

# Git vs. Salesforce

Git and Salesforce were built for completely different jobs. Understanding that gap is what makes the rest of this course make sense.

## What Git is

Git is a version control system, a tool for tracking changes to files over time.

Think of it like version history in a Google Doc. Every edit is saved. You can see who changed what, when, and go back to any earlier version. Git does the same thing, but across hundreds or thousands of files at once, for an entire software project.

The key word is _files_. Git records snapshots of files. Each change is saved. Each version is stored.

## What Salesforce is

Think of the difference between a catalog and a garden. A library catalog records discrete items: each book clearly defined, independently stored, easy to list and version. A living garden grows, spreads roots into other plants, and changes in ways no catalog fully captures.

Git is a catalog. Salesforce is the garden.

Salesforce is not a set of files. It's a live business platform.

When your team works in Salesforce, creating a custom field, building an approval flow, setting up permissions, or changing a page layout, they're configuring a connected system where every part affects other parts.

Add a new field, and it might appear in reports, get referenced in automations, show up in page layouts, and affect integrations. Change a permission setting, and it can ripple across user access in ways that aren't obvious until something breaks.

And most of that garden has never been committed to any repository. Git knows what developers explicitly put into it. The automations, permission structures, and configurations that admins and business users maintain directly in the platform exist in the org, not in any version control system. Source control holds a partial picture. The org holds the complete one.

## Why the two don't mix

Git tracks files. Salesforce doesn't work with files; it works with relationships between components that live within the platform. Git has no way to see those relationships.

There's also a structural mismatch in how the two systems are organized, but that's worth seeing in detail rather than describing in the abstract. We'll come back to it.
