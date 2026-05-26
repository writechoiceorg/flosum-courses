---
description: >-
  By the end of this course you'll be able to defend the architectural case for
  metadata-native Salesforce DevOps to other engineers, from first principles.
  Working knowledge of Git assumed.
---

# Why Salesforce DevOps Needs Unique Treatment

{% hint style="info" %}
For: **Developers, architects, DevOps engineers, technical leads, & platform owners**&#x20;

Length: **8–10 hours, over 2–3 weeks**

Prerequisites: **Knowledge of Git, CI/CD fundamentals, Salesforce Metadata API**
{% endhint %}

## Course fundamentals

Salesforce is categorically different from the software Git was built for, and the difference is architectural, not cosmetic. That mismatch produces seven specific failure modes — each one rooted in something Git was never designed to do. The fix isn't a better wrapper around Git. It's a system that reasons about Salesforce metadata semantically, from the deployment engine up.

Each lesson takes one step of that argument and grounds it in a technical mechanism, a failure scenario, and the root cause that links the two.

## Key takeaways

By the end of this course, you will be able to:

1. Explain why Salesforce's metadata model breaks Git's content-addressed assumptions, with reference to the Metadata API.
2. Name the seven mismatches by mechanism: what fails, why it fails, and what kind of system would prevent it.
3. Build a defensible cost model that maps these failures to DORA metrics and to your team's actual deployment data.
4. Ask the one diagnostic question that separates a Git-wrapper tool from a metadata-native system.
5. Specify, at component level, what a metadata-native architecture must do, and where the two architectural schools (extend Git vs. replace it) actually differ.
6. Diagnose where your own pipeline sits relative to DORA Elite, and identify which mismatches are costing you the most.

## Lessons

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th></th><th data-type="content-ref"></th><th data-hidden data-card-target data-type="content-ref"></th><th data-hidden data-card-cover data-type="image">Cover image</th></tr></thead><tbody><tr><td><h4><i class="fa-rocket-launch" style="color:$primary;">:rocket-launch:</i></h4></td><td><h4>1. Getting started</h4></td><td>Get introduced to DevOps and Salesforce, and understand the pillars of this course.</td><td><a href="lesson-1-welcome-+-get-started/">lesson-1-welcome-+-get-started</a></td><td><a href="/broken/pages/PbYb0GukRhiS4qCHdRal">Broken link</a></td><td><a href=".gitbook/assets/fl-placeholder.png">fl-placeholder.png</a></td></tr><tr><td><h4><i class="fa-globe-stand" style="color:$primary;">:globe-stand:</i></h4></td><td><h4>2. Two Different Worlds</h4></td><td>See why Salesforce and Git are categorically different systems, not just different tools.</td><td><a href="lesson-2-two-different-worlds/">lesson-2-two-different-worlds</a></td><td><a href="/broken/pages/EFXeLTHVDQLFgK0Iy51O">Broken link</a></td><td><a href=".gitbook/assets/fl-placeholder.png">fl-placeholder.png</a></td></tr><tr><td><h4><i class="fa-cloud-xmark" style="color:$primary;">:cloud-xmark:</i></h4></td><td><h4>3. The Seven Mismatches</h4></td><td>Walk through the seven places Git breaks against Salesforce, with the cost of each.</td><td><a href="lesson-3-the-seven-mismatches/">lesson-3-the-seven-mismatches</a></td><td><a href="/broken/pages/oUUNprjFZmH3rqDBvb9h">Broken link</a></td><td><a href=".gitbook/assets/fl-placeholder.png">fl-placeholder.png</a></td></tr><tr><td><h4><i class="fa-panel-fire" style="color:$primary;">:panel-fire:</i></h4></td><td><h4>4. The Economics of Drift</h4></td><td>Translate every mismatch into a number a CFO will recognize.</td><td><a href="lesson-4-the-economics-of-drift/">lesson-4-the-economics-of-drift</a></td><td><a href="/broken/pages/AYVJmAXbSHYSDodrPBYA">Broken link</a></td><td><a href=".gitbook/assets/fl-placeholder.png">fl-placeholder.png</a></td></tr><tr><td><h4><i class="fa-face-head-bandage" style="color:$primary;">:face-head-bandage:</i></h4></td><td><h4>5. Why Patching Git Doesn't Work</h4></td><td>Understand why wrapper tools mitigate symptoms without fixing the root architecture.</td><td><a href="lesson-5-why-patching-git-doesnt-work/">lesson-5-why-patching-git-doesnt-work</a></td><td></td><td><a href=".gitbook/assets/fl-placeholder.png">fl-placeholder.png</a></td></tr><tr><td><h4><i class="fa-ranking-star" style="color:$primary;">:ranking-star:</i></h4></td><td><h4>6. What Actually Works</h4></td><td>See what a metadata-native architecture looks like, and why it's the only thing that holds up.</td><td><a href="lesson-6-the-architecture-you-actually-need/">lesson-6-the-architecture-you-actually-need</a></td><td></td><td><a href=".gitbook/assets/fl-placeholder.png">fl-placeholder.png</a></td></tr></tbody></table>

