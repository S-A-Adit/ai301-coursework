# Procedure: how this skill grades a plan package

<!--
THIS IS THE PART YOU WRITE, and it is a new kind of part. Weeks 1 and
2, SKILL.md carried a numbered workflow and you only wrote judgment
files. This week the workflow is gone from the frame: SKILL.md says
"execute procedure.md", and these are the operating steps you author.
The machinery is in your hands now.

Your operator swap is the design brief. When your executor stalled
because your rubric said WHAT to decide but not HOW to find the
evidence, that was a procedure gap. This file is where those gaps get
closed: a complete procedure lets someone who has never seen a plan
package before (a groupmate, or the skill itself) grade one exactly the
way you would.

Under each stage heading below, write the concrete steps for that
stage. The one-line note under each heading says what a complete
procedure must decide there. Write steps, not intentions: "read the
repro evidence before the plan, and note what behavior it pins down"
is a step; "understand the context" is a wish.
-->

## Read order

1. Read the issue context first, to know what bug or behavior is
   supposedly being fixed.
2. Read the repro evidence next, before looking at the plan. Note down
   the confirmed cause it establishes and the supporting data
   (artifacts, logs, reproduction steps) that backs that cause. Doing
   this before reading the plan keeps the confirmed cause independent
   of how the plan later frames its own diagnosis.
3. Read the plan last, in this order: its stated cause/diagnosis, its
   scope statement (in-scope and not-in-scope), the files or areas it
   proposes to change, then its test plan.
4. This order matters because the Diagnosis and Scope checks both
   require comparing something the plan says against the repro
   evidence read first; reading the plan first would let its framing
   bias what you notice in the repro evidence.

## Evidence gathering

1. From the repro evidence, record: the confirmed cause, and the
   supporting data/artifacts that pin it down.
2. From the plan, record: the stated cause (diagnosis), the file(s) or
   area(s) it names for the fix, and what its test plan says it will
   check.
3. Compare the plan's stated cause to the repro evidence's confirmed
   cause. Record one of: matches, partially matches, contradicts, or
   ignores (the plan never engages with the confirmed cause at all).
4. For Scope, record whether every file/change the plan proposes
   connects back to the diagnosed cause from step 3, or whether any
   proposed change is adjacent/unconnected.
5. For Test, record whether the named test or test case is specific
   enough to catch the diagnosed cause (would fail before the fix,
   pass after), not just a generic "run the app and check" statement.

## Check execution

For each check, grade `P` if its pass condition is clearly met, `F` if
it is clearly not met, and `?` if the needed evidence is missing or
too ambiguous to apply the pass condition.

1. Grade Diagnosis using the comparison from Evidence gathering step
   3: `P` if the plan's cause matches or clearly follows from the
   repro-confirmed cause and names a candidate file; `F` if it
   contradicts or ignores the confirmed cause, or names no file; `?`
   if the repro evidence never pinned down a cause clearly enough to
   compare against.
2. Grade Scope using step 4: `P` if every proposed change connects to
   the diagnosed cause; `F` if any proposed change is adjacent or
   unconnected, even if the rest is sound; `?` if the plan has no
   scope statement to grade.
3. Grade Test using step 5: `P` if the named test/case would
   specifically catch the diagnosed cause; `F` if the test plan is
   vague or unrelated to the diagnosis; `?` if a test is mentioned but
   what it actually checks cannot be determined.
4. Grade the three checks independently, in the order above. A fail or
   unclear grade on one check never changes how another check is
   graded.
5. Record a one-line evidence quote or fact backing each grade; do not
   re-read the whole package once a grade is recorded unless a later
   check sends you back to re-check a specific fact.

## Verdict assembly

1. Apply the rubric's verdict rule: `accept` (ready) only if
   Diagnosis, Scope, and Test are all graded `P`.
2. Treat `?` the same as `F` for every check, per the rubric's verdict
   rule — unclear never earns the benefit of the doubt.
3. If any one of the three checks is `F` or `?`, the verdict is
   `reject` (hold). Quote the evidence recorded for the first failing
   or unclear check (in the order Diagnosis, Scope, Test) as the
   deciding evidence in the output.
4. The verdict space is binary: `accept` or `reject`, nothing else.
