---
description: >-
  Git captures authorship only. Compliance frameworks require approval,
  deployment, and out-of-pipeline change tracking too: the 180-day vs. 7-year
  gap is measurable exposure.
---

# Mismatch 7: Audit and Compliance

Git was designed to answer one question reliably: who changed this file, when, and what did they change it to? It answers that question well. Compliance frameworks ask three additional questions that Git has no mechanism to answer. Building a compliant Salesforce pipeline on a Git foundation requires reconstructing those answers from outside the version control system, which is fragile, incomplete, and often insufficient for regulated-industry requirements.

## The four auditor questions

When a compliance auditor reviews a system (under SOX, HIPAA, GDPR, or a sector-specific framework) they are establishing a chain of custody for changes to a controlled system. The four questions that chain-of-custody requires:

**Who wrote it?** Git answers this. The commit log records author, timestamp, and content. This is the one question Git was designed for.

**Who approved it?** Git does not answer this natively. Approval records exist in pull request comments, code review tools, change management systems, and email threads, not in the commit log. A tamper-evident, auditable approval record requires infrastructure outside Git (GitHub/GitLab PRs with signed approvals, JIRA change tickets, ServiceNow records). Those records are not natively linked to deployments. Reconstructing which approval corresponded to which deployment requires manual correlation.

**Who deployed it?** Git records what was in the repository at a given commit. It does not record who triggered the deployment from that commit to a specific org, when, or from which system. A deployment triggered by a CI/CD pipeline, a manual `sfdx force:source:deploy`, or a Metadata API call from an untracked script are indistinguishable in the repository. The deployment event exists only in CI/CD logs, if they were preserved.

**What changed outside the pipeline?** Git cannot answer this by definition. It only knows about changes that were retrieved, committed, and pushed. Direct configuration changes in production, including an admin modifying a sharing rule or a consultant adjusting a flow in Setup, are invisible to Git entirely.

## Setup Audit Trail: the gap in the platform's native capability

Salesforce's Setup Audit Trail provides a native log of configuration changes made through the Setup interface and the Metadata API. For the auditor question "what changed outside the pipeline," this is the primary platform mechanism.

The Setup Audit Trail retains 180 days of history. SOX Section 802 requires retention of audit records for 7 years. HIPAA requires 6 years. This creates a 5.5 to 6.5 year gap between what Salesforce natively retains and what regulated industries are required to demonstrate.

The Setup Audit Trail also has capability gaps independent of retention period: it does not record the approval that authorized a change, only that the change was made. It does not record which deployment package a configuration change originated from. It does not capture changes made via certain API paths or managed package operations.

{% hint style="warning" %}
For organizations in regulated industries, this mismatch is frequently the one that produces a compliance finding. A git log and a set of pull request records do not constitute an auditable change management record under most compliance frameworks; they are supporting evidence, not the record itself.
{% endhint %}

## What a compliant pipeline requires beyond Git

Building a compliant Salesforce change management process on a Git foundation requires reconstructing the missing records from external systems and maintaining their correlation across the full deployment lifecycle:

* **Approval records:** Require a change management system (ServiceNow, JIRA) with mandatory pre-deployment approval workflows, and link each deployment to the originating approval record.
* **Deployment records:** Preserve CI/CD run logs and link each production deployment to a specific pipeline run, approved change ticket, and org state before and after.
* **Out-of-pipeline changes:** Implement a process for retrieving and committing direct-in-org changes on a defined cadence, and flag unreconciled org state as a compliance risk.
* **Retention:** Export audit-relevant records to a long-term storage system before the 180-day Setup Audit Trail window expires.

This is achievable, but it requires significant process engineering on top of Git, and it remains incomplete for the "out-of-pipeline changes" question, which Git structurally cannot answer regardless of how much process surrounds it.

## Solution category

A tooling approach that treats audit as a first-class capability (rather than a reconstructed afterthought) builds the approval chain, deployment record, and configuration snapshot directly into the change management workflow, making the compliance record an automatic output of the normal deployment process rather than a manual artifact assembled before an audit.

_The analogy that maps precisely: Git is a diary. Compliance needs a courtroom transcript: who authorized it, who was present, what the chain of custody was, and what the consequences would be of falsifying any of it._
