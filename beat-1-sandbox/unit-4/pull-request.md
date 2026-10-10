# Unit 4 — Test and Submit

Path: `beat-1-sandbox/unit-4/pull-request.md`

Record of the pull request you opened against the Path Review repo, and of the evaluation
runs that produced `eval-run.txt`. This file is graded at the path above; a copy kept
anywhere else in the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Your pull request

**Pull request**

https://github.com/codepath/pathreview-ai301-fa26-s1/pull/114

**Branch**

`fix/62-redis-health-url`

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

20/20

**Package analysis**

**pkg-01** (pandas-dev/pandas#66657, category standards-wall). Gold label: reject. The gold note reads: "solid C-parser fix with decisive ASAN evidence, but pandas' required PR checklist is visibly ignored: no closes #xxxx, no checklist, and the stated whatsnew entry is absent from the diff". My rubric also graded reject, so we agreed. The package's repo-facts block says "the PR template is a required checklist: closes #xxxx with the issue number, tests added and passed, all code checks passed (pre-commit), type annotations added to new arguments, and an entry in the latest doc/source/whatsnew/vX.X.X.rst when fixing a bug". The description never references #66657 and the diff touches only `tokenizer.c` and `test_c_parser_only.py`, with no whatsnew file. My Template and policy check requires "every ask the template or contribution docs make of the PR text is met (issue reference, tests-added statement, changelog or whatsnew entry in the diff when asked...)", so it fails here. Because that check is required, one fail holds the package, whatever the fix and its evidence look like.

**Check rationale**

"| Template and policy | The repo-facts block's PR template asks and stated contribution and AI-use policy, read against the PR title and description | Pass if every ask the template or contribution docs make of the PR text is met (issue reference, tests-added statement, changelog or whatsnew entry in the diff when asked, small-PR rule) and any required AI-use disclosure is present. Fail if a stated ask is ignored. If the repo states no template or policy, pass. | required |"

I wrote this check from the rubric's standards-wall failure family and did not revise it: the first full run already matched on both standards-wall packages. It reads that way because I made the pass condition about what the repo asked for, not how the description is formatted, so a terse PR that meets every stated ask passes and a polished one that ignores the template fails. I rejected a check that only looks for filled template headings, since boilerplate would pass it. The "if the repo states no template or policy, pass" sentence stops it inventing asks the repo never made.

**Trade-offs**

Nothing changed after my first full run, which scored 20/20 with every category matched (clear-accept 7/7, not-tested 4/4, silent-drift 4/4, standards-wall 2/2, unreviewable 3/3), so I ran no `--only` canary re-runs. The trade-off I accept: every check except Honest shortfall is required and `unclear` counts as fail, so a package I cannot verify is held even if it is fine, and a tiny typo-fix PR must still show before/after evidence. I also saw the limit of this on my own PR: two runs of the skill on identical files gave accept, then reject (on the repo's `frontend` job, which my evidence never mentioned), then accept after I ran that job and recorded the result, so a single grading run is not a stable verdict.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/pr-precheck/`.
