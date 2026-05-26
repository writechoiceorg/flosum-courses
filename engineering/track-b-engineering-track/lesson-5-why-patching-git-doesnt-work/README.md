# Lesson 5: Why Patching Git Doesn't Work

Wrapper tools improve Git's interface without replacing its engine: this lesson maps what they reduce, what they inherit, and how to apply the diagnostic.

* [**The Wrapper Landscape**](wrapper-landscape.md): two tooling categories, what each genuinely provides, and the three structural gaps no wrapper can close regardless of Salesforce-awareness.
* [**Where Wrappers Still Fail**](wrappers-still-fail.md): Git handles the merge step while the wrapper handles the edges; Salesforce's semantic mismatches live exactly in the middle.
* [**The Diagnostic Question**](diagnostic-question.md): one question distinguishes metadata-native tools from wrappers: does the merge engine reason about Salesforce semantics, or call git merge?
* [**The 12–18 Month Pattern**](the-12-18-month-pattern.md): the three-phase arc (initial improvement, gradual accommodation, and structural ceiling) and why the wall appears at 12–18 months specifically.
* [**An Honest Assessment**](honest-assessment.md): where the wrapper ROI case holds, where it predictably weakens, and the precise question that distinguishes the two scenarios.
