---
description: >-
  The 12-18 month wall, the pipeline problem, and an honest assessment of what
  wrapper tools can and cannot do.
---

# The Wrapper Truth

Wrapper tools follow a recognizable adoption arc. Understanding it is what separates teams that choose the right tool from teams that discover the ceiling at the worst possible time.

## The 12–18 month wall

Teams that adopt wrapper tools go through a recognizable arc.

In the first months, everything improves. Deployments become less manual. Errors decrease. The team feels like it finally has its release process under control. The tool pays for itself quickly compared to the time previously spent on change sets.

Then, somewhere between twelve and eighteen months in, as the org grows, as the team scales, as release frequency increases, the wall arrives.

The specific form varies. But the pattern is consistent:

* A major release fails in production in a way the tool didn't catch
* A deployment corrupts a permission set or profile in ways that take hours to untangle
* An audit asks for records the tool can't produce
* A rollback becomes necessary and the team discovers the tool can't perform one
* Production has drifted so far from the repository that the pipeline has become unreliable

These aren't product defects. They're what happens when you reach the boundary of what Git's engine can support, and the wrapper has no path past it.

Teams at this point face a hard choice: work around the limitations indefinitely, or start over with something that addresses the root cause.

## The pipeline problem

A second category of tools creates this problem faster.

Some teams deploy Salesforce changes using general-purpose automation pipelines, tools designed for software development that can be configured to execute Salesforce commands. These tools are widely used. They were not built for Salesforce.

They face the same structural limitations as wrapper tools, without the Salesforce-specific layer that wrappers at least provide. Three gaps matter most:

**No overwrite protection.** Before a deployment begins, a Salesforce-aware system should check whether the target org contains changes that would be overwritten, changes an admin may have made directly, outside the pipeline. General-purpose pipelines don't perform this check. They deploy what they have. If something in production changed after the last sync, it gets overwritten silently.

**No Salesforce-native rollback.** Rolling back a Salesforce deployment means reconstructing the prior state of a live org, not reverting a file. A general-purpose pipeline treats deployment as a file operation. When something goes wrong, there is nothing to revert to.

**No awareness of Salesforce metadata.** When conflicts arise, these tools resolve them the way Git does, by comparing content. They have no understanding of how Salesforce components relate to each other or what a "valid" deployment actually means in a live org.

These aren't criticisms of the tools themselves. An automation platform that wasn't designed for Salesforce isn't failing at its job. It's doing exactly what it was built to do, in a context it wasn't built for. The problem is the mismatch, not the tool.

## An honest assessment

The right question for wrapper tools isn't "good or bad." It's "enough or not enough."

Wrappers are better than manual change sets. For small teams, low release frequency, and limited compliance requirements, a wrapper may be exactly the right level of investment.

The question is whether it stays the right level of investment as the org grows.

Every wrapper has a ceiling set by Git's structural properties. Below that ceiling, the tool delivers real value. Above it, the same structural problems that Lessons 2 and 3 described begin to re-emerge, now harder to diagnose because they're hidden behind a layer of tooling.

The adoption arc described in this lesson follows a recognizable pattern. Teams reach for a tool because it is the visible solution to a visible problem, not necessarily because they have analyzed whether it addresses the specific structural properties of their Salesforce environment. A tool that is right in principle but wrong for the root cause delivers real value up to a point, and then delivers compounding cost.

Understanding what lies beyond that ceiling is what Lesson 6 is about.
