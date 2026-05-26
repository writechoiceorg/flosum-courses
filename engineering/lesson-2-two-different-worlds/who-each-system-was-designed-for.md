---
description: The audience mismatch is as load-bearing as the technical mismatch.
---

# Who Each System Was Designed For

The technical properties of Git and Salesforce are different. The intended users of Git and Salesforce are equally different, and the audience mismatch is as consequential for DevOps as the architectural one.

## Git's assumed user

Git was designed for developers working in source code. Its interface is the command line. Its mental model requires understanding commits, branches, merges, rebases, and the content-addressable storage model underneath. Contributing to a Git-managed project requires fluency with a set of abstractions that take meaningful time to acquire.

These assumptions reflect Git's origins: it was built by and for people maintaining large open-source codebases, where everyone interacting with the repository was a developer. The abstractions are the right ones for that context.

## Salesforce's assumed user

Salesforce's value proposition is the inverse. The platform exists so that people who understand the business (admins, analysts, operations specialists) can build and configure systems without developer mediation. A Salesforce admin can create a custom object, build a complex flow, and configure sharing rules without writing a line of code or opening a terminal.

This is a deliberate design goal, not a side effect. Salesforce's separation between the ability to build on the platform and the ability to write software is one of the platform's core differentiators: it enables organizations to move faster by putting building capability directly in the hands of domain experts.

## The collision in practice

When a team adopts a Git-based DevOps workflow for Salesforce, they require everyone who modifies the org, including admins and business users with no version control background, to work through tooling designed for professional developers.

The structural consequences:

* **Admins become blocked contributors.** The people with the deepest understanding of the org's business logic are excluded from the change pipeline, or required to operate in a system they weren't trained for and that wasn't designed for them.
* **Developers become translators.** The translation layer between business intent and the change pipeline is a developer, not because developers understand Salesforce's configuration model better, but because they know Git. This is an expensive and error-prone intermediary.
* **Production changes happen out-of-band.** Because admins have no clean way to represent their changes in the pipeline, they make changes directly to production. This is the primary mechanism by which org-repo drift is created and compounded.

The Human Translator Tax (the cost of the Release Manager role whose primary function is reconciling what's in the repo with what's in the org) is a direct economic consequence of this mismatch. The audience gap isn't a soft problem about user experience. It has a hard cost in headcount, coordination overhead, and delay.
