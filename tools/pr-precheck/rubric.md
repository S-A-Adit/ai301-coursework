# Rubric: is this pull request ready to submit?

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| Plan fidelity | The diff's changed files and hunks, read against the plan's scope pair (in-scope / not-in-scope), its named files, and any deviation notes | Pass if every changed file and hunk falls inside the plan's stated scope or is covered by a recorded deviation note, and nothing the plan promised is missing from the diff unless a note says it was dropped. Fail if the diff touches a not-in-scope area or an unnamed file with no note, or silently omits part of the plan. A deviation that is recorded and explained passes. | required |
| Description matches diff | The PR description's claims (what it fixes, what it changes, "closes #N", "unchanged behavior") read against the diff's actual contents | Pass if every factual claim in the description is borne out by the diff and nothing the diff changes materially is left unmentioned. Fail if the description claims more, less, or something different from what the diff delivers. | required |
| Test evidence observable | The PR's test evidence read against the plan's test plan and the issue's repro steps | Pass if the evidence shows an observable before/after for the issue's behavior (output, failing-then-passing test, repro re-run) matching the plan's test plan, and states the expected-after. Fail if it only says "tests pass" or "works for me", shows no before state, tests something other than the diagnosed behavior, or is absent. | required |
| Repo checks run | The test evidence and description read against the repo-facts block's stated checks (suite, linters, pre-commit, type checks) | Pass if the evidence shows the repo's own stated checks were run with their outcome visible, or the PR honestly discloses which could not be run and why. Fail if the repo names required checks and the package shows no sign they ran and does not disclose it. | required |
| Reviewable diff | The unified diff and the commit list | Pass if the fix is visible and every hunk serves the change: no debug leftovers, dead or commented-out code, unrelated formatting churn, drive-by edits, or stray files burying it. Fail if any such debris or unrelated hunk is present. | required |
| Template and policy | The repo-facts block's PR template asks and stated contribution and AI-use policy, read against the PR title and description | Pass if every ask the template or contribution docs make of the PR text is met (issue reference, tests-added statement, changelog or whatsnew entry in the diff when asked, small-PR rule) and any required AI-use disclosure is present. Fail if a stated ask is ignored. If the repo states no template or policy, pass. | required |
| Honest shortfall | The description and test evidence read against the plan's goals | Pass if any shortfall (part of the plan not done, a check not run, a known limit) is stated plainly in the description. Never fails the verdict: it only records that disclosure was present or absent; a disclosed shortfall is not a failure of other checks. | preferred |

## Verdict rule

Accept (submit) only if every required check passes. Reject (hold) if
any required check is fail. An `unclear` grade counts as a fail for
that check: a PR that cannot be verified from the package is not ready
to submit. Preferred checks never change the verdict. A shortfall that
the description honestly discloses does not by itself cause a fail:
judge each check by its own pass condition, not by whether the PR does
everything. The four failure families map to checks: silent drift =
Plan fidelity and Description matches diff; not tested = Test evidence
observable and Repo checks run; unreviewable = Reviewable diff;
standards wall = Template and policy.
