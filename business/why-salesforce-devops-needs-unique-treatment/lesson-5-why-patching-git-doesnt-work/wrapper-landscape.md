---
description: >-
  What vendors built on top of Git, and why, including where wrappers help and
  where they still inherit Git's mismatches.
---

# The Wrapper Landscape

By this point in the course, the problem is clear. Git and Salesforce are structurally mismatched. So the natural question is: why not build something on top of Git that fixes those problems?

That's exactly what a generation of vendors tried to do.

## What a wrapper does

A wrapper tool is a product built on top of Git that adds a Salesforce-aware layer to the experience. Think of it as a dashboard in front of Git's engine. You interact with the dashboard (point-and-click deployments, visual pipeline views, approval workflows) while Git continues to do the underlying work of tracking changes and managing versions.

These tools came directly out of a real problem. For most Salesforce admins and release managers, working with Git directly is painful. Commits, branches, pull requests, and merge conflicts are developer concepts, not Salesforce concepts. Wrapper tools hid that complexity behind a more accessible interface.

## Where they genuinely help

This is important to say clearly: wrapper tools are better than no tool at all.

Before wrappers, most Salesforce teams managed releases using change sets: a manual, error-prone process involving hand-picked component lists, a limited UI, and a lot of crossed fingers. Wrapper tools replaced that with automation. That's real progress.

Specifically, wrappers tend to help with:

<table data-view="cards"><thead><tr><th></th><th></th></tr></thead><tbody><tr><td><strong>Visibility</strong></td><td>Dashboards showing what's in a release, who approved it, and where it is in the pipeline, without requiring Git literacy.</td></tr><tr><td><strong>Basic automation</strong></td><td>Deployments triggered on pull request merges, eliminating the manual steps that made change sets error-prone.</td></tr><tr><td><strong>Error reduction</strong></td><td>Missing component validation before deployment starts, catching the most common failure before it reaches production.</td></tr><tr><td><strong>Audit trail</strong></td><td>A record of who deployed what and when, at the surface level: enough for basic change management, not for compliance.</td></tr></tbody></table>

For teams moving off change sets, a wrapper is a genuine step forward.

## The steering wheel cover

Here is where the limits appear.

Imagine a car with a beautiful leather steering wheel cover. The cover makes driving more comfortable. The grip is better. It looks professional. But if the brakes don't work, the steering wheel cover doesn't change that.

Wrapper tools improve the Salesforce DevOps experience in exactly the same way. The interface gets better. Visibility improves. Basic automation replaces manual effort. But the engine underneath is still Git, and Git's structural mismatches with Salesforce don't disappear because the dashboard changed.

Every failure mode established in Lessons 2 and 3 lives in the engine, not the interface. Wrapper tools don't resolve metadata interdependency. They don't solve the back-sync gap. They don't provide rollback. They don't close the compliance gap. They make some of those problems more manageable at small scale, but they don't fix them.

As the org grows and release complexity increases, the distance between what the wrapper promises and what the engine can deliver becomes harder to close.
