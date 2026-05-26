---
description: >-
  Admins change production. Developers change sandboxes. Git knows neither.
  Drift is born here.
---

# Mismatch 2: Org as Source of Truth

Picture a team that shares a living document (a project plan, a policy) but instead of using a shared platform, they email copies around. You send your version to three colleagues. They each make changes. Meanwhile, you've made more changes on your copy. Four different versions now exist, each holding some portion of the truth, and no one knows which one is current.

That's Salesforce DevOps without a metadata-native workflow. Except the document is your production org, and the consequences aren't a stale policy. They're a broken release.

Here's how drift is born. A developer makes changes in a sandbox and commits them to Git. An admin, under pressure to fix something before the weekend, makes a change directly in production. A consultant adjusts a flow in QA to unblock a test. None of those changes are in the same place. Some are in Git. Some are in the org. Some are in both, out of sync with each other.

Git doesn't know any of this happened. Git knows what was last committed to it. That's all it knows.

When the next deployment runs, the pipeline pushes what's in the repo to the org. The admin's production fix gets overwritten by an older version from Git. The consultant's QA change disappears on the next refresh. The developer's sandbox work collides with changes in the target org that Git didn't know existed.

This is called code clobber, and it's not a corner case. It's the natural outcome of having two separate systems both accumulating changes with no mechanism for reconciling them.

The fundamental problem is architectural. In every other software context, the repository is the source of truth. What's in Git is what's deployed. In Salesforce, the live org is the source of truth. Git is a snapshot, a partial, point-in-time approximation of what's in the org, captured whenever someone remembers to retrieve and commit. The gap between that snapshot and reality grows continuously.

There is a second dimension to the structural gap. Source control holds only what someone explicitly committed to it, and in Salesforce that has always been a small fraction of the org's total configuration surface. The automations, permission layers, and platform settings that make up the rest exist only in the org, regardless of how disciplined the team is.

{% hint style="info" %}
This mismatch is the root of most of the others. Drift creates the conditions for Mismatches 3, 4, and 6, and makes Mismatch 7 (audit) essentially impossible to satisfy.
{% endhint %}

The Human Translator Tax that many teams pay, funding a Release Manager whose job is reconciling what's in Git with what's actually in the org, is a direct consequence of this mismatch. It's not a process problem. It's what happens when you use the wrong tool for the source of truth.
