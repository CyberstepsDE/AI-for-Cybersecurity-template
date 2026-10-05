# Connecting the agent to the lab's tools

> **Status:** not yet run end to end. The instructor runs a pilot before session 3; the
> handout then gives the exact host name, your user name and your key. Until then, this
> page shows the shape of the connection and the reasons behind each setting.

## What MCP is

MCP (Model Context Protocol) is a standard way for an agent to use tools that run
somewhere else. A program called an MCP server offers a list of tools, each with a name
and parameters; the agent sees the list and asks the server to run one. Claude Code
speaks MCP.

Session 2 keeps Kali simple: you install Claude Code inside your own Kali virtual machine
and it uses Kali's tools in the terminal, with you approving each command. This page is
the next step, built for session 3: reaching the instructor's Kali tools from your laptop
over SSH, with the allowed targets fixed by the lab network.

## How the Kali tools fit together

The lab's Kali Linux machine runs Kali's official package `mcp-kali-server` (version
0.0~git20260317.00154c0 on kali.org, page updated 2026-08-25, checked 2026-10-04). It
installs two programs:

| Program | What it does | Default |
|---|---|---|
| `kali-server-mcp` | A small web API that runs the security tools on the Kali machine | listens on 127.0.0.1, port 5000 |
| `mcp-server` | The bridge: speaks MCP to your agent and forwards each call to that API | `--server http://localhost:5000` |

```
your laptop                     lab Kali machine
Claude Code  --SSH-->  mcp-server  -->  kali-server-mcp (127.0.0.1:5000)  -->  tools
```

## Read this before you connect

- **The API runs whatever it is asked to run, and has no login.** Its source has a route
  for running any command and no authentication code (upstream repository
  `Wh0am123/MCP-Kali-Server`, read 2026-10-04). Anyone who can reach its port can run
  commands on the Kali machine. That is why it stays on 127.0.0.1 and you reach it only
  through SSH, as the upstream author recommends; opening it to the network is what the
  author calls "STRONGLY DISCOURAGED".
- **The tool list is not a safety boundary.** The bridge offers 12 tools: `server_health`,
  `nmap_scan`, `gobuster_scan`, `dirb_scan`, `nikto_scan`, `sqlmap_scan`, `wpscan_analyze`,
  `enum4linux_scan`, three more for password and exploitation work, and `execute_command`,
  which runs any shell command. Most tools also accept free-form extra arguments. So the
  real boundaries are three: the lab network, which only lets traffic reach the training
  targets; your approval before every call; and `scope.yaml`.
- **Connect only the tools the session needs.** Your instructor lists them. Every extra
  tool is something the agent can misuse or be tricked into using
  (`rules/less-is-more.md`).
- **Your SSH key is also a way around the tool list.** The agent has its own terminal.
  With a key that logs in without a password, it could run any command on the Kali
  machine over SSH and skip the bridge entirely. The lab server closes this by allowing
  your key to start the bridge and nothing else; until the instructor has confirmed that
  in the pilot, treat every SSH command the agent proposes as a request to run anything.

## What the lab server must provide (instructor, decided in the pilot)

- Your SSH key may start only the bridge (an OpenSSH `command=` restriction on the key),
  not a shell.
- Students do not share one API: every student gets their own Kali machine or their own
  API instance, because the API has no login and runs commands as the user it runs as.
- The lab network lets the tools reach only your own training instance, not another
  student's.

Until these are confirmed, the honest status of the Kali connection is "not yet safe to
use".

## Before the first connection

1. You can log in to the lab Kali machine with the SSH key from the handout, without a
   password prompt: the agent starts SSH in the background, where nobody can type one.
2. Connect once by hand (`ssh <user>@<lab-kali-host>`) and accept the host key after
   comparing its fingerprint with the one in the handout. Then log out.

## Connect Claude Code (session 3)

Run this in the course folder, with the values from the handout:

```
claude mcp add --transport stdio kali -- ssh -T <user>@<lab-kali-host> mcp-server --server http://127.0.0.1:5000
```

Run it inside the course folder, so the server is added for this project. Name the server
`kali`. This folder's `.claude/settings.json` then:

- denies `execute_command` on any server, whatever its name;
- makes Claude Code ask before every other `kali` tool, also after you answered "don't
  ask again", because an ask rule outranks an allow rule;
- asks before every `ssh` command, in Bash and in PowerShell;
- starts terminal sessions in Manual mode, where Claude Code asks before acting, instead
  of auto mode, where a classifier decides for you.

Claude Code registers every bridge tool except the denied `execute_command`. To show the
model only the session's tools, deny the rest by name in `.claude/settings.json` (a deny
on a tool name removes it from the model's view); the instructor's handout lists which
tools the session uses. Before you approve a call, find its target and options; if you
cannot see them, deny it and ask the agent to state them. Check the server with
`claude mcp get kali`.

A note on the model: for authorised penetration testing, Claude Code moves such requests
from its newest models to older ones, and may decline some
(https://code.claude.com/docs/en/model-config, checked 2026-10-05). The instructor
confirms the model that completes `/assess-webapp` in the session 3 pilot.

## First call

Ask the agent to call `server_health` and nothing else. **It must ask for your approval.**
If it runs without asking, stop: the `trust` line or the server name is wrong. Read the
answer yourself. Then follow `/assess-webapp`, which starts with `scope.yaml`.

## Sources (checked 2026-10-05)

- Kali package page: https://www.kali.org/tools/mcp-kali-server/
- Upstream repository: https://github.com/Wh0am123/MCP-Kali-Server (README, `server.py`,
  `client.py`)
- Claude Code MCP, permissions and permission modes:
  https://code.claude.com/docs/en/mcp, https://code.claude.com/docs/en/permissions and
  https://code.claude.com/docs/en/permission-modes
- Claude Code model fallback for offensive-security requests:
  https://code.claude.com/docs/en/model-config
