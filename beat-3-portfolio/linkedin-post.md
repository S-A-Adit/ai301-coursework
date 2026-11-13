I opened a fix for WordPress's Gutenberg editor, an open source project. It adds an automatic check that stops a known software problem from quietly coming back.

The project, Gutenberg, once had a problem where its list of software parts pulled in four different versions of the same browser-data package. When I investigated, that problem had already been fixed, but nothing would warn the team if it returned. So I wrote a check that fails loudly if it does, plus tests that prove it works.

Where it stands today: the pull request (my proposed change) is open and waiting for review. A maintainer asked whether the original problem could still be reproduced. I checked, answered honestly that it couldn't, and explained why the safety check might still be worth having.

What it taught me:
✅ Check whether a problem still exists before you build a fix.
✅ Write the test first, so you can see it fail before it passes.
✅ Wait for the owner's answer before investing the work. I didn't, and I'd do it differently now.

https://github.com/WordPress/gutenberg/pull/86097

#OpenSource #SoftwareEngineering #WordPress
