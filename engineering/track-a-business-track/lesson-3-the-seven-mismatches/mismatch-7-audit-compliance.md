---
description: >-
  Git answers one of the four questions auditors ask: who wrote it. The other
  three require capabilities Git doesn't have.
---

# Mismatch 7: Audit and Compliance

A diary records what you did. A courtroom transcript records who authorized it, who witnessed it, what the chain of custody was, and what the consequences would be of falsifying any of it.

Git is a diary.

When an auditor reviews a system for compliance (SOX, HIPAA, GDPR, or any other regulated framework), they're not looking for a diary. They're looking for a courtroom transcript. Specifically, they ask four questions:

**Who wrote it?** Git answers this. It records who committed each change, with a timestamp and a message.

**Who approved it?** Git does not answer this. Approvals happen in pull request comments, JIRA tickets, email threads, or verbal sign-offs, not in the commit log. There is no native, tamper-evident approval record in a Git repository.

**Who deployed it?** Git does not answer this reliably. A deployment may have been triggered by a CI pipeline, a manual action, or a change that bypassed the pipeline entirely. Git records that a change set was assembled. It doesn't record who pushed it to production, when, or from which system.

**What changed outside the pipeline?** Git cannot answer this by definition. It only knows about changes that went through it. Admins making configuration changes directly in production (which is how most Salesforce orgs operate day-to-day) are invisible to Git entirely.

Salesforce's native Setup Audit Trail retains 180 days of configuration history. SOX requires 7 years of records. That gap is not a policy shortfall you close with documentation practices. It's a structural capability gap in the tooling.

{% hint style="info" %}
For organizations in regulated industries (financial services, healthcare, life sciences, government), this mismatch is often the one that triggers a compliance finding. "We use Git for version control" is not an adequate answer to "show me your deployment approval chain for the last three years."
{% endhint %}

The practical implication is that a Git-based Salesforce pipeline, by itself, cannot satisfy the audit requirements of any serious compliance framework. Building a compliant pipeline on top of Git means reconstructing the missing records from pull request histories, deployment logs, change tickets, and manual tracking: a fragile, labor-intensive process that still cannot account for out-of-pipeline changes.
