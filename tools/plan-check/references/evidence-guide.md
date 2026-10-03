# Evidence guide: where evidence lives in a plan package

<!--
THIS IS THE PART YOU WRITE (second week running: the judgment files
stay in your hands). The skill uses this guide as its map: for every
kind of evidence a rubric check names, this file says WHERE to find it
in a plan package and WHAT GOOD LOOKS LIKE when you do.

Under each family heading below, write:

- Where it lives: the exact places to look. In an eval bundle (which
  section of the package: the issue context, the repro-evidence block,
  the candidate plan's scope statement or test plan, the plan comment,
  the repo-facts block). In live mode (where on GitHub or in the
  draft: the issue thread, the student's posted repro comment, the
  repo's docs, the draft plan and comment).
- What good looks like: one or two sentences someone else could apply.
  Prefer observable conditions ("the stated cause cites behavior the
  repro evidence actually shows") over adjectives ("diagnosis is
  solid").

A rubric check whose evidence this guide cannot locate is a check
nobody else can execute, and this week that cuts twice: your
procedure.md tells the skill WHEN to gather each family, and this
guide tells it WHERE. Write the map you wish your executor had.
-->

## Diagnosis and grounding

Where it lives: in an eval package, the `Cause:` line of the
`## Candidate plan` section. Pin the behavior it must explain against
the `## Repro evidence` section's Steps and its stated
Expected/Actual split (the step where actual diverges from expected is
the behavior the cause has to account for). In live mode: the plan
draft's stated cause, against the student's own posted repro comment
on the issue thread.

What good looks like: the cause names a mechanism specific enough to
produce exactly the divergent step in the repro evidence, not a
restatement of the issue title. A diagnosis that explains a different
symptom than the one reproduced, or that never references the repro
evidence at all, contradicts or ignores it even if it sounds
plausible.

## Scope

Where it lives: the `Change:` line of `## Candidate plan`, specifically
its `In:` and `Out:` halves (or an equivalent file/area list if the
plan doesn't use that exact wording).

What good looks like: every file or area named under `In:` is one the
`Cause:` line actually implicates — read Diagnosis and Scope together,
not in isolation. An `Out:` line (or equivalent "not touching X")
present and itself tied to the same cause is a good sign, not a
formality to check off. A plan that adds an unrelated cleanup, a
drive-by rename, or a second fix for a different bug fails Scope even
if each individual change is defensible on its own.

## Executability

Where it lives: the same `Change:` line, read for concreteness rather
than boundaries: the exact file path(s) and the function, callback, or
code location named.

What good looks like: a stranger could open the named file and find
the named location without asking the author anything first — e.g.
"the push completion callback in
`pkg/gui/controllers/sync_controller.go`" is executable;
"somewhere in the sync logic" is not. Order-of-work only matters here
if the change touches more than one file and the plan doesn't say
which comes first.

## Test plan

Where it lives: the `Test:` line of `## Candidate plan`, read against
the `## Repro evidence` section's Steps.

What good looks like: the test plan names the specific repro step (or
observable) that must flip, and says what result confirms the fix
versus what result would mean the diagnosis was wrong — "at step 3 the
color must flip without leaving the view" is decisive because it says
exactly what to watch and what counts as pass/fail. "Will test it
locally" or "should work now" names no observable and is not decisive,
even if a test file is mentioned elsewhere, unless that file's
assertion is tied back to the same observable.

## Honesty

Where it lives: anywhere in `## Candidate plan` or
`## Candidate plan comment` where the plan extends past what the repro
evidence directly confirms — e.g. a claim about a second view or
code path ("since both share the callback") that the repro evidence
itself never exercised.

What good looks like: an extension beyond confirmed evidence is
flagged as an assumption to verify ("also check the main commits panel
and after a force push, since both share the callback") rather than
asserted as already-confirmed fact. False confidence looks like the
same claim stated flatly, with no signal that it goes beyond what was
reproduced.

## Comms

Where it lives: `## Candidate plan comment`, read against
`## Thread highlights` (maintainer or classmate signals already on the
issue) and against the `## Repo facts` block's contribution policy,
templates, and any AI-disclosure requirement.

What good looks like: the comment references the actual reproduction
and the actual plan in the student's own words (not "same approach as
above"), and shapes itself to what the repo facts ask for — e.g. a
repo-facts note about scarce maintainer review bandwidth is a reason
to say the fix is being kept minimal, the way calib-01's comment does
("keeping it minimal given the review-bandwidth note in CONTRIBUTING").
A comment that ignores a stated AI-disclosure requirement, or that
reads as generic boilerplate unconnected to this thread, is not
thread-aware.
