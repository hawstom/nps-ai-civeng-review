# NPS Plan Review Kit: Quick Start

A starter workspace for reviewing civil plan sets with Claude Code, built from Tom Haws's review-assist trial (Aug-Sep 2026).

## Setup (once)

1. Install Claude Code: https://claude.com/claude-code
2. Install Python 3, then: `pip install pymupdf pypdf numpy matplotlib`
3. Unzip this folder anywhere, e.g. `C:\PlanReviews\`

## Try it

Open a terminal in the unzipped folder, run `claude`, and type:

    Review LDLPM-practice

Claude reviews the 6-sheet civil set, back-checks it against the round-1 markups, and writes sticky notes into the PDF. Open the PDF in your viewer, then compare it with the finished review in `example\LDLPM\2026-09-29\LDLPM-CIVIL.pdf`.

## Your own jobs

Put each job in a folder named for it, with one dated subfolder per submittal:

    MyJob\2026-10-05\MyJob-GD.pdf

Then type `Review MyJob`. Claude annotates that PDF in place (no copies). Reply to its stickies in the PDF, tell it you did, and correct it in chat. **Your corrections are what make it better.** Claude saves lessons to its own memory as you work.

## What's inside

- `CLAUDE.md`: the review procedure, markup format, NPS standards and lessons learned. Claude reads it automatically every session. Edit freely.
- `example\LDLPM\`: a finished two-round review (Tom's green notes + Claude's yellow notes).
- `LDLPM-practice\`: the same job with Claude's round-2 notes removed.
- `.claude\settings.json`: lets Claude run Python without asking every time.

Questions: ask Tom.
