# Contribution: Update and de-duplicate Caniuse data routinely, at least for releases

Path: `beat-2-contribution/contribution-story.md`

This file is Assignment 5, and it is graded at the path above; a copy kept anywhere else in
the repository is not read. **Keep every section header below under its exact name** — the
format criteria grade exactly that. Your course repo link is the whole submission: no tool
files and no evidence uploads are part of Assignment 5.

**Contribution Number:** [1]
**Student:** Syed Afnan Adit
**GitHub Username:** S-A-Adit
**Issue:** https://github.com/WordPress/gutenberg/issues/33344
**Pull Request:** [GitHub PR link, once opened]

One repository, built across units 5 to 8, telling the whole story of your real
contribution. Fill it as you work, not at the end — the sections below ask for evidence that
is far easier to write down as it happens than to reconstruct in the last weekend.

Contribution Number stays `[1]` unless you make more than one contribution this term; it
just numbers them.

---

## Toolkit

Every story section below, from Why I Chose This Issue through Pull
Request, ends with a Toolkit block: which tool did that phase's work
and how you used it, what it produced, what difficulties or
shortcomings it presented, and what you changed or would change
because of them. Together the blocks are the thread the rubric's
Toolkit points read, and they are the raw material for your unit-9
portfolio, so keep them current as you work rather than
reconstructing them later.

A Toolkit block looks like this (copy it into each story section and
fill it there):

```text
**Toolkit**

- Tool: [which tool did this phase's work, and how you used it]
- What it produced: [described in a sentence or a short excerpt]
- Difficulties: [what fought you or fell short, or "none met"]
- What changed: [the patch, revision, or workaround, what you would
  update, or "nothing"]
```

This section also keeps one short dated entry per week of tool work,
starting below: what you built or changed that week, what broke, and
what you changed because of it. The blocks tell the story where it
happened; these entries are the toolkit's own timeline.

All four entries are graded. Keep every heading below, and fill each
one in the week it belongs to.

**Unit 5: Built the issue-scout: a Python/CSV-JSON search strategy and a five-check rubric**

