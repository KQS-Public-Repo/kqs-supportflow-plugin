---
name: setup
description: Check that KQS SupportFlow is installed, signed in and reachable, and walk the user through fixing it if not. Use when the kqs-support tools are missing or failing, or when the user asks how to set up or connect SupportFlow.
---

# KQS SupportFlow setup check

Work through these in order and stop at the first one that fails. Keep the user's steps short and plain.

## 1. Are the tools answering?

Call the `kqs-support` server's `server_status` tool (or `my_organization` if that is not listed).

- **It answers:** tell the user SupportFlow is connected, name the organization or server it reports, and stop.
- **The kqs-support tools are not listed at all, or Claude says the server failed to start:** go to step 2.
- **It returns "not signed in for this Windows user":** go to step 3.
- **It returns "Cannot reach the tunnel" or "Is the SupportFlow connected?":** go to step 4.

## 2. Is the Client installed?

The plugin starts `KQS_SupportFlow.exe`, which the installer puts on the PATH. Tell the user:

1. Download and run **KQS_SupportFlow_setup.exe** from https://www.koquelani.com/services/clientdownload
   (version 1.5.18.1 or later; an older Client must be updated).
2. **Fully quit and restart Claude** afterwards. Programs only see a new PATH when they start.

## 3. Has this Windows user signed in?

The bridge uses the sign-in saved by the Client for the Windows user Claude runs as. Tell the user to open
**KQS_SupportFlow** (Start menu or tray), sign in, and then fully quit and restart Claude so it reconnects to the
tools.

## 4. Is the Client connected?

The Client is installed and signed in but not connected. Tell the user to open **KQS_SupportFlow** from the tray and
press **Connect**, then try again. If it still fails, the Client's log (in its window) says why.

## Duplicate tools

If every SupportFlow tool appears twice, the Client's own Install MCP entry and this plugin are both active. Tell the
user to keep one: in KQS_SupportFlow open gear → AI Connectors → Claude Code → **Remove**, or uninstall the plugin.
