# KQS SupportFlow for Claude

Connects Claude to the **KQS SupportFlow Client** running on the same PC, so Claude can list your stations, read their
telemetry, and run commands on them. Every command that reaches a station is signed by your ControlKey in the SupportFlow
Client - this plugin holds no keys, no tokens and no settings of its own.

## Before you start

1. Install the **SupportFlow Client 1.5.19 or later** and sign in: https://www.koquelani.com/services/clientdownload
   The installer puts the Client on your PATH, which is how this plugin finds it.
2. Leave the Client running and connected - Claude reaches your stations through it.
3. Restart Claude after installing the Client, so it sees the updated PATH.

That is all: no environment variables, no token to copy. The plugin starts `KQS_SupportFlow.exe --mcp-stdio`, and that
reads the sign-in you made in the Client - for your Windows user only.

## Install

```
/plugin marketplace add KQS-Public-Repo/kqs-supportflow-plugin
/plugin install kqs-supportflow@koquelani-supportflow
```

The tools appear under the `kqs-support` server of this plugin. In the Claude desktop app you can also install it from the
plugin directory.

**Something not working?** Run `/kqs-supportflow:setup`. It checks that the Client is installed, signed in and connected,
and tells you what to fix.

**The Client is not on your PATH?** (an install older than 1.5.19, or a copy run from a folder) Run the current
`KQS_SupportFlow_setup.exe`, which adds the Client to your PATH, then fully quit and restart Claude.

**Not signed in yet?** The tools answer that the SupportFlow Client is not signed in for this Windows user. Open the Client,
sign in, then restart Claude so it reconnects to the tools.

**Already used the Client's Install MCP for Claude Code?** Then Claude Code already has a `kqs-support` server, and this
plugin would add a second copy of the same tools. Use one or the other: remove the Client's entry (gear → AI Connectors →
Claude Code → **Remove**) or don't install the plugin.

The SupportFlow Client (1.5.18 and later) notices this plugin. It shows Claude Code as **● via plugin**, doesn't
offer to install its own copy, and asks before adding one if you press Install anyway. If both are present, it shows
**● installed twice**.

## What this plugin runs

One MCP server, `kqs-support`, started as `KQS_SupportFlow.exe --mcp-stdio`. That is the installed SupportFlow Client in
bridge mode: it reads MCP messages from Claude on stdin, passes each one to the Client's own connection on this PC
(`http://127.0.0.1:<port>/mcp`) with the sign-in the Client saved for this Windows user, and writes the answers to
stdout. It opens no ports and installs nothing. The plugin itself contains no programs, keys or tokens - only this
configuration, a setup check (`/kqs-supportflow:setup`) and documentation.

## Where your data goes

- **Claude to the Client:** every tool call, with its arguments (for example a command to run on a station), goes to the
  SupportFlow Client on this PC.
- **The Client to KQS SupportFlow:** the Client carries the call over its encrypted connection to the KQS SupportFlow service,
  run by Koquelani Systems (www.koquelani.com). The service passes it to the station the call names. The servers are
  listed under [Servers and ports](#servers-and-ports).
- **The answer comes back the same way, to Claude.** It can include telemetry, command output, file listings, files you
  ask for and screenshots.
- **ControlKey:** commands sent to a station are signed with your ControlKey in the Client, and a station that uses ControlKey
  checks the signature before it acts.

The plugin sends nothing anywhere else. The KQS SupportFlow service and the Client are covered by Koquelani's own terms;
see https://www.koquelani.com/services.

## Servers and ports

The plugin itself makes no network connections: it talks only to the SupportFlow Client on this PC. The Client makes every
connection outward and directly, never through a web proxy. It opens no port that can be reached from outside this PC, so
Windows Firewall needs no rule.

If your network filters outgoing traffic, allow these:

| Server | Outbound port | What for |
|---|---|---|
| `sso1.koquelani.com` | TCP 9443 | The Client's encrypted connection to KQS SupportFlow. Every tool call travels this way. This is the primary server. |
| `sso2.koquelani.com` | TCP 9443 | The same connection, when sso1 does not answer. |
| `sf01-east.koquelani.com` | TCP 9443 | The same connection, when neither of the above answers. |
| The same three servers | TCP 443 (HTTPS) | Two uses only. When every server has failed on 9443, the Client tries 443 to tell you whether your network is blocking 9443. When you update your Endpoints, it downloads the new Endpoint build and checks it before signing the update. |

- **More servers:** the service can name other servers for the Client to use, always under `koquelani.com`. A rule for
  `*.koquelani.com` on TCP 9443 and TCP 443 therefore will not need changing later.
- **Names:** the Client looks names up through your normal DNS.
- **Web pages:** sign-up, Help, the portal and downloads at `www.koquelani.com` open in your web browser, over ordinary HTTPS
  (TCP 443).

### Local MCP access (this PC only)

The SupportFlow Client serves MCP on this PC at **`http://127.0.0.1:9191/mcp`**.

| | |
|---|---|
| Address | `127.0.0.1` (loopback) only. Nothing outside this PC can reach it. |
| Port | **TCP 9191** by default. To change it, edit **Local MCP port** in the Client's settings (gear → Server Cfg). This plugin always uses the port the Client saved. |
| Path | `/mcp` |
| Sign-in | Every request needs your account's MCP token. This plugin's bridge adds it from the Client's saved sign-in, so you never copy it. For other AI apps, the Client's **AI Connectors** (gear → AI Connectors) set them up with the token. |

The browser viewer uses **TCP 9788**, also on `127.0.0.1` only. Neither port is opened to the network. If your security
software asks about either one, allow it.
