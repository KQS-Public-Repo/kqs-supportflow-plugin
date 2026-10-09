# KQS SupportFlow for Claude

Connects Claude to the **KQS SupportFlow Client** running on the same PC, so Claude can list your stations, read their
telemetry, and run commands on them. Every command that reaches a station is signed by your ControlKey in the SupportFlow
Client - this plugin holds no keys, no tokens and no settings of its own.

## Before you start

1. Install the **SupportFlow Client 1.5.18.1 or later** and sign in: https://www.koquelani.com/services/clientdownload
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

**The Client is not on your PATH?** (an install older than 1.5.18.1, or a copy run from a folder) Run the current
`KQS_SupportFlow_setup.exe`, which adds the Client to your PATH, then fully quit and restart Claude.

## What this plugin runs

One MCP server, `kqs-support`, started as `KQS_SupportFlow.exe --mcp-stdio`. That is the installed SupportFlow Client in
bridge mode: it reads MCP messages from Claude on stdin, passes each one to the Client's own connection on this PC
(`http://127.0.0.1:<port>/mcp`) with the sign-in the Client saved for this Windows user, and writes the answers to
stdout. It opens no ports and installs nothing. The plugin itself contains no programs, keys or tokens - only this
configuration, a setup check (`/kqs-supportflow:setup`) and documentation.

**Not signed in yet?** The tools answer that the SupportFlow Client is not signed in for this Windows user. Open the Client,
sign in, then restart Claude so it reconnects to the tools.

**Already used the Client's Install MCP for Claude Code?** Then Claude Code already has a `kqs-support` server, and this
plugin would add a second copy of the same tools. Use one or the other: remove the Client's entry (gear → AI Connectors →
Claude Code → **Remove**) or don't install the plugin.

The SupportFlow Client (1.5.18.0 and later) notices this plugin. It shows Claude Code as **● via plugin**, doesn't
offer to install its own copy, and asks before adding one if you press Install anyway. If both are present, it shows
**● installed twice**.
