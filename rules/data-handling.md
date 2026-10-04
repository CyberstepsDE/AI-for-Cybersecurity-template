# Data handling and secrets

> **Load this when:** you are about to send data to the model, save a file, commit, or
> touch anything that authenticates.

## What leaves the laptop

Everything the agent sends to the model goes to the model provider: the prompt, the
files you let it read, the tool outputs. With OpenRouter it goes to OpenRouter and the
company that hosts the model; with a local model through Ollama it stays on the laptop.

**So:** work only with synthetic data (`samples/`) or data from the lab. Never real
logs from an employer, real mailboxes or real personal data. If you need to analyse
something sensitive at work, that is a decision for your employer, and a local model is
one of the options.

## Secrets

- No key, token, password or connection string goes into a file that gets committed:
  not in a note, not in `evidence/`, not in a report, not in a commit message.
- Your OpenRouter key lives in the agent's own settings, never in this folder.
- A secret that reached git is burned: tell the instructor, who replaces it. Deleting
  the line does not remove it from the history.
- Before every commit, read `git diff --cached` and stage files by name, never with
  `git add -A`.

## Before you publish this folder

If you push your copy to a public repository as a portfolio, check `evidence/` and your
notes first: lab addresses, accounts or screenshots with sessions do not belong there.
`scope.yaml` is excluded by `.gitignore` for that reason.
