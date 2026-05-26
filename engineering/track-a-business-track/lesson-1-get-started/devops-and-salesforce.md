---
description: >-
  What DevOps means in plain English, and why Salesforce DevOps is its own
  animal.
---

# DevOps and Salesforce

## DevOps in plain English

"DevOps" is one of those words that means different things depending on who's in the room. Here's the version that matters for this course:

DevOps is a set of practices for getting software from development to production as reliably and quickly as possible. At its core, it answers a straightforward question: how does a change made by a developer become something a user can actually use?

The practices that answer that question are version control, automated testing, staged deployments, rollback procedures, and audit trails. They were invented for teams building traditional software: web applications, mobile apps, and backend systems. The tools they use were built for that world.

Salesforce is not that world.

## Why Salesforce DevOps is different

Salesforce isn't software you write and deploy to a server. It's a platform you configure and extend, partly by writing code, but mostly by clicking, dragging, and declaring: creating fields, building flows, setting up permission sets, designing page layouts.

The changes your team makes don't live in text files on someone's laptop. They live in the org itself: a shared, cloud-based environment that is always on, always changing, and interconnected in ways that aren't obvious until something breaks.

This creates a specific problem: when you try to apply standard DevOps tools to Salesforce, you're taking software designed for one kind of system and forcing it onto a categorically different one. The result isn't just friction. It's a predictable set of failure modes that show up the same way, on almost every Salesforce team, everywhere.

That's what this course is about: naming those failure modes, explaining why they happen, and understanding what it takes to actually solve them.

{% hint style="info" %}
You don't need to know what Git or CI/CD pipelines are to follow this course. Those terms will come up, but everything will be explained from scratch when it does.
{% endhint %}
