---
description: >-
  What DevOps means in plain English, and why Salesforce DevOps is its own
  animal.
---

# DevOps and Salesforce

## A working definition

DevOps, stripped of the buzzwords, is an engineering discipline for moving changes from development to production with speed, reliability, and confidence. Its practices (version control, continuous integration, automated testing, staged deployment, observability, rollback) were defined primarily by teams building general-purpose software: web services, mobile applications, backend systems.

The canonical toolchain for that world is well-established. Git for version control. A CI/CD platform to run pipelines. Test frameworks to validate behavior. A deployment mechanism to push artifacts to target environments.

This toolchain works because the software it was built for shares a set of structural properties: changes live in flat text files, the system is content-addressable, deployments are file copy operations, and environments can be provisioned reproducibly from a known state.

Salesforce shares none of those properties.

## What makes Salesforce structurally different

Salesforce is not a codebase you compile and deploy to a server. It is a deployed system: a live org that is continuously modified by multiple actors (developers, admins, automated processes) through a structured metadata model exposed via the Metadata API.

The structural differences that matter for DevOps:

* **Metadata is semantically addressed, not content-addressed.** A Salesforce component is an object with an identity, relationships, and constraints within the org's data model. Git treats it as a text blob. Those are different levels of abstraction, and the gap between them is where most problems originate.
* **The org is the source of truth, not the repository.** State lives in the org. The repo is an approximation that diverges from the moment anyone makes a change outside the pipeline, which happens continuously in any active org.
* **Environments are not reproducible.** A Salesforce sandbox is a point-in-time snapshot of production, not an ephemeral, identical container. It drifts from the moment it's created.
* **Deployments are transactions, not file operations.** Deploying metadata to an org triggers validation against the org's live state: field references, validation rules, test execution, dependency resolution. The Metadata API can fail partially in ways a file copy cannot.

These aren't implementation quirks. They are architectural properties of the platform. Standard DevOps tooling doesn't account for them: not because the tools are poorly built, but because they were designed for a different substrate.

This distinction matters because some of the difficulty in Salesforce DevOps is accidental: complexity introduced by tooling choices, process gaps, or organizational decisions that can, in principle, be fixed. But the four properties above are not accidental. They are intrinsic to what Salesforce is. An org will always drift from a repository that doesn't track every in-org change. Deployments will always be transactions against a live state machine. Sandboxes will always diverge from production the moment they're created.

Understanding which problems are accidental and which are fundamental is the prerequisite for choosing tooling that addresses the root cause rather than the surface symptom. The rest of this course is a systematic examination of those fundamental properties, what they mean in practice, and what an adequate solution has to address.

## Why Salesforce DevOps is a wicked learning environment

General software development is a relatively kind learning environment: feedback is fast, experiments are cheap, and the failure signal is usually legible. You deploy, it breaks, you read the error, you fix it. The causal chain is short enough to build reliable intuitions.

Salesforce DevOps is a wicked learning environment. Feedback is delayed. The failure signal is often ambiguous: a deployment that succeeds today in a sandbox may fail tomorrow in production not because of anything you changed, but because the org changed underneath you. A merge that looks correct produces a broken permission set that only reveals itself against specific profile configurations. Rollback is not available. You can do everything right and still get a bad outcome.

In wicked environments, intuitions formed from experience can mislead rather than guide. The engineer who has shipped successfully in general software ten times has learned what good feedback looks like in a kind environment. That pattern recognition doesn't transfer cleanly to Salesforce DevOps. The failure modes are different enough that the gut-level read on "this will work" is often wrong in ways that surprise experienced engineers.

This is not an argument against experience. It's an argument for understanding the specific mechanisms that create the wicked feedback structure: the seven mismatches this course examines. Once those mechanisms are understood, the pattern recognition becomes accurate again, because it's based on the right model of the system.
