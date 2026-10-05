# AI for Cybersecurity: Lab Workspace

A folder for doing the labs of the Cybersteps course "AI for Cybersecurity" with an AI
agent. It is not an application. What is checked in is what stays true in every lab:
the rules the agent reads, the routines it follows, practice data, report templates, and
how the agent connects to the lab's tools.

You bring the task and the judgement. The agent reads the data, drafts, and asks before
it acts. Every finding ends with the record that proves it.

> **Status (2026-10-05):** written and checked against the Claude Code documentation; not
> yet run end to end with students. The course agent is Claude Code. The connection to the
> lab's Kali tools over SSH waits for the lab server, built before session 3.

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
├── .claude/skills/            - the skills Claude Code runs, such as /lab-start
├── .agents/skills/            - the same skills, for any other agent that reads them
├── .claude/settings.json      - Claude Code: Manual mode and approval rules for Kali
├── openrouter.example.json    - copy to openrouter.json, add your key: claude --settings openrouter.json
├── ollama.json                - run a local model: claude --settings ollama.json
├── mcp/README.md              - how the agent reaches the lab's Kali tools (session 3)
├── samples/                   - synthetic practice data
├── templates/                 - report formats
├── evidence/                  - your tool outputs, queries and working notes
└── reports/                   - your finished reports
```

Each skill's full instructions live once, in `.agents/skills/`; the matching file in
`.claude/skills/` is a short pointer Claude Code reads. To change a skill, edit the file
in `.agents/skills/`.

**New lab data arrives as a download.** A copy made from this template is a fresh start,
so later changes here do not reach it. When a session needs new practice data, the
instructor gives it as a ZIP; unpack it into `samples/` and commit it with `/lab-save`.

## Words used here

- **Agent:** the program that sends your task to a model and, when allowed, uses tools
  such as reading a file or running a command. The course agent is Claude Code, installed
  in session 2. Any agent that reads `AGENTS.md` follows the same rules.
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
  repository asks you to sign in. If git asks for a password, enter a GitHub personal
  access token instead: GitHub no longer accepts passwords for git.
- **No git?** Download the folder as a ZIP from the template page ("Code", "Download
  ZIP") and unpack it. Everything works except the commit in `/lab-save`, which then
  only writes `WORK_LOG.md`.

## How to use it, step by step

1. **Make your own copy** from https://github.com/CyberstepsDE/AI-for-Cybersecurity-template:
   press "Use this template", then "Create a new repository". Make it private: it will
   hold your lab notes.
2. **Clone your copy** (`git clone <its address>`) and open a terminal in its folder.
3. **Always start Claude Code in this folder,** so it gets the course rules, skills and
   settings. The folder stays the same; only how you start it picks the model provider:
   - `claude` uses your own Claude account.
   - `claude --settings openrouter.json` uses OpenRouter. First copy
     `openrouter.example.json` to `openrouter.json` and paste your key; `openrouter.json`
     stays out of git.
   - `claude --settings ollama.json` uses a model on your own laptop (needs Ollama and a
     pulled model).

   Accept the folder when it asks whether you trust it. Read `AGENTS.md` and the skills
   yourself before you rely on them.
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
| Tools over MCP reach only your own training target | The lab network on the instructor's server | **Enforced once the lab server exists** (built before session 3, not yet piloted). This covers the instructor's MCP tools only. A tool you run inside your own Kali VM reaches whatever that VM can reach. |
| Your SSH key starts only the Kali bridge | A restriction on the key, on the lab server | **Enforced once piloted.** Until then the agent's terminal can use the key for anything (`mcp/README.md`). |
| A person approves every Kali tool call | `.claude/settings.json` (an ask rule on the `kali` server) | **Asks,** if configured as in `mcp/README.md`; the first call proves it. |
| A person approves risky commands | `.claude/settings.json` starts terminal sessions in Manual mode | **Partly.** In Manual mode Claude Code asks before shell commands except a built-in set of read-only ones. A "don't ask again" you click for one command is remembered for that command. |
| `scope.yaml` and `.env` stay out of git | `.gitignore` | **Enforced** for those names only. Anything you paste into a note is not covered. |
| Everything else in `AGENTS.md` and `rules/` | The agent reading it | **Agreed, not enforced.** It holds as well as the model follows it, which is why a person reads every finding. |

## Running several agents (optional)

One task at a time needs only one Claude Code. When you want several agents at once, each
on a different model for a different job, a runtime such as [herdr](https://herdr.dev)
(Apache 2.0) runs several Claude Code sessions side by side and can reach other machines
over SSH. It is a convenience layer, not a different agent: each session still reads this
folder and follows these rules. We only show it; the course does not require it.

## License

[MIT](LICENSE).
