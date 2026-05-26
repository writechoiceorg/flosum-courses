---
description: >-
  A Git push is a file operation. A Salesforce deployment is a transaction
  against a live system with validation rules, tests, and dependencies.
---

# Mismatch 5: Deployments Are Transactions

Writing a check feels like completing a transaction. You've done your part: written the amount, signed your name, handed it over. But the check isn't money until the bank clears it. The bank verifies your balance, checks the routing number, runs fraud detection, and only then moves the funds. Any one of those steps can fail, and if they do, nothing moves.

A Git push is a check. A Salesforce deployment is what the bank does with it.

When you push to Git, you're moving files from one location to another. The operation succeeds when the files transfer. Git doesn't evaluate whether the content of those files will work correctly in the environment where they land.

When you deploy to Salesforce, the platform doesn't just receive files. It validates every component you've submitted against the current state of the org: are all the dependencies this component references present? Does this configuration violate any active rules? Do all the tests that touch this change still pass? If any check fails, the entire deployment rolls back: nothing is partially applied.

That last point matters. Salesforce deployments are all-or-nothing. Either everything in the change set deploys, or nothing does. There's no "everything except the three components that failed." You fix the validation errors, rebuild the change set, and try again.

{% hint style="warning" %}
"It passed in the sandbox" is not a reliable predictor of deployment success in another org. Sandboxes diverge. A deployment that validates cleanly in one org may fail in another because the target has different dependencies, configurations, or test behavior.
{% endhint %}

This transaction model is not a limitation: it's a feature. Salesforce is protecting the integrity of a live system that real users are operating in. A partial deployment that leaves the org in a half-configured state is far more dangerous than a clean rollback.

There is a further constraint. Some changes cannot be completed in a single deployment regardless of how the pipeline is structured. Changing the type of a field that is already in use, for example, requires a sequence of coordinated steps, each one a separate transaction. The transaction model doesn't just govern whether a deployment succeeds. It also shapes how many deployments a single change requires.

What it means for release management is that Salesforce deployments require more planning, more pre-validation, and more org-context awareness than a file push. A pipeline that treats them as file operations will fail in production, not sometimes, but predictably.
