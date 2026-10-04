# Connecting the agent to the lab's tools

> **Status:** not yet run end to end. The instructor runs a pilot before session 3; the
> handout then gives the exact host name, your user name and your key. Until then, this
> page shows the shape of the connection and the reasons behind each setting.

## What MCP is

MCP (Model Context Protocol) is a standard way for an agent to use tools that run
somewhere else. A program called an MCP server offers a list of tools, each with a name
and parameters; the agent sees the list and asks the server to run one. Hermes Agent and
Claude Code both speak MCP.

## How the Kali tools fit together

The lab's Kali Linux machine runs Kali's official package `mcp-kali-server` (version
0.0~git20260317.00154c0 on kali.org, page updated 2026-08-25, checked 2026-10-04). It
installs two programs:

| Program | What it does | Default |
|---|---|---|
| `kali-server-mcp` | A small web API that runs the security tools on the Kali machine | listens on 127.0.0.1, port 5000 |
| `mcp-server` | The bridge: speaks MCP to your agent and forwards each call to that API | `--server http://localhost:5000` |

```
your laptop                              lab Kali machine
agent (Hermes or Claude Code)  --SSH-->  mcp-server  -->  kali-server-mcp (127.0.0.1:5000)  -->  tools
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

## Hermes Agent

Add this to Hermes's `config.yaml` (Windows: `%LOCALAPPDATA%\hermes\config.yaml`, Mac:
`~/.hermes/config.yaml`), with the values from the handout:

```yaml
mcp_servers:
  kali:
    command: "ssh"
    args: ["-T", "<user>@<lab-kali-host>", "mcp-server", "--server", "http://127.0.0.1:5000"]
    trust: untrusted
    tools:
      include: [server_health, nmap_scan]
      resources: false
      prompts: false
```

- `trust: untrusted` makes Hermes ask you before every call of a tool that the server
  does not mark as read-only. The Kali bridge marks none, so every call asks. The default,
  `full`, asks nothing.
- `tools: include:` registers only the listed tools. Replace the example list with the
  instructor's list for the session. Never include `execute_command`. The two `false`
  lines also leave out the helper tools Hermes would otherwise add for the server's
  resources and prompts.
- Without the `trust` line, Hermes treats the server as fully trusted and asks nothing,
  without any warning.

Hermes's approval prompt names the tool and the server, but not the target or the
options. Make the agent show them in the chat before every call:

```
hermes config set display.tool_progress verbose
```

Deny any Kali call whose target and options you have not seen.

Check the connection with `hermes mcp test kali`.

A note on words: `trust: untrusted` here means "ask before every call". Elsewhere
(`rules/untrusted-input.md`) "untrusted" means "text that may try to steer the agent".
Both apply to the Kali tools: you approve the call, and you treat its output as data.

## Claude Code

```
claude mcp add --transport stdio kali -- ssh -T <user>@<lab-kali-host> mcp-server --server http://127.0.0.1:5000
```

Name the server `kali`. This folder's `.claude/settings.json` then:

- denies `execute_command` on any server, whatever its name;
- makes Claude Code ask before every other `kali` tool, also after you answered "don't
  ask again", because an ask rule outranks an allow rule;
- asks before every `ssh` command;
- starts terminal sessions in Manual mode, where Claude Code asks before acting, instead
  of auto mode, where a classifier decides for you.

Claude Code registers all 12 tools of the bridge; it cannot filter them like Hermes.
Before you approve a call, find its target and options; if you cannot see them, deny
it and ask the agent to state them. For the web application assessment in session 3
use Hermes, because Claude Code moves penetration-testing requests to an older model.
Check the server with `claude mcp get kali`.

## First call

Ask the agent to call `server_health` and nothing else. **It must ask for your approval.**
If it runs without asking, stop: the `trust` line or the server name is wrong. Read the
answer yourself. Then follow `/assess-webapp`, which starts with `scope.yaml`.

## Sources (checked 2026-10-04)

- Kali package page: https://www.kali.org/tools/mcp-kali-server/
- Upstream repository: https://github.com/Wh0am123/MCP-Kali-Server (README, `server.py`,
  `client.py`)
- Hermes MCP settings, `trust` and tool filters:
  https://hermes-agent.nousresearch.com/docs/user-guide/features/mcp and
  https://hermes-agent.nousresearch.com/docs/reference/mcp-config-reference
- Hermes approval prompt and tool display: Hermes source, `tools/mcp_tool_handlers.py`
  (commit af90026a); https://hermes-agent.nousresearch.com/docs/user-guide/configuration
- Claude Code: https://code.claude.com/docs/en/mcp,
  https://code.claude.com/docs/en/permissions and
  https://code.claude.com/docs/en/permission-modes
