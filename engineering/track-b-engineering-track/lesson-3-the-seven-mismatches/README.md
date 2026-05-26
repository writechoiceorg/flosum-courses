# Lesson 3: The Seven Mismatches

Each of the seven mismatches has a specific technical mechanism and a class of solution: this lesson examines all seven from first principles.

* [**Mismatch 1: Metadata Interdependency**](mismatch-1-metadata-interdependency.md): Git tracks XML files, not the org's component graph. A field rename silently breaks 14 other components.
* [**Mismatch 2: Org as Source of Truth**](mismatch-2-org-as-source-of-truth.md): org state and repo state diverge continuously. No reconciliation mechanism exists, and code clobber is the natural result.
* [**Mismatch 3: Merged Doesn't Mean Works**](mismatch-3-merged-doesnt-mean-works.md): a syntactically valid merge can produce semantically broken metadata: Profiles and Permission Sets are the canonical failure case.
* [**Mismatch 4: Branching Breaks**](mismatch-4-branching-breaks.md): standard branching strategies assume environments are disposable and identical. Salesforce sandboxes are neither, and get expensive fast.
* [**Mismatch 5: Deployments Are Transactions**](mismatch-5-deployments-are-transactions.md): Salesforce validates dependencies, tests, and constraints before committing atomically or rolling back. Deploying is nothing like a file push.
* [**Mismatch 6: No Rollback**](mismatch-6-no-rollback.md): Salesforce has no rollback. Restoring a broken production org means manually reconstructing prior state while users wait.
* [**Mismatch 7: Audit and Compliance**](mismatch-7-audit-compliance.md): Git records authorship; compliance auditors need approval chains, deployment records, and out-of-pipeline changes, none of which Git tracks.
