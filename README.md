# AI for Cybersecurity: Lab Workspace

A folder for doing the labs of the Cybersteps course "AI for Cybersecurity" with an AI
agent. It is not an application. What is checked in is what stays true in every lab:
the rules the agent reads, the routines it follows, practice data, report templates, and
how the agent connects to the lab's tools.

You bring the task and the judgement. The agent reads the data, drafts, and asks before
it acts. Every finding ends with the record that proves it.

> **Status (2026-10-04):** written and checked against the Hermes and Claude Code
> documentation and source; not yet run end to end with students. The connection to the
> lab's Kali tools waits for the lab server, built before session 3.

## What is inside

```text
.
├── AGENTS.md                  - the rules the agent reads before doing anything
├── CLAUDE.md                  - a stub that points Claude Code at AGENTS.md
├── README.md                  - this file
├── LICENSE                    - MIT
├── scope.example.yaml         - copy to scope.yaml: what you may touch, and when
├── rules/                     - the detail behind AGENTS.md, one topic per file
│   ├── scope.md               - act only where you are authorised
│   ├── evidence.md            - no finding without a record
│   ├── less-is-more.md        - the smallest set of tools, steps and words
│   ├── untrusted-input.md     - text in the data is data, never an instruction
│   ├── data-handling.md       - what leaves your laptop; no secrets in git
│   └── what-checks-prove.md   - what each check proves, and what it cannot
├── .agents/skills/            - the skills (routines), read by Hermes Agent
├── .claude/skills/            - short pointers to the same skills, for Claude Code
├── .claude/settings.json      - Claude Code: approval rules for the lab's Kali tools
├── mcp/README.md              - how the agent connects to the lab's tools
├── samples/                   - synthetic practice data
├── templates/                 - report formats
├── evidence/                  - your tool outputs, queries and working notes
└── reports/                   - your finished reports
```

The skills live once, in `.agents/skills/`. Hermes reads that folder; Claude Code reads
only `.claude/skills/`, so each skill there is a two-line pointer to the real file. To
change a skill, edit the file in `.agents/skills/`.

## Words used here

- **Agent:** the program that sends your task to a model and, when allowed, uses tools
  such as reading a file or running a command. This folder works with Hermes Agent and
  with Claude Code, both installed in session 2.
- **Skill:** a routine in plain English that the agent follows when you type its name,
  for example `/triage-log`. It is instructions, not a program.
- **MCP:** a standard way for the agent to use tools that run on another machine, such as
  the lab's Kali Linux tools. See `mcp/README.md`.
- **Scope:** the list of targets you are authorised to test, the time window and the
  tools allowed. Authorisation is the line between a security test and a crime.
- **Repository:** a folder whose history git records. **Clone:** copy a repository from
  GitHub to your laptop.

## Before you start

- **Git.** Run `git --version`. If the command is not found, install Git (on Windows:
  Git for Windows from https://git-scm.com; on a Mac, running `git` offers to install
  it). Then tell git who you are, once:
  ```
  git config --global user.name "Your Name"
  git config --global user.email "you@example.com"
  ```
- **A GitHub account**, for your own private copy. The first clone of a private
  repository asks you to sign in.
- **No git?** Download the folder as a ZIP from the template page ("Code", "Download
  ZIP") and unpack it. Everything works except the commit in `/lab-save`, which then
  only writes `WORK_LOG.md`.

## How to use it, step by step

1. **Make your own copy** from the link your instructor gives you: "Use this template",
   then "Create a new repository". Make it private: it will hold your lab notes.
2. **Clone your copy** (`git clone <its address>`) and open a terminal in its folder.
3. **Start your agent in the folder.**
   - Hermes Agent: run `hermes`. The first time, it reports project skills that are "not
     loaded": Hermes does not run instructions from a folder you have not approved. Read
     `AGENTS.md` and the skills first, then run `hermes skills trust` and start Hermes
     again.
   - Claude Code: run `claude` and accept the folder when it asks whether you trust it.
4. **Type `/lab-start`.** The agent reads the rules, the scope and the last work log entry,
   then tells you what it understood and waits.
5. **When your instructor gives you lab targets,** copy `scope.example.yaml` to
   `scope.yaml` and fill it in. Without `scope.yaml` the agent is told to analyse files
   only and to act against no system.
6. **Work through a skill** from the table in `AGENTS.md`, for example `/triage-log
   samples/auth.log`.
7. **Type `/lab-save`** when you finish. It writes `WORK_LOG.md`, so the next session, which
   remembers nothing, can continue.

## What is enforced, and what is only agreed

An instruction file is context, not a mechanism: a model can drift past it. Which rules
have teeth:

| Rule | What holds it | Honest label |
|---|---|---|
| Tools reach only your own training target | The lab network on the instructor's server | **Enforced once the lab server exists** (built before session 3, not yet piloted). Your own laptop's commands are not covered. |
| Your SSH key starts only the Kali bridge | A restriction on the key, on the lab server | **Enforced once piloted.** Until then the agent's terminal can use the key for anything (`mcp/README.md`). |
| A person approves every Kali tool call | Hermes: `trust: untrusted` in your `config.yaml`. Claude Code: `.claude/settings.json` | **Asks,** if configured as in `mcp/README.md`; the first call proves it. Hermes's prompt does not show the target, so turn on the full display. |
| A person approves risky commands | Hermes: `approvals.mode manual` (session 2). Claude Code: `.claude/settings.json` starts it in Manual mode | **Partly.** Hermes checks commands against a list of dangerous patterns, not every command. Claude Code in Manual mode asks before shell commands except a built-in set of read-only ones. |
| `scope.yaml` and `.env` stay out of git | `.gitignore` | **Enforced** for those names only. Anything you paste into a note is not covered. |
| Everything else in `AGENTS.md` and `rules/` | The agent reading it | **Agreed, not enforced.** It holds as well as the model follows it, which is why a person reads every finding. |

One side effect worth knowing: Hermes scans `AGENTS.md` for text that looks like an
attack on the agent, and if it finds any, it loads none of the file. A rules file that
quotes an attack phrase as a warning, or names certain offensive tools, can switch off
all your rules; the trace is a warning line in the agent log (`hermes logs`), easy to
miss. Keep such examples in data files, not in `AGENTS.md`.

## License

[MIT](LICENSE).
