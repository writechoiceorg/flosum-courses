---
description: >-
  A successful Git merge can still produce a broken Salesforce deployment
  because metadata has semantic dependencies Git can't see.
---

# Mismatch 3: Merged Doesn't Mean Works

Imagine two architects making changes to the same house blueprint simultaneously. One adds a bathroom extension on the east side. The other redesigns the kitchen entrance. You merge the two blueprints by combining their changes line by line, a perfectly valid operation.

The result: two front doors. No kitchen.

The blueprints merged successfully. The house is unbuildable.

This is what happens when two Salesforce developers modify the same Profile or Permission Set at the same time, and Git merges their work.

Git sees two sets of changes to the same file and does exactly what it's supposed to do: it applies both change sets and produces a new version that incorporates both. No conflict. Merge complete. Green light.

What Git can't see is that the merged result is semantically broken. The two changes might reference different sets of object permissions, assign conflicting access levels, or reference fields that only exist in one developer's sandbox. The XML is valid. Salesforce will reject it.

The deployment error comes later, during validation or at deployment time, and by then the change has already been approved, reviewed, and promoted through the pipeline. What looked like a completed delivery is a rollback waiting to happen.

Profiles and Permission Sets are the canonical case, but the same dynamic applies across metadata types: two changes to the same Flow, the same custom object, the same validation rule. Git merges bytes. Salesforce evaluates semantics. Those are not the same operation.

Profiles and Permission Sets attract conflicts at an unusual rate because of what they are: shared containers that absorb every change to access and permissions in the org. Add a new field and a Profile entry is needed. Change an object's access level and Permission Sets update. Assign a new page layout and another Profile record changes. In a multi-team org, nearly every piece of work ends up touching the same files simultaneously. The semantic collision isn't an edge case. It's structural.

{% hint style="warning" %}
A clean Git merge is not a safe Salesforce merge. The two are not equivalent. A pipeline that reports "merge successful" has told you something about the text of the files, not whether the resulting deployment will succeed.
{% endhint %}

The practical consequence is that merge conflicts in Salesforce are harder to find than merge conflicts in Git, because Git tells you about byte-level collisions immediately, but semantic conflicts are invisible until Salesforce tries to validate them in the org. By that point, the pipeline is blocked, the release is delayed, and someone has to manually reconcile two developers' changes against the live org state.
