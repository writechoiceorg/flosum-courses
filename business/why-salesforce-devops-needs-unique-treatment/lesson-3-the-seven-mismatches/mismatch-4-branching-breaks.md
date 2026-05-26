---
description: >-
  GitFlow assumes ephemeral, identical environments. Salesforce sandboxes drift
  the moment they exist.
---

# Mismatch 4: Branching Breaks

Imagine a theater company preparing a production. They have three rehearsal stages: one for blocking, one for costume work, one for full dress rehearsals. Each stage has accumulated its own configuration over months: different lighting rigs, different prop arrangements, different backdrops.

When opening night arrives, the cast performs on a stage they've never actually used. Half their rehearsed movements don't translate. The blocking from Stage 1 doesn't work with Stage 2's props. The dress rehearsal was for a different configuration entirely.

This is what happens when a Salesforce team maps their development workflow directly to a Git branching model.

GitFlow is the dominant branching strategy in software development. It works by creating branches for features, merging them into a development branch, promoting through testing, and shipping to main. It assumes environments at each stage are identical, or close enough that differences don't matter.

Salesforce sandboxes are never identical. The moment a sandbox is created, it begins diverging from the org it copied. Admins make direct changes. Developers configure it for their specific work. Refresh cycles are slow, sometimes weeks apart, and a refreshed sandbox overwrites all the changes made in it.

When a team runs GitFlow on top of this, they're mapping abstract branch pointers to live, stateful, diverging environments. The `develop` branch represents what should be in the dev sandbox, not what actually is. The gap between "should be" and "actually is" grows with every day and every direct-in-org change.

{% hint style="info" %}
The branch-per-environment model is an anti-pattern in Salesforce specifically because it assumes the same interchangeability of environments that Git assumes for branches. That assumption is false for Salesforce orgs by design.
{% endhint %}

The drift compounds in both directions. Teams working with long-lived feature sandboxes don't only have to push their changes forward to production. They also have to pull production's changes back down into their sandboxes continuously, because the live org keeps changing underneath them: direct admin changes, other teams' deployments, platform updates. A sandbox that hasn't been resynchronized is developing against a version of reality that no longer exists. Standard branching models weren't designed to manage traffic in both directions simultaneously.

The consequence isn't just technical friction. Sandboxes at scale carry real costs: licensing, refresh time, and manual reconfiguration after each refresh. When the branching model doesn't map to how sandboxes actually work, teams pay for complexity that doesn't solve the underlying problem.
