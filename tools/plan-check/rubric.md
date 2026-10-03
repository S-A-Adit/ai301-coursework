# Rubric: is this plan ready to post and build from?

<!--
THIS IS THE PART YOU WRITE. The skill in SKILL.md executes whatever
checks you define here (via your procedure.md). It ships empty on
purpose: the judgment is your work.

A filled rubric must contain:

1. At least one row in the checks table. Each row needs all four
   columns:
   - Check: a short name (used in the output JSON).
   - Evidence: exactly what to look at, and where in the package. Name
     the part (the plan's scope statement, the test plan read against
     the repro evidence's steps, the plan comment read against the
     thread highlights, the repo-facts block) or a location from your
     references/evidence-guide.md. "The plan" is not a source; "the
     plan's stated cause read against what the repro evidence shows"
     is.
   - Pass condition: a decision rule about the OUTCOME that someone
     else could apply and get your answer. Judge the thing itself (is
     this one bounded change? could a stranger start executing it?),
     never the write-up's shape (how many sections it has, how long it
     is, whether it uses headings). Structure-shaped checks are what
     make graders disagree with themselves.
   - Weight: `required` (a fail here holds the package) or `preferred`
     (never changes the verdict).

2. A verdict rule below the table: how the check grades combine into
   accept (ready) or reject (hold), including how `unclear` is
   treated. The verdict space is binary. If you write no rule for
   `unclear`, the skill treats it as fail.

Cover what actually gets bad plans posted. The lecture named the
failure families: the diagnosis ignores or contradicts the reproduced
evidence, the change is unbounded (scope creep), the plan targets the
symptom while the evidence points at the cause, a stranger could not
start executing it, the test plan proves nothing observable, the
unknowns are dressed up as certainty, and the comment ignores what the
thread or the repo's stated conventions ask. A rubric that ignores a
family will fail eval packages designed around that family.
-->

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| Diagnosis | The plan's stated cause and intended-behavior description, read against the repro evidence's reproduced behavior and the issue context | Pass if the plan states the intended (correct) behavior, names what is actually causing the bug and why, and names the file(s) likely involved in the fix. Fail if the cause is asserted without being tied to the reproduced symptom, or no candidate file is named. | required |
| Scope | The plan's scope statement (in-scope / not-in-scope lines, files or areas named) | Pass if every change the plan proposes is connected to fixing the diagnosed cause. Fail if the plan includes adjacent information or proposed changes that are not connected to the issue — even a reasonable-sounding proposal fails this check if it is not coherent with the diagnosed cause. | required |
| Test | The plan's test plan, read against the diagnosis | Pass if the plan names a concrete way to verify the diagnosis was right or wrong — typically a separate test file or test case that would fail before the fix and pass after. Fail if the test plan is vague, asserts success without naming how it is observed, or has no way to falsify the diagnosis. | required |

## Verdict rule

Accept (ready) only if Diagnosis, Scope, and Test all pass. Reject
(hold) if any one of the three fails. A check graded `?` (unclear)
counts as a fail for that check, not as a pass — the skill's default
if this rule were silent, stated here explicitly: unclear never earns
the benefit of the doubt.
