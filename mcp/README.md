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
```

- `trust: untrusted` makes Hermes ask you before every call of a tool that the server
  does not mark as read-only. The Kali bridge marks none, so every call asks. The default,
  `full`, asks nothing.
- `tools: include:` registers only the listed tools. Replace the example list with the
  instructor's list for the session. Never include `execute_command`.

Check the connection with `hermes mcp test kali`.

## Claude Code

```
claude mcp add --transport stdio kali -- ssh -T <user>@<lab-kali-host> mcp-server --server http://127.0.0.1:5000
```

The server must be named `kali`: this folder's `.claude/settings.json` denies
`execute_command` and makes Claude Code ask before every other `kali` tool, and both
rules match on that name. Check with `claude mcp get kali`.

## First call

Ask the agent to call `server_health` and nothing else. Read the answer yourself. Then
follow `/assess-webapp`, which starts with `scope.yaml`.

## Sources (checked 2026-10-04)

- Kali package page: https://www.kali.org/tools/mcp-kali-server/
- Upstream repository: https://github.com/Wh0am123/MCP-Kali-Server (README, `server.py`,
  `client.py`)
- Hermes MCP settings, `trust` and tool filters:
  https://hermes-agent.nousresearch.com/docs/user-guide/features/mcp and
  https://hermes-agent.nousresearch.com/docs/reference/mcp-config-reference
- Claude Code: https://code.claude.com/docs/en/mcp and
  https://code.claude.com/docs/en/permissions
