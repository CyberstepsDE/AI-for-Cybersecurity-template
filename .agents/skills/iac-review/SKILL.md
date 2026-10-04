---
name: iac-review
description: Review infrastructure-as-code files (for example Terraform) for configuration weaknesses, then compare the agent's findings with a deterministic scanner such as Checkov, row by row. Use for the cloud configuration exercises.
---

# /iac-review - the agent proposes, the scanner checks

Load `rules/evidence.md` and `rules/what-checks-prove.md` first.

## Input

Terraform files in `samples/` or given by the instructor. These describe cloud resources;
nothing is deployed in this exercise, so no cloud account is needed.

## Steps

1. **Read the files** and list the resources they create: type, name, file and line.
2. **Agent pass.** For each resource, propose configuration weaknesses, each with the
   file and line, the setting, why it matters and the fix. Example of the shape: "storage
   bucket at `main.tf:12` allows public read; anyone on the internet can list its files;
   set public access to blocked".
3. **Scanner pass.** Run the scanner your instructor names, with the command shown to the
   person first, and save its full output in `evidence/`. For Checkov the instructor's
   handout gives the exact command and version.
4. **Compare, row by row,** in `templates/gap-analysis.md` (configuration table):
   - found by both: confirmed;
   - found by the agent only: check it by reading the setting yourself; the scanner may
     lack a rule, or the agent may be wrong;
   - found by the scanner only: the agent missed it; note which kind of weakness.
5. **Map to controls** only where the instructor's control list gives a clear match, and
   say it is a mapping, not a certification.

## What the scanner proves

A passing scanner proves the files match none of its rules. It does not prove the
configuration is safe, and it says nothing about resources outside these files
(`rules/what-checks-prove.md`).

## Do not

- Run `terraform apply` or touch a real cloud account.
- Change the files to make the scanner pass without saying which finding each change
  fixes.
