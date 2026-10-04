---
name: gap-analysis
description: Map an organisation's policy documents to the requirements of a framework such as NIS2 Article 21, with an exact quote as evidence for every match, and list the gaps. Use for GRC exercises with the lab's fictional company policies.
---

# /gap-analysis - requirement, quote, gap

Load `rules/evidence.md` first.

A gap analysis answers: for each requirement, does the organisation's own documentation
show that it is met? The evidence is a quote from the policy. The agent drafts; the
analyst checks every quote, because an auditor signs only what was checked.

## Input

- The requirements: the framework text from its official source, for example NIS2 from
  EUR-Lex. Write the URL and the date you opened it. Do not work from memory or from a
  summary: the article's own wording decides.
- The policies: the fictional company's documents in `samples/` or given by the
  instructor. Never a real employer's internal documents (`rules/data-handling.md`).
- ISO 27001: its full text is sold, not public. Use only control numbers and titles that
  your instructor provides.

## Steps

1. **List the requirements** you will check, one per row, with the article and point.
2. **For each requirement, search the policies** and quote the exact sentence that meets
   it, with the file and section. If nothing meets it, write "no evidence found" and
   name the files searched.
3. **Rate each row:** met, partly met (say what is missing) or gap.
4. **Recommend** one concrete change per gap, written as a policy owner could act on it.
5. **Check every quote.** The person opens each file and confirms the quote is there,
   word for word. Mark the column in `templates/gap-analysis.md`. A quote the model
   produced but nobody found in the file is removed, and noted.

## Do not

- Paraphrase a policy and call it a quote.
- Count a policy that says "we will" as evidence that something is done. Write what the
  text actually commits to.
- Give legal advice. Name the requirement and the gap; whether the organisation falls
  under the law is a question for its legal team.
