# A Guard for a Bug That Had Already Gone Away: Duplicate `caniuse-lite` Checks in Gutenberg

## Overview

WordPress/gutenberg [issue #33344](https://github.com/WordPress/gutenberg/issues/33344) reported that the repo's `package-lock.json` resolved several versions of `caniuse-lite` at once, so tools that read browser data could disagree. When I checked, trunk resolved exactly one version, so the bug in the report was gone, and nothing in the repo would catch it coming back. I opened [PR #86097](https://github.com/WordPress/gutenberg/pull/86097), which makes `npm run lint:lockfile` fail when the lockfile resolves `caniuse-lite` to more than one version. As of 2026-11-13 the PR is open and unmerged: a maintainer asked whether the issue is still reproducible, I answered, and I am waiting on a review.

## What happened

### Reproducing a bug that wasn't there

I didn't need a Gutenberg build to reproduce this. I wrote a small script, `count.py`, that counts every `caniuse-lite` entry in a lockfile (in both the old nested format and the newer `packages` format), and ran it on the commit the issue cites and on every later branch:

```text
bc1c260 lockfileVersion 1 {'1.0.30001148': 1, '1.0.30001165': 1, '1.0.30000998': 1, '1.0.30001168': 1}
wp/5.7  lockfileVersion 1 {'1.0.30001148': 1, '1.0.30001165': 1, '1.0.30000998': 1, '1.0.30001168': 1}
wp/6.0  lockfileVersion 1 {'1.0.30001292': 1}
wp/6.9  lockfileVersion 3 {'1.0.30001765': 1}
trunk   lockfileVersion 3 {'1.0.30001810': 1}
```

That output backs two claims: the duplication was real in 2021 (four versions at the cited commit), and it does not exist today (one version on trunk). The only lockfile check in the repo, `tools/validation/validate-package-lock.cjs`, rejects `"resolved": false` and never counts duplicates, so today's single version is where the lockfile happens to sit, not something the repo enforces. I put that gap to the maintainers as a question in my claim comment instead of building anything.

### Test first

I wrote the test before the change and ran it against the unchanged script. The case that feeds the validator a lockfile with two `caniuse-lite` versions failed, and the other three cases passed:

```text
expected +0 to be 1
```

That is the proof the diagnosis was right: the validator exits 0 on a duplicate. The same run showed that the plan's original before/after, using the issue's own commit, couldn't work, because `bc1c260` is a version 1 lockfile with no `packages` key and the validator crashes on it either way. I switched to a copy of the trunk lockfile with one extra `caniuse-lite` entry. Without the change the validator exits 0 on that copy. With it, it exits 1 and lists `1.0.30001810` and `1.0.30001148` with their paths.

### The change my first version needed

My first version of the check failed on the real trunk lockfile, which my fixtures had not caught. The script's loop also walks each package's `dependencies` field, and in a version 3 lockfile that field maps names to range strings such as `^1.0.30001702`. So `caniuse-lite` showed up with an undefined version and looked like a second one. I changed the check to count only entries with a string `version`, and added a test for exactly that case. The test file now has 5 tests and all pass. The lesson was to build fixtures from the shape of the real file, not from my idea of it.

### A review question that changed what the PR is

I had built before any maintainer answered my claim, which my own plan said not to do, and the PR description says so. The next thing that happened was a question from a maintainer, manzoorwanijk, on 2026-11-01:

> Now that we use the same version of every dependency throughout the monorepo, the resolution to different versions should no longer be the case. Are we able to reproduce the issue right now?

I re-checked current trunk (`dec5dbca18`, one `caniuse-lite`) and the repo's `syncpack.config.mjs`, then answered no, and explained that syncpack reads `package.json` rather than the lockfile, so the PR is only a guard. I offered to close it if they don't want that check. That was the result that landed: a maintainer read the PR and the reproduction, and the question moved the PR from "fix" to "is a guard wanted". I also asked a reviewer on 2026-11-11 to add the `[Type] Build Tooling` label, which my account can't add.

### What my own tool caught

The PR monitor I installed for this course ran once, on 2026-11-06, and drafted no reply: "Nothing is unanswered, so send nothing and wait." I checked the thread and it was right; my answer was already the last comment. I had told myself twice that the question was still open.

## Limitations and what's next

The PR guards against a problem that may not need guarding. It covers only `caniuse-lite`, it doesn't read version 1 lockfiles, and I never traced why four versions appeared in 2021. It doesn't add the scheduled data update the issue title also asks for. I also didn't run the build, the full unit suite or the end-to-end tests, and whole-repo `lint:js` never finished on my machine, so I ran it on my two changed files only.

If the maintainers say the guard isn't wanted, I'll close the PR and leave the reproduction on the issue thread. If they want it, I'll add the label and any review changes. Next time I would wait for the maintainer's answer before writing code.
