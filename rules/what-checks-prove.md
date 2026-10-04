# What each check proves, and what it cannot

> **Load this when:** you are about to call something confirmed, clean, safe or done.

## The rule

**Never report a conclusion on the strength of a check that could not have detected
the problem you are worried about.** Every check answers one narrow question.

| Check | What it proves | What it does NOT prove |
|---|---|---|
| A scanner found nothing | It found none of the patterns it knows | That there is nothing to find |
| The model says "malicious" | The model produced that word | That the event was malicious; the record decides |
| An alert fired | A rule matched | That an attack happened |
| No alert fired | No rule matched | That nothing happened |
| A login succeeded after failures | The account was used | That the same person made both attempts |
| A port is open | Something answers on it | Which service, which version, whether it is vulnerable |
| A CVE matches a version string | The version string matches | That the vulnerable code path is reachable here |
| The report reads well | Somebody wrote fluent text | That any of it is true |

## What to do instead

1. Name the failure you are worried about in one sentence.
2. Ask which check would have caught it. If none would, you do not have evidence yet.
3. Get the evidence where the risk lives: the raw record, not the summary of it.
4. Say what you did not check. "The log covers 02:00 to 03:00 only; earlier activity
   was not examined" is a complete and useful sentence.
