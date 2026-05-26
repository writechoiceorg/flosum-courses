---
description: >-
  Sandbox refresh cycles, drift accumulation, and cost at scale make
  branch-per-environment an anti-pattern, yet it persists in Salesforce shops.
---

# Mismatch 4: Branching Breaks

Git branching strategies (GitFlow, trunk-based development, environment-per-branch) are designed around a core assumption: environments are ephemeral, reproducible, and interchangeable. A `develop` branch can be checked out and run anywhere. A feature branch can be tested in any environment with the same result. The environment is not a persistent artifact; it's a runtime instance of the repository state.

Salesforce sandboxes violate every part of this assumption.

## Why the assumption fails

Salesforce sandboxes are created as point-in-time copies of a parent org. From the moment of creation, they diverge:

* Admins configure them for their specific work (custom settings, test data, connected app credentials).
* Developers deploy work-in-progress changes that may not exist in any branch.
* The platform itself changes: scheduled jobs run, record data is created, flows execute.
* Sandbox refresh cycles are constrained by type: Developer and Developer Pro sandboxes can refresh daily, Partial Copy every 5 days, Full every 29 days. Unlike a Docker container or a cloud VM, you cannot refresh on demand.

When a refresh does occur, the sandbox is wiped to a copy of production, including all in-progress work that was not committed to any pipeline. Developers lose uncommitted configuration. Tooling connections break because the sandbox Org ID changes on refresh. Everything must be manually reconfigured.

## Why the anti-pattern persists

Despite these constraints, the branch-per-environment model is extremely common in Salesforce shops. The reason is understandable: it provides a visible, auditable mapping between what's in the pipeline and what's in each environment. Teams can say "the `qa` branch is what's in QA." This feels like control.

The problem is that the mapping is fictional within hours of any direct-in-org change. The `qa` branch represents what _should_ be in QA at the last point someone remembered to retrieve and commit. It doesn't represent what's actually there.

Maintaining the illusion requires continuous synchronization overhead: periodic retrieves, manual diff reviews, selective commits, conflict resolution. In practice, this work is batched before releases, which is why release integration events in Git-based Salesforce shops frequently take days rather than hours.

## The drift runs in both directions

The drift problem is not one-directional. Teams working with long-lived feature sandboxes must not only push their changes forward toward production: they must also continuously pull production's current state back into their sandboxes, because the live org keeps changing underneath them. Admin changes, other teams' deployments, and platform updates modify production continuously. A sandbox that hasn't been resynchronized is developing against a version of reality that no longer exists.

This creates a structural obligation with no native support. The Metadata API can retrieve a point-in-time snapshot of production components, but there is no atomic "sync sandbox to production" operation. Teams implement workarounds: periodic manual retrieves, selective deployments of known-changed components, scheduled full refreshes. Each approach is error-prone and introduces its own drift window. The back-propagation gap is not a workflow deficiency. It is a consequence of the absent back-sync path documented in Lesson 2: when the org is the source of truth and Git is an external observer, inbound synchronization has no first-class mechanism.

## The org-topology inversion makes the model worse

Git branches are cheap to create and destroy: they're pointers to commits. They have no cost at rest and no side effects when deleted. Branching models implicitly assume you can create many branches and merge freely.

Salesforce sandbox topology runs in the opposite direction. Sandboxes are expensive at scale, slow to provision, and tied to org licensing limits. A Full sandbox can cost meaningfully as a line item; most organizations have a handful at most. You cannot spin up an isolated environment per feature branch the way you would a Docker container or a cloud VM.

The practical consequence is that sandbox contention becomes a bottleneck: multiple feature branches competing for a small number of shared sandboxes, with the coordination overhead of shared-environment deployments and the drift risk that comes with every concurrent deployment to the same org.

{% hint style="info" %}
The teams that operate Salesforce most predictably tend not to map branches one-to-one to environments at all. Instead, they maintain environment promotion pipelines, with changes flowing through environments in order, validated at each stage against the actual org state of the next stage, and the repository as a record of what was promoted rather than the primary control mechanism.
{% endhint %}

_The analogy that makes this concrete: a theater company with three rehearsal stages, all with different props and lighting accumulated over months. Opening night is on a stage none of them match. The rehearsals weren't preparation; they were practice for conditions that don't exist._
