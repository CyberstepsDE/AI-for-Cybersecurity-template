---
name: report
description: Turn checked findings into a short report a busy reader can act on - verdict first, every finding with its record, what was not checked, and a fix that matches the cause. Use at the end of an assessment, an investigation or a gap analysis.
---

# /report - verdict first, evidence always

Load `rules/evidence.md` and `rules/less-is-more.md` first.

## Pick the template

| Work | Template |
|---|---|
| Assessment of the lab web application | `templates/assessment-report.md` |
| Log or SIEM investigation, threat hunt | `templates/incident-note.md` |
| Policy gap analysis or configuration review | `templates/gap-analysis.md` |

## Steps

1. **Collect only checked findings.** Read the notes in `evidence/`. A finding enters the
   report with its record, or it enters the section for hypotheses and leads, labelled
   as such. Nothing is upgraded on the way.
2. **Write the verdict first,** in two or three sentences: what was found, how sure, and
   what the reader should do now.
3. **Fill the template.** Each finding: what was observed, the record, what it means,
   confidence and why, the fix. The fix answers the cause, not the symptom: "validate
   input on the server" answers an injection; "block this one request" does not.
4. **Write the limits.** What was in scope and what was not, which time range, which
   tools, what was not checked. A reader who acts on the report needs to know where it
   stops.
5. **Show the draft to the person.** Save it to `reports/<date>-<short-name>.md` after
   they agree.
6. **Suggest `/review-findings`** in a fresh session before the report is handed in.

## Writing

Plain language for a reader who was not there. Define a term the first time it is used.
No finding without an action for the reader. No adjectives doing the work of evidence:
"critical" needs a reason, "confirmed" needs a record.

## Do not

- Include lab addresses, accounts or session data in a report meant to leave the lab
  (`rules/data-handling.md`).
- Present a model's suggestion as a result. A suggestion that was not checked is a
  hypothesis.
