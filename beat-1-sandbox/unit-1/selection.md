# Unit 1 — Issue Selection

Path: `beat-1-sandbox/unit-1/selection.md`

Record of the issue carried into Unit 2, and of the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in
the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the
wrong label is not graded.

---

## Selected issue

**Issue link**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/62

**Verdict output**

Live-mode run graded three candidates from `codepath/pathreview-ai301-fa26-s1`
(issues #62, #68, #73); all three were accepted. Full output, pasted verbatim:

```
Repo facts (as of 2026-09-19): not archived; last push 2026-09-16 (3 days ago);
CONTRIBUTING.md has no AI-contribution statement, and no AGENTS.md/AI_POLICY.md
exists in the repo, so the AI-policy check reads as silence (passes). All three
issues were opened by Aburke225 (COLLABORATOR), have 0 comments, no assignees,
and 0 linked or cross-referenced pull requests (verified via the GitHub API and
a PR search on each issue number).

Accepted, in fit order:

1. #62 — Health check references `settings.redis_host`, which does not exist on
   Settings — best fit: a pure application-code bug (config/Settings wiring + a
   FastAPI route), no domain-specific algorithm, and it's exactly the "read an
   unfamiliar codebase, write a clear PR" practice the fit profile wants.

2. #68 — Keyword search raises `ZeroDivisionError` when the index is empty —
   accepted and well-specified, but the fix sits next to a BM25 ranking
   library, closer to the "domain-specific algorithm" territory the fit
   profile says to avoid, even though the actual patch (an empty-corpus guard)
   is simple.

3. #73 — README and `.env.example` disagree about which LLM API key to set —
   accepted, lowest effort (1-2 hrs), but it's a docs/config-file sync rather
   than application code, so it gives the least practice reading the codebase.

Rejected: none — all three passed every required check.

[
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s1/issues/62",
    "checks": [
      {"name": "Maintainer activity", "grade": "pass", "evidence": "Default-branch push 2026-09-16, 3 days before today (2026-09-19)."},
      {"name": "Repository in use", "grade": "pass", "evidence": "archived: false; commit within 180 days."},
      {"name": "Newcomer-sized scope", "grade": "pass", "evidence": "One enumerated mismatch: health.py uses settings.redis_host/redis_port, Settings only defines redis_url."},
      {"name": "No existing contributor commitment", "grade": "pass", "evidence": "0 assignees, 0 comments, 0 PRs referencing #62 (API + search)."},
      {"name": "AI contribution policy", "grade": "pass", "evidence": "CONTRIBUTING.md has no AI statement; no AI_POLICY.md/AGENTS.md in repo."},
      {"name": "Clear problem and expected outcome", "grade": "pass", "evidence": "Exact mismatch named plus a concrete repro: GET /health while Redis is up returns false 503."},
      {"name": "Helpful contribution context", "grade": "pass", "evidence": "Labels, 'Estimated Effort: 2-4 hours', exact file named, repro steps given."}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s1/issues/68",
    "checks": [
      {"name": "Maintainer activity", "grade": "pass", "evidence": "Default-branch push 2026-09-16, 3 days before today."},
      {"name": "Repository in use", "grade": "pass", "evidence": "archived: false; commit within 180 days."},
      {"name": "Newcomer-sized scope", "grade": "pass", "evidence": "One enumerated gap: KeywordSearcher.index() needs an empty-corpus guard; a named xfail marker (manifest H-01) to remove on fix."},
      {"name": "No existing contributor commitment", "grade": "pass", "evidence": "0 assignees, 0 comments, 0 PRs referencing #68."},
      {"name": "AI contribution policy", "grade": "pass", "evidence": "Same repo-wide silence on AI policy."},
      {"name": "Clear problem and expected outcome", "grade": "pass", "evidence": "Exact method and error named; expected behavior mirrors search()'s existing empty-case handling."},
      {"name": "Helpful contribution context", "grade": "pass", "evidence": "Labels, 'Estimated Effort: 2-4 hours', exact source and test files, exact xfail marker to remove."}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s1/issues/73",
    "checks": [
      {"name": "Maintainer activity", "grade": "pass", "evidence": "Default-branch push 2026-09-16, 3 days before today."},
      {"name": "Repository in use", "grade": "pass", "evidence": "archived: false; commit within 180 days."},
      {"name": "Newcomer-sized scope", "grade": "pass", "evidence": "One enumerated fix: bring README.md and .env.example in line with core/config.py's recognized LLM_PROVIDER/API-key options."},
      {"name": "No existing contributor commitment", "grade": "pass", "evidence": "0 assignees, 0 comments, 0 PRs referencing #73."},
      {"name": "AI contribution policy", "grade": "pass", "evidence": "Same repo-wide silence on AI policy."},
      {"name": "Clear problem and expected outcome", "grade": "pass", "evidence": "States the exact discrepancy between README, .env.example, and config.py."},
      {"name": "Helpful contribution context", "grade": "pass", "evidence": "Labels, 'Estimated Effort: 1-2 hours', exact files named."}
    ],
    "verdict": "accept"
  }
]
```

---

## Eval iterations

**Run history**

Iteration proceeded by rubric edit, re-grading only the affected/at-risk items with
`--only` after each edit, then confirming with a full run before moving on. In order:

1. Baseline rubric, smoke test (`--limit 1`): `agreement: 0/1 scored items`
2. Baseline rubric, smoke test (`--limit 3`): `agreement: 2/3 scored items`
3. Baseline rubric, full run: `agreement: 16/20 scored items (bar: 18/20: below the bar)` — categories `claimed 4/4 clear-accept 4/8 dead-repo 3/3 policy 1/1 scope 4/4`
4. Rewrote `Repository in use` and `Newcomer-sized scope`; `--only issue-01,issue-04,issue-09,issue-19`: `agreement: 3/4 scored items`
5. Loosened `Newcomer-sized scope` further (enumerated-length wording); `--only issue-01,issue-05,issue-10,issue-15,issue-20`: `agreement: 4/5 scored items`
6. Added an "undecided core deliverable" clause to `Newcomer-sized scope`; `--only issue-01,issue-04,issue-09,issue-19,issue-05,issue-10,issue-15,issue-20`: `agreement: 6/8 scored items`
7. Added an "etc./partial list of one gap" clarification; same `--only` set: `agreement: 7/8 scored items`
8. Added the "two or more abandoned linked PRs" clause; same `--only` set: `agreement: 8/8 scored items`
9. Regression sweep on every untouched item; `--only issue-02,issue-03,issue-06,issue-07,issue-08,issue-11,issue-12,issue-13,issue-14,issue-16,issue-17,issue-18`: `agreement: 12/12 scored items`
10. Full run (rubric still had no AI-policy check): `agreement: 19/20 scored items (bar: 18/20: below the bar; category floor unmet: no match in policy)`
11. Added the `AI contribution policy` required check; `--only issue-12,issue-08,issue-10,issue-15,issue-01,issue-09,issue-16`: `agreement: 7/7 scored items`
12. Final full run, saved with `--save-run`: **`agreement: 20/20 scored items  (bar: 18/20: PASS)`**

The last score above matches the `agreement:` line in the committed `eval-run.txt`.

**Issue analysis**

`issue-09` (conda/conda#7617, "conda config clear option"). My rubric's current verdict
is `accept`; the gold label is also `accept`, with the note "old but valid bounded
feature; the 2022 claim is stale and the maintainer invited takers."

This one was not a first-try agreement. My original `Repository in use` check read: "Pass
if the repository has at least one default-branch commit within the last 180 days *and*
the issue was opened within the last 365 days." `issue-09` was opened in 2018 — eight
years before the capture date — so that second clause failed it outright even though the
repo-facts show `conda/conda` pushing commits the same week the bundle was captured. The
check was conflating two different questions: "is the repository still maintained" and
"is this particular issue recent." An old, still-valid issue in a very-alive repository
was being punished for its own age, which is exactly backwards — issue age is what the
`No existing contributor commitment` and `Maintainer activity` checks already cover via
the comment thread (a 2022 claim that went stale, a maintainer inviting someone to "give
it a try"). I rewrote `Repository in use` to look only at the repo-facts archived flag and
commit recency, dropped the issue-age clause entirely, and it now accepts `issue-09` for
the right reason: the repository is in use, independent of when this issue happened to be
filed.

**Check rationale**

From `rubric.md` as uploaded to `tools/issue-select/`, the `Newcomer-sized scope` pass
condition:

> Pass if the issue targets one coherent bug, feature, or documentation gap with a fully
> enumerated list of the edits it needs — even if that list spans several files or pages,
> lists several concrete symptoms/causes of the same problem, names optional approaches,
> or gives a partial list of examples (e.g., trailing "etc.") of the same single
> underlying gap — because a newcomer can bound the work by fixing that one gap wherever
> it shows up, without needing every instance named.

It's written this way because my first version rejected on sheer length or on hedge
words like "etc.", and that sank issues the gold labels called bounded (a docs task that
touched four files because the plan for each file was fully spelled out; a bug report
listing several instances of one missing feature with a trailing "etc."). The fix I
landed on judges boundedness by whether the issue enumerates what needs to change, not by
how many files or sentences that takes — length stopped being evidence of scope creep on
its own.

**Trade-offs**

This check now accepts issues that look long or list several sub-cases, as long as they
read as one enumerated gap. The trade-off is that it can be talked into passing something
that isn't actually as bounded as its bullet list makes it look — a well-organized issue
could enumerate five files that turn out to interact in ways a newcomer can't predict
from the issue text alone. I accepted that risk deliberately rather than reject on length,
because the alternative (rejecting anything past a certain size) was actively wrong on
`issue-01` and `issue-04` in this eval set.

To catch the specific case where "looks bounded on paper" was contradicted by hard
evidence, I added a narrower, separate clause to the same check: reject (or mark
unclear) when an issue has two or more closed/unmerged linked PRs, or a comment thread
showing repeated claim-then-abandon cycles by different contributors — that's `issue-15`
in this eval set (two closed PRs, years of claim/unassign cycles, gold: "years of design
debate and two abandoned PRs"). I re-ran `--only issue-01,issue-04,issue-09,issue-19,
issue-05,issue-10,issue-15,issue-20` as a canary after adding that clause specifically to
confirm it flipped `issue-15` to reject without dragging `issue-09` (which has exactly one
old closed, unrelated linked PR) down with it — the check requires *two or more*
specifically because one stale closed PR wasn't enough signal on its own.

---

## Selection rationale

**Selection rationale**

1. **Fit and time available.** I picked #62 over the other two accepted candidates
   because it's plain application-code work in Python (a `Settings`/config mismatch
   feeding a FastAPI health route) — no domain-specific algorithm to learn first, unlike
   #68's BM25 keyword search, and more code to actually read than #73's docs/env-file
   sync. The stated 2-4 hour estimate fits comfortably in the time I have for a first
   contribution.

2. **What the verdict got right, and what I weighed beyond it.** The rubric correctly
   confirmed the mechanical facts: the repo is alive (push 3 days old), nobody has
   touched this issue (no assignee, no comments, no linked or cross-referenced PRs), and
   the fix is a single enumerated mismatch rather than an open-ended task. What the
   rubric can't judge — because `scope.md` says fit only ranks, never gates — is which
   *kind* of bounded bug is the better use of my first contribution. All three issues
   were equally "accept," so the actual choice came down to something the checks don't
   score: I want practice reading someone else's config/settings wiring and writing a
   clean, well-tested PR, not practice reading an information-retrieval library I don't
   know yet. That's a fit judgment, not a correctness judgment, and it's exactly the kind
   of call the rubric is built to leave to me.

3. **Anticipated difficulty in claiming it.** Low. It has no assignee, no comments, and
   no linked or cross-referenced PRs as of this run, so nobody else appears to be
   working it. The Path Review house rule also means even if a classmate's claim comment
   shows up before I claim it, that alone doesn't block me — course credit attaches to
   opening the PR, not to being first. The main risk is a classmate claiming it in the
   window between this grading run and when I actually comment, since "good first issue"
   + a clear repro tends to attract attention quickly.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/issue-select/`.
