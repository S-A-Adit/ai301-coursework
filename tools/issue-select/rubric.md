# Rubric: is this a good first issue?

<!--
THIS IS THE PART YOU WRITE. The skill in SKILL.md executes whatever checks
you define here. It ships empty on purpose: the judgment is your work.

A filled rubric must contain:

1. At least one row in the checks table. Each row needs all four columns:
   - Check: a short name (used in the output JSON).
   - Evidence: exactly what to look at, and where. Name the source
     (repo-facts block, issue body, comment thread, or the locations in
     references/evidence-guide.md). "The repo" is not a source; "the last
     5 default-branch commit dates" is.
   - Pass condition: a condition someone else could apply and get your
     answer. Prefer thresholds with numbers ("a maintainer commented
     within 30 days") over adjectives ("maintainer is responsive").
   - Weight: `required` (a fail here rejects the issue) or `preferred`
     (never changes the verdict; a nice-to-have that helps rank the
     issues you accept).

2. A verdict rule below the table: how the check grades combine into
   accept or reject, including how `unclear` is treated. The verdict
   space is binary. If you write no rule for `unclear`, the skill treats
   it as fail.

Cover what actually kills first contributions. The lecture named four
families: the maintainer is alive, the repo is in use, the scope fits a
newcomer, and nobody else is already on it. A rubric that ignores a family
will fail eval issues designed around that family.
-->

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| Maintainer activity | The repo-facts block for default-branch commit dates and the issue's comment thread. | Pass if at least one of the following is true: (a) there is a default-branch commit within the last 90 days, or (b) a maintainer or repository collaborator has commented on or responded to an issue within the last 90 days. | required |
| Repository in use | The repo-facts block: archived flag and default-branch commit dates. | Pass if the repository is not archived and has at least one default-branch commit within the last 180 days. Do not weigh the age of the issue itself; an old issue in an actively used repository can still be valid. | required |
| Newcomer-sized scope | The issue body, issue labels, and the locations in `references/evidence-guide.md` describing issue scope and contribution difficulty. | Pass if the issue targets one coherent bug, feature, or documentation gap with a fully enumerated list of the edits it needs — even if that list spans several files or pages, lists several concrete symptoms/causes of the same problem, names optional approaches, or gives a partial list of examples (e.g., trailing "etc.") of the same single underlying gap — because a newcomer can bound the work by fixing that one gap wherever it shows up, without needing every instance named. Reject if the issue is open-ended rather than enumerated: a tracking list or umbrella bundling multiple unrelated changes/features, a codebase-wide or architectural redesign, or an issue that gives no scope information a newcomer could use to bound the work. Length alone is not a reason to reject. Also reject if the issue's core deliverable itself is undecided — the requester proposes a new feature but a required asset, target behavior, or the basic decision that the feature should exist at all has not been confirmed by a maintainer — since a newcomer cannot make that call themselves. Do not reject over a hedge on a peripheral, non-essential extra (e.g., a minor follow-up mentioned as "if needed"); judge only whether the thing the issue asks to be built is itself decided. Also reject (or mark unclear) if the issue's linked PRs include two or more closed/unmerged attempts at this same issue, or the comment thread shows a long history of repeated claim-then-abandon cycles by several different contributors — that track record is evidence the task is not as settled as its description makes it look, even when the description otherwise reads as bounded. One old closed PR is not enough on its own to trigger this. If the issue does not provide enough information to determine its scope, mark unclear. | required |
| No existing contributor commitment | The issue body and complete comment thread, including references to linked pull requests, branches, and assignees. | Pass if there is no clear evidence that another contributor has already claimed or is actively implementing the issue. Reject if a contributor has explicitly claimed it, an active pull request is addressing it, or a maintainer says someone is already working on it. | required |
| AI contribution policy | The repo-facts "contribution policy" line. | Pass unless the policy outright refuses AI-assisted contributions (e.g., "we do not accept AI-generated code or documentation"). No stated policy, AI welcomed, or AI allowed subject to caveats/human review (even if it "strongly discourages" AI or closes unreviewed AI PRs) all pass. | required |
| Clear problem and expected outcome | The issue body and any maintainer comments clarifying the task. | Pass if the issue explains a concrete problem, feature, bug, or improvement and provides enough information to understand the expected outcome. If the task is too vague to identify what should be changed, mark unclear. | preferred |
| Helpful contribution context | The issue body, labels, and relevant maintainer comments. | Pass if the issue includes at least one useful contribution signal, such as reproduction steps, acceptance criteria, a suggested approach, relevant labels, or a maintainer explanation. | preferred |

## Verdict rule

Accept an issue if and only if every required check passes.

Preferred checks never change the verdict; they are used only to rank accepted issues by fit.

A required check graded `unclear` counts as a failure. An issue is rejected if any required check fails or is unclear.

Among accepted issues, rank candidates by the number of preferred checks that pass, with more preferred checks passing ranked higher. If candidates tie, prefer the issue with clearer scope and more specific expected outcomes.

<!-- State how the grades above combine into accept or reject, and how
unclear is treated. Example shape (write your own): "accept if every
required check passes; preferred checks never change the verdict, they
rank accepted issues; unclear counts as fail." -->
