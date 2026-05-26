# Lesson 2: Two Different Worlds

Git is content-addressed and repo-authoritative; Salesforce is semantically-addressed and org-authoritative: this lesson builds the precise model that makes Lesson 3 predictable.

* [**Git vs. Salesforce**](git-vs-salesforce.md): contrasts Git's content-addressed, file-level model with Salesforce's semantically-addressed, live-org model: the library catalog vs. the living garden.
* [**Who Each System Was Designed For**](who-each-system-was-designed-for.md): Git assumes developer users; Salesforce was designed to eliminate developer mediation: forcing both creates expensive, unscalable translation overhead.
* [**The Metadata API in Detail**](metadata-api-in-detail.md): why the Metadata API's XML looks Git-compatible but isn't: partial retrieval, inconsistent serialization, the gap between snapshot and org state.
* [**Content-Addressed vs. Semantically-Addressed**](content-addressed-vs-semantically-addressed.md): the unifying frame: Git objects are what they contain; Salesforce components are what they mean in the org's data model.
* [**The Topology Insight**](topology-insight.md): branches descend from main; sandboxes descend from production: the inverted topologies that make standard branching strategies structurally invalid.
* [**Why This Difference Matters**](why-this-difference-matters.md): topology inversion, absent back-sync path, and continuous branch-org divergence: the three consequences that generate every failure mode in Lesson 3.