I wrote a gh search query for Python bugs touching CSV/JSON, three ranking filters (fewest comments, most recent, no assignee), and a rubric that gates on maintainer activity, repo in use, scope, existing claims and AI policy. The first scan fell short: my query had an HTML-escaped `&lt;10` and no `--json` fields, and without a recency limit three of the top five were in repos dead for 180+ days, so I fixed the query and added an `--updated` cutoff. The scout accepted one issue (usyd-cs14-1#21) and rejected all three of my hand-picked issues; the rubric couldn't tell a seven-year-old abandoned claim from a live one, and I haven't yet decided whether it should.

**Unit 6: Installed the voice guard, wrote my upstream voice rules, and wrote a repo-memory file for Gutenberg**

I extended my voice guide with upstream rules for claims, questions and pings, installed the guard and its hook, and wrote `CLAUDE.local.md` in my Gutenberg fork clone (kept out of the diff with `.git/info/exclude`). Three things broke on Windows: `ln -s` doesn't link, so the three skills got a one-line pointer to the canonical guide; `python3` is only a Store stub, so the hook runs `python`; and `check_voice.py` couldn't launch `claude` because it's a `.cmd` shim, which I fixed with `shutil.which`. The wiring test never fired the hook under headless `claude -p`, but in a normal interactive session it stopped `gh issue comment --help` with "no body the guard can read", which is the pass. The guard passed my claim draft, but I posted it from my own terminal, so the hook never saw that post, and I'll post through Claude Code from now on.

**Unit 7: [one line on the tool work]**

[2-4 sentences: the ship-gate and your gate manifest. What it produced
on your real branch, what fought you, what changed because of it.]

**Unit 8: [one line on the tool work]**

[2-4 sentences: the PR-monitor and your playbook. What it read, what it
drafted, what you did with the draft.]

---

## Why I Chose This Issue

I scanned all of GitHub with my issue-scout for open Python bugs about CSV or JSON, and it accepted only one issue, which I didn't take. I then graded three issues I'd found by hand. After I patched the rubric's scope check so scope becomes a difficulty note instead of a rejection, the scout accepted WordPress/gutenberg#33344 and flagged it **ambitious**; WordPress/ai#1166 and Ocelot-Social#1952 stayed rejected on existing claims, and PathReview#62 (my earlier practice issue) was rejected too, with 13 open PRs and about a dozen classmates already on it. I chose #33344: it's unassigned, its thread has no claim in it, the repo is very active, and it needs web tooling (a `package-lock.json` and a CI check) more than deep algorithm knowledge, which fits my profile. I hope to learn how a large monorepo's CI and lockfile tooling fit together and how to write a PR a maintainer will actually review.

The ambitious flag is real. When I checked, trunk's `package-lock.json` resolves exactly one `caniuse-lite` version, so the duplication from the original report is gone, and a maintainer said in 2025 that this issue could be closed. I raised that as the one question in my claim instead of building anything. I also chose to leave the AI-use disclosure out of my claim comment even though Gutenberg's guidelines ask contributors to disclose meaningful AI assistance; my repro-check graded that as a fail, and the disclosure will go in my PR description. On the tool: the scout held up on repo health and on spotting claims, but the first strict scope check rejected an issue that was fine to claim, which is what the patch fixed. Claim: https://github.com/WordPress/gutenberg/issues/33344#issuecomment-6372742810

**Toolkit**

- Tool: issue-scout (my search strategy and rubric, with the unit-6 scope-feasible patch), run as a scan and in direct-grade mode on four picks.
- What it produced: accept on gutenberg#33344 with difficulty ambitious; reject on WordPress/ai#1166, Ocelot-Social#1952 and PathReview#62 (existing claims).
- Difficulties: the old scope check rejected #33344; the rubric still can't tell a stale claim from a live one; and the scan never produced an issue I took, because the one accept was in a course project.
- What changed: I replaced the "Newcomer-sized scope" check with `scope-feasible` and added the difficulty note, logged in scan-notes.md.

---

## Understanding the Issue

**Problem Description**

The issue says Gutenberg's `package-lock.json` resolved several different versions of `caniuse-lite` at once, so tools that read its browser data (browserslist, autoprefixer) could disagree between packages, and the data was not being updated on any routine. At the commit the issue cites (`bc1c260`) the lockfile has four versions.

**Expected Behavior**

The lockfile resolves one `caniuse-lite` version, and something in the repo notices if a second one appears.

**Current Behavior**

On `trunk` (`44d6ae9ff9`) the lockfile resolves exactly one version (1.0.30001810), so the duplication from the report is gone. Nothing stops it coming back: the only lockfile check does not count duplicate packages. A maintainer said in 2025 the issue could be closed; another commenter disagreed, because differing versions make CSS builds unreliable.

**Affected Components**

`package-lock.json`, `tools/validation/validate-package-lock.cjs` (run by `npm run lint:lockfile`), and the Static checks workflow `.github/workflows/static-checks.yml` that runs it.

**Toolkit**

- Tool: `gh issue view` to read the thread, plus my `count.py` lockfile counter from the reproduction, and my `CLAUDE.local.md` repo notes.
- What it produced: the answers above, including that trunk has one version and that the thread has no maintainer reply since my claim on 2026-10-23.
- Difficulties: the issue title says "update and de-duplicate", but trunk shows only half of it is still open, so the four answers needed a judgment call about what "expected" means.
- What changed: I wrote the answers around the guard question I put to the maintainers instead of around the 2021 duplication.

---

## Reproduction Process

**Environment Setup**

Windows 11 Home, Git Bash, Python 3.12.5, curl. The issue is about which `caniuse-lite` versions `package-lock.json` resolves, so no Gutenberg build was needed: I read the lockfiles straight from GitHub's raw URLs. The issue names the `wp/5.7` branch and commit `bc1c2608b705f479f95c4fa1ade52fd8ad30e0fe`. I tested that commit, `trunk` (`debb46e`) and `wp/6.0` through `wp/6.9`.

Challenge: I also forked WordPress/gutenberg and made a shallow clone (`--depth=1 --filter=blob:none`), only to host my repo-memory file. The clone took over six minutes, and I didn't run `npm install`.

**Steps to Reproduce**

1. Save this as `count.py`. It counts every `caniuse-lite` entry in a lockfile, in both the old nested `dependencies` format and the newer `packages` format:

```python
import json,sys
d=json.load(open(sys.argv[2],encoding='utf8'));v={}
def walk(deps):
    for n,x in (deps or {}).items():
        if n=='caniuse-lite': v[x['version']]=v.get(x['version'],0)+1
        walk(x.get('dependencies'))
for k,x in d.get('packages',{}).items():
    if k.endswith('node_modules/caniuse-lite'): v[x['version']]=v.get(x['version'],0)+1
if not v: walk(d.get('dependencies'))
print(sys.argv[1],'lockfileVersion',d.get('lockfileVersion'),v)
```

2. Download the lockfile from the commit the issue cites and count it: `curl -sfL https://raw.githubusercontent.com/WordPress/gutenberg/bc1c2608b705f479f95c4fa1ade52fd8ad30e0fe/package-lock.json -o l.json && python count.py bc1c260 l.json`
3. Repeat step 2 with the branch name in the URL for `wp/5.7`, `wp/6.0` to `wp/6.9`, and `trunk`.
4. Observed: the issue's commit and `wp/5.7` resolve four versions; every later branch and `trunk` resolve one.

**Reproduction Evidence**

- **Commit showing reproduction:** none (the evidence is lockfile output, no change in my fork).
- **Screenshots/logs:** output excerpt:

```text
bc1c260 lockfileVersion 1 {'1.0.30001148': 1, '1.0.30001165': 1, '1.0.30000998': 1, '1.0.30001168': 1}
wp/5.7  lockfileVersion 1 {'1.0.30001148': 1, '1.0.30001165': 1, '1.0.30000998': 1, '1.0.30001168': 1}
wp/6.0  lockfileVersion 1 {'1.0.30001292': 1}
wp/6.4  lockfileVersion 2 {'1.0.30001513': 1}
wp/6.9  lockfileVersion 3 {'1.0.30001765': 1}
trunk   lockfileVersion 3 {'1.0.30001810': 1}
```

- **My findings:** the problem in the issue is real at the commit it cites: four different `caniuse-lite` versions at `bc1c260` and on `wp/5.7`. It does not reproduce on `wp/6.0`-`6.9` or on `trunk`, which each resolve one version. I didn't check `release/*` branches or the other `wp/5.x` branches, and I can't say why the duplicates went away or whether anything stops them returning. The only lockfile check in the repo, `tools/validation/validate-package-lock.cjs`, looks for `"resolved": false` and doesn't count duplicates. I put the "is a guard wanted?" question to the maintainers in my claim comment.

**Toolkit**

- Tool: repro-check, with my voice guide, to grade my claim and repro report together.
- What it produced: a full-package verdict of reject: environment, steps, behavior and honest-conclusion all passed, and the claim passed; `ai-disclosure` failed because neither comment discloses AI use, and Gutenberg's PR template and the WordPress AI Guidelines ask for it.
- Difficulties: my repro output block had four abbreviated lines that a stranger would read as literal output; and I decided not to add a disclosure line, so that check stays failed.
- What changed: I re-ran those four commands and pasted the real output. The disclosure will go in the PR description instead.

---

## Solution Approach

**Analysis**

Issue #33344 reports that `package-lock.json` resolved four different `caniuse-lite` versions at `bc1c260` / `wp/5.7` (1.0.30001148, 1.0.30001165, 1.0.30000998, 1.0.30001168), so tools that read `caniuse-lite` (browserslist, autoprefixer) could disagree between packages. My repro (see Reproduction Process) shows that is gone: `wp/6.0` to `wp/6.9` and `trunk` (`44d6ae9ff9`) each resolve exactly one version, and `package-lock.json` on trunk has a single `node_modules/caniuse-lite` entry (1.0.30001810).

What I can show is that nothing in the repo constrains it; why the four versions appeared in 2021 I have not confirmed. Likely, `caniuse-lite` is a transitive dependency of many packages, each with its own semver range, and npm keeps one copy only when one version satisfies every range, but I have not traced which packages pulled in each of the four versions at `bc1c260`. Nothing refreshes the data on a schedule either: there is no browserslist update step in `package.json` or `.github/workflows` (WordPress core runs `browserslist:update` in wordpress-develop, not here). The only lockfile check, `tools/validation/validate-package-lock.cjs` (run by `npm run lint:lockfile` and `.github/workflows/static-checks.yml`), rejects `"resolved": false` and does not look at duplicate packages. So today's single version is where the lockfile happens to sit, not something the repo enforces.

**Proposed Solution**

Only if a maintainer confirms a guard is wanted: extend `tools/validation/validate-package-lock.cjs` so `npm run lint:lockfile` also fails when `caniuse-lite` resolves to more than one version. It walks the same entries it already walks, collects the `version` of every package whose name is `caniuse-lite`, and exits 1 with a message naming the versions and the paths that pulled them in, plus the fix (run `npx update-browserslist-db@latest`, then dedupe). To make it testable, the script takes an optional lockfile path argument and defaults to the repo's `package-lock.json`.

What I will not change: `package-lock.json` itself (there is nothing to fix on trunk), the other validators, the CI workflow files (the check already runs in `static-checks.yml` through `lint:lockfile`), a scheduled data-update job, or duplicate checks for any package other than `caniuse-lite`. If a maintainer says the issue is done, or wants something different, I stop and re-plan instead of building this.

**Implementation Plan**

1. Wait for a maintainer answer to my claim comment's question. Build nothing until I have one. If there is still no answer when I'm ready to build, I ask once more and say what I'll do by when.
2. In `tools/validation/validate-package-lock.cjs`, read an optional path from `process.argv[2]`, defaulting to `../../package-lock.json`. Behavior with no argument stays identical.
3. Add the duplicate check: gather `caniuse-lite` versions from the entries already iterated (both `packages` keys ending in `node_modules/caniuse-lite` and nested ones), and if there is more than one distinct version print each version with its lockfile path and exit 1.
4. Add `tools/validation/validate-package-lock.test.js`, modeled on `validate-tsconfig.test.js`: write temporary lockfiles and run the script with them. Cases: one version passes, two versions fail with both named, the existing `"resolved": false` case still fails.
5. Run the checks in my ship manifest (`npm install`, `npm run lint:lockfile`, `npm run lint:js`, `npm run typecheck`) plus the new test, confirm `npm run lint:lockfile` still passes on the real lockfile, and re-run my `count.py` repro on a branch that has duplicates as the before/after.
6. Open the PR with `Closes #33344` only if the maintainer agrees that closes it, and disclose the AI tooling in the PR description.

**Test plan**

The diagnosis is that the current check cannot see duplicates, so the test is a lockfile with two `caniuse-lite` versions. Before the change, `node tools/validation/validate-package-lock.cjs <fixture-with-two-versions>` exits 0 (I will run it first and record that). After the change it must exit 1 and print both versions; if it still exits 0, the guard doesn't look where the duplicates are and the plan is wrong. The same test file asserts a one-version lockfile exits 0 and the `"resolved": false` case still exits 1. Then `npm run lint:lockfile` on the real trunk lockfile must still pass, and my `count.py` repro on `bc1c260` (four versions) must now make the script fail.

**Toolkit**

- Tool: plan-check, with my rubric (Diagnosis, Scope, Test), procedure and voice guide, run on `plan.md` and a draft `plan-comment.md`.
- What it produced: accept, with Diagnosis, Scope and Test all pass: the cause (no check counts duplicates) matches my repro, only `validate-package-lock.cjs` and a test next to it change, and the test plan uses a two-version lockfile that exits 0 before and must exit 1 after.
- Difficulties: the headless `claude -p` run could not read the skill folder without a permission prompt, so I ran the skill inside an interactive session. Before grading I fixed two things against my voice guide's "no certainty I haven't earned" rule: the first draft stated why the duplicates appeared as fact, though my repro never traced it, and it had no test plan. The comment still says the two-version fixture "exits 0 today" before I've run it.
- What changed: the plan now marks the cause of the 2021 duplication as likely and untraced and has a Test plan section, and I dropped an optional docs-note step. The comment is not posted; the plan is conditional on a maintainer answer.

---

## Testing Strategy

[What observable checks prove the fix: your reproduction steps re-run
against the built change (before and after), and the repo's own test
suite or checks with the outcome shown. "Tests pass" with nothing
observable named is not a strategy.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Implementation Notes

[A running log, added to as you work. Date each entry. What you
built, decisions you made when you made them, wrong turns included.]

**[Date]**

[Entry]

**[Date]**

[Entry]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Pull Request

**PR Link:** [GitHub PR URL when opened]

**PR Description:** [what the PR says; much of the content above
adapts into it]

**Maintainer Feedback:**

- [Date]: [feedback received, or "none yet"; silence is a normal
  state and costs you nothing]
- [Date]: [how you addressed it]

**Status:** [Awaiting review / Iterating / Merged / Closed. The
status is a fact you record, never a grade: the PR is graded as
opened.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Learnings and Reflections

**Technical Skills Gained**

[What you learned technically, named concretely.]

**Challenges Overcome**

[What was hard and how you solved it; point at the real events, the
Toolkit blocks above are full of them.]

**What I'd Do Differently Next Time**

[Reflection on your process.]

---

## Resources Used

[What actually helped, not a reading list. List things this story
already shows you using: the doc you followed to set the environment
up, the thread that explained the bug, the person who answered you.
One real entry beats ten plausible ones.]

- [Link to documentation that helped]
- [Discussion, issue, or post that helped]
