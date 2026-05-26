---
description: >-
  Git reverts a commit in seconds. Salesforce has no rollback. You're
  reconstructing the prior state by hand, in production, while the business
  waits.
---

# Mismatch 6: No Rollback

In Git, rolling back a bad deployment takes seconds. You find the commit before the problem, revert to it, and push. Done. The system was designed for this. Reversing a change is as cheap and safe as making one.

In Salesforce, there is no rollback.

Git is Control-Z: instant, reliable, predictable. Salesforce is "rebuild the sandcastle from memory," while the tide is coming in.

```mermaid
flowchart TB
    subgraph Git["Git: reversal is a primitive"]
        direction TB
        G1["Broken production"]
        G2["Previous commit"]
        G3["Restored"]
        G1 -->|git revert| G2
        G2 -->|git push| G3
    end

    subgraph SF["Salesforce: reversal is reconstruction"]
        direction TB
        S1["Broken production"]
        S2["Identify components"]
        S3["Reconstruct prior state"]
        S4["Redeploy as new change"]
        S5["May fail"]
        S1 --> S2
        S2 --> S3
        S3 --> S4
        S4 --> S5
        S3 -.->|drift| R1["Rollback breaks"]
        S4 -.->|data already moved| R2["No prior state to restore"]
    end

    classDef good fill:#E1F5EE,stroke:#0F6E56,color:#04342C
    classDef bad fill:#FAECE7,stroke:#993C1D,color:#4A1B0C
    classDef risk fill:#FCEBEB,stroke:#A32D2D,color:#501313

    class G1,G2,G3 good
    class S1,S2,S3,S4,S5 bad
    class R1,R2 risk
```

Here's what actually happens when a deployment breaks production in a Salesforce org. You identify the components that need to be reverted. You retrieve what those components looked like in the previous state: from a backup, if you have one; from a sandbox, if it hasn't drifted; from memory, if that's all you have. You redeploy the previous configuration as a new deployment, which is subject to the same validation rules and transaction semantics that caused the original problem. If anything in the org has changed in the meantime (and in production, things are always changing), the "rollback" may fail too.

This isn't a gap that process discipline closes. It's an architectural property of the platform. Salesforce deployments are transactions against a live state machine. That state machine doesn't have an undo primitive. The only way back is forward, through a new deployment that restores the prior configuration.

There is an even harder case. Some production changes have no recovery path at all. When data has already moved against a new configuration, or when the platform cannot reverse an operation by design, there is no prior state to restore. The sandcastle isn't just hard to rebuild. The tide already came in.

{% hint style="warning" %}
The cost of rollback in Salesforce isn't just recovery time. It's the business impact of the hours production is degraded or unavailable while reconstruction happens manually. For organizations running on Salesforce, a broken deployment affects every user on the platform in real time.
{% endhint %}

The absence of native rollback is one of the primary reasons pre-deployment validation matters so much in Salesforce. You can't easily undo a bad deploy, so you have to know before you deploy whether it will succeed. A tooling approach that validates changes against actual org state before they go out addresses the problem at the source. One that doesn't puts the burden of recovery on the team: in production, under pressure, without a reliable path back.
