---
name: review-findings
description: Check every finding in a note or report against the record it cites, as a reviewer who did not write it. Returns READY or NOT READY with located problems. Use before a report or incident note is handed in or shared.
---

# /review-findings - a second mind checks the evidence

Load `rules/evidence.md` and `rules/what-checks-prove.md` first.

**Run this in a fresh session,** not in the one that wrote the findings. The author
checks whether the text says what they meant; the reviewer checks whether the records
say it too. One mind does not ask both questions well. If you have a second agent or a
different model, use it: a different model has different blind spots.

## Input

The path to a note or report in `evidence/` or `reports/`.

## Steps

For every finding, in order:

1. **Open the record it cites.** The file and lines, the saved query output, the tool
   output. Do not trust the quote in the report: read the source.
2. **Compare.** Do the numbers, times, addresses and account names in the finding match
   the record exactly? Count again with your own read-only command.
3. **Check the claim against the check.** Could the record have shown the problem the
   finding claims? Use the table in `rules/what-checks-prove.md`. "Port open" does not
   support "vulnerable"; "no alert" does not support "nothing happened".
4. **Check the outside facts.** A CVE, an ATT&CK technique, a NIS2 article or a tool's
   behaviour: is the source named and dated? Open it if it is not.
5. **Give each finding one label:**
   - **confirmed:** the record shows exactly this;
   - **overstated:** the record shows less than the text claims; give the wording the
     record supports;
   - **not supported:** the record does not show it, or no record is cited;
   - **wrong:** the record shows something different.

## Report

A verdict first: **READY** (every finding confirmed, or overstated ones reworded) or
**NOT READY**. Then the findings that are not confirmed, worst first, each with the
location in the report, the record you opened, what it actually shows and the smallest
fix. Then one line on what the report does not cover and should say so.

If everything is confirmed, list what you opened and counted. "Looks fine" with no
method is not a review.

## Do not

- Fix the report. You report; the author fixes; you read again.
- Add new findings of your own as if they were checked. A new lead goes in a separate
  section called "leads for the author".
