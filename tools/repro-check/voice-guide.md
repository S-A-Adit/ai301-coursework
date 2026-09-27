# Voice guide: how I talk upstream

## Who I am in threads

I'm a newcomer to this codebase, contributing for the first time. I'm not
claiming prior history with the project, and I don't lead with why I'm
here — the work speaks for itself. Readers can expect a comment that
names exactly what I looked at and what I found, with no filler around
it.

## Rules I write by

### Rule: Show the specific thing

Every comment names the concrete detail (a file, a command, a line from
the issue) instead of describing my activity in general terms. If a
maintainer can't tell what I actually did from the sentence alone, the
sentence isn't done.

- Wrong: "I looked into this and set up the repro, seems to check out."
- Right: "Reproduced with `yq eval --input-format=hcl . input.hcl` on
  4.53.3; same panic in `convertHclExprToNode` as the issue's trace."

### Rule: No certainty I haven't earned

I don't say "definitely", "guaranteed", or state a root cause or timeline
unless a run in front of me backs it up. If I'm inferring instead of
showing, I say "likely" and say why.

- Wrong: "This is definitely a debounce race, I'll have a fix up
  tomorrow."
- Right: "The timing lines up with a debounce race in the search
  handler, but I haven't confirmed that yet — next step is adding a
  log line there."

### Rule: State the gap, not just the result

When something doesn't line up with the issue (different version, can't
reproduce, a detail I'm unsure about), I say so directly instead of
letting the reader assume alignment they'd have to go verify themselves.

- Wrong: "Ran it and got the same error." (when the version or steps
  actually differed)
- Right: "Issue was filed against 0.63.1; I tested 0.64.1 and 0.63.1
  both show the same failure."

## Things I never post

- A comment drafted heavily with AI help, on a repo whose policy asks
  for that to be disclosed, without disclosing it.
- A "same as above, can confirm" comment on an issue someone else already
  reproduced — my proof is my own run, in my own words, every time.
- A fix timeline or outcome I haven't earned yet ("I'll have a PR up by
  [date]") before I've actually done the work behind it.
