# TD1 evidence: ACP capability probes

Date: 2026-10-10 (local time, UTC+7). Host: macOS arm64, Node v22.23.2.

## Adapters probed

| Provider | Adapter / version (from `initialize.agentInfo`) | Launch command (same as Zed's registry entry) |
|---|---|---|
| claude | `@agentclientprotocol/claude-agent-acp` 0.89.0 | `node <Zed registry>/npx/claude-acp/node_modules/@agentclientprotocol/claude-agent-acp/dist/index.js`, env `ANTHROPIC_API_KEY=""` (as Zed does, `crates/agent_servers/src/custom.rs:232`) |
| codex | `@agentclientprotocol/codex-acp` 2.2.2 | `npx -y @agentclientprotocol/codex-acp@2.2.2` |
| antigravity | `antigravity-acp` 1.3.0 | `<Zed registry>/antigravity-acp/v_1.3.0_59296d23d623ce0a_83936844a8ab4c19/agy_acp_server.par` (no args on darwin) |

`<Zed registry>` = `~/Library/Application Support/Zed/external_agents/registry`.

## Method

A stdlib-only Python JSON-RPC client (kept outside the repo in `/tmp/acp-probe/scripts/`) speaks ACP over stdio with `protocolVersion: 1` and `clientCapabilities: {fs: {readTextFile: false, writeTextFile: false}, terminal: false}`. It answers `session/request_permission` with a reject option and any other client request with `-32601`. Workspaces: `/tmp/acp-probe/ws` and `/tmp/acp-probe/ws2`.

Every `session/new` sent one stdio MCP server (`probe-dummy`, a stub that logs what it receives) and
`_meta: {systemPrompt: <instr>, jetbrains: {air: {customInstructions: <instr>}}}`, where `<instr>` says to always reply with exactly `BANANA`.

Phases per provider:

1. `*-1-handshake.jsonl`: initialize, session/new (ws), set_mode (a non-bypass mode and back), set_model (and back), set_config_option, session/cancel on an idle session, a second session/new with cwd ws2, session/list.
2. `*-2-prompt.jsonl`: a new process, session/new, a switch to a cheaper model where one was offered, and one prompt `Reply with the single word OK.` If the reply is `BANANA`, the role instruction was injected.
3. `*-3-restart-load.jsonl`: a fresh process (the previous one was killed), initialize, session/load of the phase-2 session (with history replay), session/resume, session/list.
4. `codex-4-list.jsonl`: session/list with the realpath cwd `/private/tmp/acp-probe/ws`. Unrelated user sessions were removed from the output.
5. `*-5-mcp-dummy-server.jsonl`: the log from the stub MCP server. It shows whether the adapter spawned the client-provided stdio server and listed its tools.

Redactions: emails, API keys, JWTs and token fields were replaced at write time. The codex list output was filtered to probe sessions only.

## Result highlights

- claude: the prompt returned `Authentication required` (-32000). Claude Code on this machine is not logged in (`claude auth status` returns `loggedIn: false`). Because of that, the probe could not check role-instruction injection or prompt-time behaviour. The handshake, session/new, modes/models, load and resume all work without auth.
- codex: the reply was `BANANA`, so `_meta.jetbrains.air.customInstructions` is injected as Codex `developerInstructions`.
- antigravity: the reply was `OK`, so neither `_meta.systemPrompt` nor `customInstructions` is honoured. The stub MCP server was spawned lazily at the first prompt, not at session/new.
