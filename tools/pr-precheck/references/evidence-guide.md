# Evidence guide: where evidence lives in a PR package

Each family below says where the evidence lives in an eval bundle (a
file like `calib-01.md`, whose sections are Repo facts, Issue, Thread
highlights, Plan context, and Candidate PR with Title, Description,
Commits, Diff, and Test evidence), where it lives in a live run, and
what good looks like. In live mode the sources are `plan.md` (with
deviation notes), the output of `git diff main...HEAD`, `git log
main..HEAD`, `test_evidence.md` (step 8), `pr_draft.md` (step 9, the
title and description), the issue thread, the repo's PR template, and
`docs/CONTRIBUTING.md`.

## Plan fidelity (harness category: silent-drift)

**Where it lives.**
- Eval: the plan's scope pair (in-scope / not-in-scope), named files,
  and any deviation notes are in the "Plan context" section. What
  actually changed is in "Candidate PR", "Diff" (file headers and
  hunks) and "Commits". The fidelity claims are in "Description".
- Live: scope pair, files, and deviation notes in `plan.md`; the
  changed files and hunks from `git diff main...HEAD`; the claims in
  `pr_draft.md`.

**What good looks like.** Every changed file and hunk is inside the
plan's stated scope or covered by a deviation note that says what
changed and why, and nothing the plan promised is missing without a
note. Silent drift runs both ways: a diff that does more (a
not-in-scope file, an extra behavior change) or less (a planned step
absent) with no note. A description that claims more, less, or
something different from the diff is drift too: check each of its
factual claims ("closes #N", "behavior unchanged", "fixes X and Y")
against the hunks.

## Test evidence (harness category: not-tested)

**Where it lives.**
- Eval: "Test evidence" under "Candidate PR", read against the test
  plan in "Plan context" and the repro steps quoted there or in the
  issue. The repo's own checks are named in "Repo facts" (suite,
  linters, pre-commit, type checks).
- Live: `test_evidence.md`, read against the test plan in `plan.md`
  and the repro in the issue thread; the repo's checks come from the
  PR template and `docs/CONTRIBUTING.md`.

**What good looks like.** An observable before and after for the
behavior the issue describes: pasted output or a test that fails
before the fix and passes after, using the plan's test plan or the
repro's own steps, with the expected-after stated. The outcome of the
repo's own named checks is visible, or the description honestly says
which were not run and why. "Tests pass", "works locally", or output
from a different behavior than the one diagnosed is not evidence.

## Diff quality (harness category: unreviewable)

**Where it lives.**
- Eval: "Candidate PR", "Diff" (unified diff) and "Commits".
- Live: `git diff main...HEAD` and `git log main..HEAD` from the
  working copy.

**What good looks like.** The fix is visible at a glance and every
hunk serves it. Tells of an unreviewable change: debug prints or
logging left in, dead code, commented-out blocks, formatting or
whitespace-only churn, renamed or reordered code unrelated to the fix,
drive-by edits, and stray files (scratch notes, build artifacts,
lockfile changes nothing asked for). Judge the content of the hunks,
not the count of commits or lines.

## Standards and comms (harness category: standards-wall)

**Where it lives.**
- Eval: "Repo facts" lists the PR template's asks, contribution
  instructions, and stated AI-use policy; explicit maintainer
  direction is in "Thread highlights". The PR's side is the "Title"
  and "Description", plus the "Diff" for asks that need a file change
  (a changelog or whatsnew entry, an added test).
- Live: the repo's PR template and `docs/CONTRIBUTING.md` for the
  asks; the issue thread for maintainer direction; `pr_draft.md` for
  the title and description; `git diff main...HEAD` for file-level
  asks.

**What good looks like.** Each ask the repo states is visibly met with
real content: the issue is referenced the way the repo asks, template
sections are filled in rather than left as boilerplate, a required
changelog entry is in the diff, any AI-use disclosure the policy
demands is present, and direction a maintainer gave in the thread is
followed. If the repo states no template or policy, there is nothing
to miss. Whether the description's claims match the diff belongs to
Plan fidelity, not here.
