---
description: >-
  Git was built for developers working in flat text files; Salesforce was built
  for admins and business users. Forcing admins through Git reverses that value
  proposition.
---

# Who Each System Was Designed For

The technical difference between Git and Salesforce matters. But the audience difference matters just as much, and it often gets overlooked.

## Git was built for developers

Git was designed for professional software developers. People who write code, work in text editors, and think in files, functions, and commits. Using Git fluently requires understanding commits, branches, merges, and a mental model built around source code and command-line tools.

That's not a criticism of Git. It reflects who Git was built for, and those are the right assumptions for that audience.

## Salesforce was built for everyone else

Salesforce's whole premise is the opposite. The platform exists so that people who understand the business (admins, analysts, operations specialists, sales ops teams) can build and configure systems without needing a developer in the loop. An admin can create a custom object, build an approval flow, and configure field-level security without writing a single line of code or touching a terminal.

That separation between building on the platform and writing software is deliberate. It's one of Salesforce's genuine strengths: the people who know the business best can solve problems directly, without waiting for a development queue.

## What happens when you force them together

When companies try to manage Salesforce changes using Git, they're asking everyone who touches the org, including admins and business users who have never worked with version control, to operate in a system explicitly designed for professional developers.

The consequences are predictable:

* The people who best understand the business logic are excluded from the change management process, or forced to work in tools that weren't designed for them
* Developers become a mandatory translation layer, not because they understand Salesforce configuration better, but because they know Git
* The people with production access find no clean way to represent their changes in the pipeline, so they make changes directly, which creates exactly the drift the pipeline was supposed to prevent

This audience mismatch compounds every technical problem in the course. A DevOps approach that only works for developers isn't a full solution for a platform where admins and business users are the primary builders.
