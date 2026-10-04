# Evidence: no finding without a record

> **Load this when:** you are about to write a finding, a verdict, a conclusion or a
> number into a note or a report.

## The rule

**Every finding cites the record it rests on.** A record is something another person
can open and check: a file and line number, a query and the rows it returned, a tool
output saved in `evidence/`. A statement without a record is a hypothesis and is
labelled "hypothesis".

## Why

A language model writes fluent, confident text whether or not it is right. In security
work a confident wrong sentence costs more than an honest "not determined": somebody
acts on it. The record is how a reader tells the two apart.

## How to write a finding

| Part | Example |
|---|---|
| What was observed | 40 failed SSH logins, five each for eight account names, from 198.51.100.23 between 02:14:02 and 02:16:46, then a disconnect for too many failures |
| Record | `samples/auth.log`, lines 4-44 |
| What it may mean | an automated password-guessing attempt |
| Other explanations | a misconfigured script of our own (less likely: the address is not one of ours, and it tries eight generic account names, not one real one) |
| Confidence | high, medium or low, and why |

## Facts about the outside world

A CVE, a MITRE ATT&CK technique, a tool's behaviour or a vendor's claim is checked at
its source before it goes into a report: the NVD entry or the vendor advisory for a CVE,
attack.mitre.org for a technique, the tool's own documentation. Write the source and the
date you checked it. Your memory, and the model's, is not a source.

## Trust nothing until checked

Not the alert's title, not the tool's summary line, not another agent's report, not
your own conclusion from ten minutes ago. Attack your own negative claims hardest: "no
other host was affected" and "nothing was exfiltrated" are the statements most
expensive to get wrong.
