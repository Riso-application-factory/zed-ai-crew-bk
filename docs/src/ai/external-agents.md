---
title: External Agents - Zed
description: Install and use ACP-integrated External Agents such as Claude, Codex, Antigravity, OpenCode, Copilot, Cursor, and Pi Coding Agent in Zed.
---

# External Agents

External Agents are agents that integrate with Zed through the [Agent Client Protocol (ACP)](https://agentclientprotocol.com). Zed hosts the thread in the [Agent Panel](./agent-panel.md) and [Threads Sidebar](./parallel-agents.md#threads-sidebar), while the External Agent usually owns its own runtime, auth, model selection, tools, and native configuration.

Use [Terminal Threads](./terminal-threads.md) instead when you want to run a CLI or TUI directly in a terminal-backed thread.

External Agents run through their own process and provider relationship. Billing, legal terms, retention, and data handling are between you and the agent provider. Zed does not charge for External Agents.

For Zed-hosted models and Zed-managed AI features, see [AI Privacy](./privacy-and-security.md) and [Feedback and Training Data](./ai-improvement.md).

## Install from the ACP Registry {#registry}

The ACP Registry is the primary way to install common External Agents in Zed.

Open the registry with {#action zed::AcpRegistry}, or open [Agent Settings](./agent-settings.md) with {#action agent::OpenSettings}, go to the **External Agents** page, click `Add Agent`, and choose `Install from Registry`.

After installation, the agent appears in the new-thread menu in the Agent Panel and Threads Sidebar.

## Common Agents {#common-agents}

Common External Agents include:

- Claude
- Codex
- Antigravity
- Gemini CLI
- OpenCode
- Copilot
- Cursor
- Pi Coding Agent

This list is curated, not exhaustive. Open the ACP Registry in Zed for the current list of available agents.

For company-specific setup paths, including Claude, Codex, Gemini, OpenCode, Copilot, Cursor, Pi, and Poolside, see [AI by Company](./by-company.md).

## Claude Agent {#claude-agent}

Use Claude Agent when you want Claude running as an ACP-integrated External Agent in Zed.

Install Claude Agent from the [ACP Registry](#registry), then start a Claude Agent thread from the Agent Panel or Threads Sidebar. Claude Agent owns its own authentication and billing. An Anthropic API key configured for [Zed Agent](./zed-agent.md) does not automatically configure Claude Agent.

To choose your billing method, open a Claude Agent thread, run `/login`, and authenticate with an API key or with Claude Code where supported. Claude-specific files such as `CLAUDE.md` and `.claude/skills/` may be read by Claude Agent directly.

## Codex {#codex-cli}

Use Codex when you want Codex running as an ACP-integrated External Agent in Zed.

Install Codex from the [ACP Registry](#registry), then start a Codex thread from the Agent Panel or Threads Sidebar. Codex owns its own authentication and billing. An OpenAI API key configured for [Zed Agent](./zed-agent.md) does not automatically configure Codex.

Codex may support ChatGPT login, Codex API keys, OpenAI API keys, or Codex-native configuration depending on the installed version and environment. To change authentication, use the Codex thread's native login/logout flow. Codex scans `AGENTS.md` and repository `skills/` directories for project rules.

## Antigravity {#antigravity}

Use Antigravity when you want Google Antigravity running as an ACP-integrated External Agent in Zed.

Install Antigravity from the [ACP Registry](#registry) (agent ID `antigravity-acp`), then start an Antigravity thread from the Agent Panel or Threads Sidebar. Antigravity runs its proprietary ACP binary (`agy_acp_server`) and owns its own authentication (`oauth-personal`, `oauth-business`, or Gemini API key). It is distinct from Gemini CLI.

Antigravity scans global skills directories (`~/.gemini/antigravity-cli/skills`), project skill directories (`.agents/skills`, `.gemini/skills`), and supports custom subagents (`research`, `self`).

## Gemini CLI {#gemini-cli}

Use Gemini CLI when you want Gemini running as an ACP-integrated External Agent in Zed.

Install Gemini CLI from the [ACP Registry](#registry), then start a Gemini CLI thread from the Agent Panel or Threads Sidebar. Gemini CLI owns its own authentication and may prompt you to log in with Google, Vertex AI, or another Gemini-supported flow.

If `GEMINI_API_KEY` or `GOOGLE_AI_API_KEY` is available to the agent process, Gemini CLI uses that key. Otherwise, if you have configured an API key for Zed's Google AI provider, Zed passes that key to Gemini CLI as `GEMINI_API_KEY`.

## OpenCode {#opencode}

Use OpenCode when you want OpenCode running as an ACP-integrated External Agent in Zed.

Install OpenCode from the [ACP Registry](#registry), then start an OpenCode thread from the Agent Panel or Threads Sidebar. OpenCode owns its own auth, model selection, and subscription behavior. To use OpenCode models in Zed Agent instead, configure [OpenCode API access](./use-api-access.md#opencode).

## Copilot {#copilot}

Use Copilot External Agents where available when you want Copilot running as an ACP-integrated External Agent in Zed.

Copilot agent auth is owned by the Copilot integration. To use Copilot Chat models in Zed Agent or Copilot for edit prediction, see [Use an Existing Subscription](./use-an-existing-subscription.md#github-copilot).

## Cursor {#cursor}

Use Cursor External Agents where available when you want Cursor running as an ACP-integrated External Agent in Zed.

Cursor subscriptions do not configure Zed's LLM provider settings. Use Cursor's external-agent or CLI/TUI setup where available.

## Pi Coding Agent {#pi}

Use Pi Coding Agent when you want Pi running as an ACP-integrated External Agent in Zed.

Pi is an agent harness, not a Zed LLM subscription. Configure any provider auth, subscriptions, tools, or model choices in Pi.

## Poolside {#poolside}

Use Poolside when you want Poolside running as an ACP-integrated External Agent in Zed.

Install Poolside from the [ACP Registry](#registry), then start a Poolside thread from the Agent Panel or Threads Sidebar. If you haven't authenticated yet, the thread offers `Log in to Poolside`, which runs `pool login` in a terminal. Authentication and model selection are configured through Poolside, not Zed.

To configure Poolside from the terminal instead, install the [Poolside Agent CLI](https://github.com/poolsideai/pool), then run:

```sh
pool acp setup --editor zed
```

This command always writes to `~/.config/zed/settings.json`, which is Zed's settings file on macOS and on Linux without a custom `XDG_CONFIG_HOME`. On Windows, or with a custom config directory, install from the registry or use the manual configuration below instead. You do not need to restart Zed. It detects the settings change automatically. Select `Poolside` from the new-thread menu.

To configure it manually, install the [Poolside Agent CLI](https://github.com/poolsideai/pool), make sure `pool` is on your `PATH`, then add it as a [Custom Agent](#custom-agents):

```json [settings]
{
  "agent_servers": {
    "Poolside": {
      "command": "pool",
      "args": ["acp"],
      "type": "custom"
    }
  }
}
```

See [Poolside's Zed documentation](https://docs.poolside.ai/tools/zed) for more setup details.

## Start an External Agent Thread {#start-thread}

Open the [Agent Panel](./agent-panel.md), then use the agent selector or the new-thread menu to start a thread with an installed External Agent.

You can also create keybindings for specific agents with {#action agent::NewExternalAgentThread}.

## Configuration & Runtime Boundaries {#configuration-boundaries}

External Agents run as separate processes that communicate with Zed over ACP. This creates important boundaries regarding configuration, skills, tools, and permissions:

| Capability | Behavior in External Agent threads |
| --- | --- |
| Model/provider config | Usually owned by the External Agent |
| Auth/API keys/subscriptions | Usually owned by the External Agent |
| [Zed Agent profiles](./agent-profiles.md) | Do not apply unless the integration says otherwise |
| Zed Skills | Do not apply as Zed Skills; Zed does not expose its skill tool over ACP |
| Native agent skills/instructions | Scanned directly by the agent (`CLAUDE.md`, `AGENTS.md`, `SKILL.md`) |
| Built-in command execution | Executed in agent's own process/sandbox (agents do not call Zed's `terminal/create`) |
| Client FS tools (`fs/read`, `fs/write`) | Forwarded to Zed over ACP; verified against workspace boundaries and logged to `ActionLog` |
| Zed MCP servers | Forwarded over ACP in `session/new` (`stdio` filtered for local/remote; `http` headers preserved) |
| Native MCP config | Read independently by the agent (`~/.claude/mcp.json`, `~/.codex/mcp.json`) |
| [Zed tool permissions](./tool-permissions.md) | Regex rules in settings **only** apply to native agent; ACP relies entirely on `session/request_permission` |
| Human Gate approval | Displayed in Zed UI when agent sends `session/request_permission` (bypassed if agent runs in bypass/yolo mode) |

For Zed's native agent configuration, see [Zed Agent](./zed-agent.md).

## Agent-Specific Auth and Config {#agent-auth-config}

External Agents may have their own sign-in flow, API key setup, subscription behavior, environment variables, and config files.

Examples:

- Claude Agent may use Claude Code auth and Claude-native config.
- Codex may use ChatGPT login, Codex API keys, OpenAI API keys, or Codex-native config.
- Antigravity uses Google OAuth / API keys and independent tool permission modes (`default`, `auto_edit`, `yolo`).
- Cursor subscriptions do not configure Zed's LLM provider settings; use Cursor's agent or CLI setup where available.
- Pi Coding Agent is an agent harness. Configure provider auth in Pi.

If an External Agent supports subscription-backed behavior, configure that in the agent unless the agent's Zed integration says otherwise.

## Remote Projects {#remote-projects}

External Agents may read credentials locally, remotely, or through their own sign-in flow. Check the specific agent's setup path when using SSH, dev containers, or other remote projects.

Zed LLM provider API keys saved in the local keychain are not automatically the same as an External Agent's credentials.

## Custom Agents {#custom-agents}

Use custom agents when you are developing an ACP-compatible agent or need to run an agent that is not in the registry.

Open [Agent Settings](./agent-settings.md), go to the **External Agents** page, click `Add Agent`, and choose `Add Custom Agent`. Zed opens your settings file with an `agent_servers` entry.

```json [settings]
{
  "agent_servers": {
    "my-agent": {
      "type": "custom",
      "command": "node",
      "args": ["~/projects/agent/index.js", "--acp"],
      "env": {}
    }
  }
}
```

Registry-installed agents can also have per-agent settings under `agent_servers.<agent-id>`.

## Extension-Provided Agents {#extension-agents}

Extension-provided agents are deprecated. The [ACP Registry](#registry) is now the way to install agents, and previously installed extension agents are automatically migrated to their registry equivalents.

For details, see [Agent Server Extensions](../extensions/agent-servers.md).

## Importing Threads {#importing-threads}

Zed can import existing threads from configured External Agents so they appear in your [Thread History](./agent-panel.md#multiple-threads) alongside the rest of your threads.

Open the Threads Sidebar with {#kb multi_workspace::ToggleWorkspaceSidebar}, then open Thread History by clicking the clock icon at the bottom of the sidebar or running {#action agents_sidebar::ToggleThreadHistory} from the Command Palette. Click **Import Threads**, choose the agents you want to import from, then click **Import Threads** again.

Zed connects to each selected agent over ACP and adds sessions that are not already in your history. Imported threads are archived entries; open one to restore it and continue where you left off.

Only configured External Agents appear in the import dialog. Sessions without an associated working directory are skipped, and re-importing is safe because threads already in your history are skipped.

## MCP {#mcp}

Zed-configured [MCP servers](./mcp.md) may be forwarded to External Agents over ACP. External Agents may also read their own native MCP configuration.

If an MCP tool does not appear in an External Agent, check both Zed's MCP server configuration and the agent's native MCP configuration.

## Interactive Architecture Diagram {#interactive-diagram}

For an interactive, end-to-end swimlane diagram modeling session setup and restore, skills resolution, tools execution matrix, and human gate permissions across Claude Agent, Codex, and Antigravity, open [external-agents.html](./external-agents.html).

## Debugging {#debugging-agents}

Use {#action dev::OpenAcpLogs} from the Command Palette to inspect messages between Zed and an External Agent.

Include ACP logs when reporting issues with External Agents.
