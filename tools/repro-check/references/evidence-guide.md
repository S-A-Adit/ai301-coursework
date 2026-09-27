# Evidence guide: where proof lives in a reproduction package

## Environment

Where it lives: eval — the repro report's opening "Environment"/environment
line, read against the `## Issue`'s own stated environment and the
repo-facts block's "bug reports" template line (it lists exactly which
fields that repo expects: version, OS, install method, etc.). Live — the
draft repro comment's environment line, read against the issue body's
stated version/OS and the repo's issue template (linked from the repo's
`.github/ISSUE_TEMPLATE` or CONTRIBUTING doc).

What good looks like: every field the bug-report template asks for is
named with a concrete value (not "latest" or "my machine"), and if the
tested version/OS differs from the one in the issue, the report says so
in a sentence rather than leaving the reader to notice the mismatch
themselves.

## Steps

Where it lives: eval — the repro report's steps/command block(s),
read against the issue's own "Steps"/reproduction section to find the
specific trigger action. Live — the same, in the draft repro comment.

What good looks like: literal commands or inputs, in the order run, from
a stated starting point (a fresh directory, a given file, a given
config) — not a paraphrase of an action ("configured the project"). The
step that corresponds to the issue's trigger (the exact keypress,
argument, or input shape the issue names) is present and unaltered, not
approximated or swapped for a nearby variant.

## Behavior shown

Where it lives: eval — the repro report's shown output/log/artifact,
read side-by-side with the issue's own quoted error, panic, or described
symptom (not the issue's title, which can be broader than the actual
trigger). Live — the same comparison, between the draft's pasted
output and the issue thread's quoted behavior.

What good looks like: the artifact's failure mode (the exact error
text, exit behavior, or absence of expected output) matches the kind of
failure the issue reports — a panic confirms a panic, a silent no-op
confirms a silent no-op, not a graceful validation error standing in for
either. When the report instead states it could not reproduce, it names
the specific difference between its setup and one that plausibly would
trigger the behavior (a version, a platform, a data shape) rather than
just reporting a negative result. Read the artifact itself, never the
prose summary of it — a summary can describe a panic that the pasted
output does not actually contain.

## Honesty

Where it lives: eval — the repro report's closing "Expected/Actual" or
summary lines, checked against every artifact shown earlier in the same
report. Live — the same, in the draft repro comment.

What good looks like: each claim in the conclusion traces to a specific
run shown above it. A cannot-reproduce conclusion that names what was
tried and what differed is exactly as valid a conclusion as a confirmed
one — treat it as proof stated honestly, not as an incomplete report.
Watch for claims the shown work does not support: a diagnosis of root
cause with no run demonstrating it, a "happens every time" with only one
run shown, or certainty language ("guaranteed", "definitely") standing
in for a missing artifact.

## Comms

Where it lives: eval — the candidate claim comment's full text, and
the repo-facts block's "contribution policy" line for any AI-disclosure
requirement. Live — the draft claim comment (and repro comment, once
written), and the repo's CONTRIBUTING doc or README for the same policy
line; `scope.md`'s house rules note anything specific to the course's
Path Review repo.

What good looks like: the claim comment names something specific to
this issue (the behavior, a file, a hypothesis) rather than reading as
a template that would fit any issue in any repo, and it commits to a
next step rather than just asking to be assigned. Where the repo's
policy explicitly requires disclosing AI assistance, at least one of
the comments says so in plain language ("used an AI assistant to help
draft/reproduce this") — a policy that is silent, permissive, or merely
cautions reviewers about AI-generated PRs does not require this.
