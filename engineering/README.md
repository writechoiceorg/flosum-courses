# Why Salesforce DevOps Needs Unique Treatment

**Course:** Why Salesforce DevOps Needs Unique Treatment

**Length:** 8–10 hours over 2–3 weeks

**Prerequisites:** Working knowledge of Git, CI/CD fundamentals, Salesforce Metadata API

***

By the end of this course, you'll be able to explain the Salesforce DevOps problem and what it costs to a CFO. No code required.For **Admins, RMs, RevOps, leaders** | Length **4–5 hours** | Prerequisites **None**

##

## Who this track is for

Developers, architects, DevOps engineers, technical leads, and platform owners. Technical depth from first principles. Scenarios drawn from real failure modes. The primary explanation goes technical first. You finish able to defend the architectural case for metadata-native DevOps to other engineers.

***

## Learning objectives

By the end of this course, you will be able to:

1. Articulate Git's design assumptions from first principles and explain precisely where those assumptions break against the Salesforce Metadata API
2. Describe the architectural difference between content-addressed (Git) and semantically-addressed (Salesforce) systems
3. Walk through each of the seven mismatches with a technical failure scenario, root-cause analysis, and category-of-solution framing
4. Build a defensible cost model for the Human Translator Tax and drift-related overhead using DORA metrics as a benchmark
5. Evaluate vendor tooling using the "semantic vs. syntactic merge" diagnostic question
6. Describe the minimum architectural requirements for a metadata-native DevOps platform
7. Score your organization against 20 technical signals and identify the highest-leverage intervention points

***

## Lessons

| Lesson | Title                              | Length (approx.) |
| ------ | ---------------------------------- | ---------------- |
| 1      | Welcome + Get Started              | 25 min           |
| 2      | Two Different Worlds               | 80 min           |
| 3      | The Seven Mismatches               | 180 min          |
| 4      | The Economics of Drift             | 60 min           |
| 5      | Why Patching Git Doesn't Work      | 45 min           |
| 6      | The Architecture You Actually Need | 40 min           |

***

## Lesson 3: sub-file structure

Lesson 3 is split into 7 files (one per mismatch). Each mismatch file follows this structure:

1. Canonical name and one-sentence summary
2. Technical mechanism: what specifically breaks and why
3. Annotated failure scenario, drawn from real Salesforce DevOps failure patterns
4. Root-cause analysis
5. Cost framing: hours, risk, dollars
6. What kind of solution prevents this
