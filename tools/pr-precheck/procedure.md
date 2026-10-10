# Procedure: how this tool grades a PR package

## Read order

1. Read the issue first (title, body, thread highlights) to know what
   behavior the PR is meant to fix.
2. Read the repo-facts block, live the PR template and
   `docs/CONTRIBUTING.md`. Note every ask the template makes, the
   named checks (suite, linters, pre-commit), and any AI-use policy.
3. Read the plan next, before the diff: its scope pair, the files it
   names, any deviation notes, and its test plan. List the files and
   areas it allows and the ones it excludes.
4. Read the diff and commit list: list every changed file and
   summarize each hunk in a few words.
5. Read the PR title and description, then the test evidence, last.
6. The plan comes before the diff so the plan's boundary is fixed
   before the diff's contents can bias it; the description comes after
   the diff so its claims are judged against what the diff really
   does.

## Evidence gathering

1. Plan fidelity: pair the changed-file list with the plan's allowed
   list. Record each file as inside plan, covered by deviation note, or
   outside. Record each plan step as done, noted as dropped, or missing
   without a note.
2. Description matches diff: split the description into factual claims.
   Record each as supported by the diff, contradicted, or unsupported,
   and note any material diff change the description never mentions.
3. Test evidence: record the behavior the evidence shows before and
   after. Compare it to the plan's test plan and the issue's repro
   steps: same behavior, same inputs, expected-after stated? Record
   "tests pass" claims with no observable output as non-evidence.
4. Repo checks: list the checks named in step 2 of the read order and
   record, for each, whether its outcome is visible in the evidence, or
   disclosed as not run.
5. Reviewable diff: record every debug leftover, dead or
   commented-out code, formatting-only hunk, stray file, and hunk not
   tied to the fix.
6. Template and policy: record each template ask and policy item with
   where the PR text or diff meets it, or that it does not.
7. Honest shortfall: record any plainly stated shortfall in the
   description.

## Check execution

Grade each check `P` if its pass condition is clearly met, `F` if
clearly not met, and `?` if the evidence needed is missing or too
ambiguous to apply the condition.

1. Run the checks in the rubric's table order, each independently: a
   fail or unclear on one never changes how another is graded.
2. If evidence for a check is genuinely absent (no test evidence
   section, no plan scope pair), grade it `?`, never `P`.
3. A disclosed shortfall does not make a check fail unless the check's
   own pass condition fails.
4. Record one line per check: the fact or quote that decided it. Once
   recorded, do not re-read the whole package unless a later check
   sends you back to one specific fact.
5. In live mode, after grading, apply the voice seam to the title and
   description and note rule breaks in the summary, outside the
   grades.

## Verdict assembly

1. Apply the rubric's verdict rule: `accept` only if every required
   check is `P`.
2. Treat `?` as `F` for every required check.
3. Preferred checks are reported but never change the verdict.
4. If any required check is `F` or `?`, the verdict is `reject`. Name
   the first failing or unclear required check in rubric order as the
   deciding check, and quote its recorded evidence in the summary.
5. The verdict is binary: `accept` or `reject`. End with the JSON
   block, one entry per check including the preferred one, nothing
   after it.
