# Native sender admission source audit

Status: source-only findings for [Wayfinder decision #32](https://github.com/Riso-application-factory/zed-ai-crew/issues/32). This audit made no native source patch, executable build, runtime check, tracker mutation or production choice. It does not resolve #32 or Confinement #18. The parent prototype must join and exercise the interfaces below.

## Sources and scope

Read map #1, ticket #32, [receiver source audit](wayfinder-receiver-lease.md), [native effect source audit](wayfinder-native-effect-admission.md), the exact [#30 combined patch](../evidence/wayfinder/confinement/codex-dispatch-combined.patch), and public OpenAI Codex `rust-v0.160.1` extracted at `/private/tmp/crew-receiver-v0.160.1/codex-rust-v0.160.1/codex-rs`. Baseline comparisons read the adjacent public `source.tar.gz`; no credentials, account configuration, private managed policy or real target-project content was read. The scratch tree already includes #31 receiver edits and the parent's #30 composition; the hash table distinguishes these from upstream. The inspected sender files still matched upstream when hashed.

Upstream links below refer to this exact version, rather than a moving branch. Prototype facts refer to the archived #30/#31 artifacts; those additions are not upstream APIs. [#31 canonical resolution](https://github.com/Riso-application-factory/zed-ai-crew/issues/31#issuecomment-6137968780) supplies previous runtime evidence; this audit did not rerun it.

## Established source facts

### Readiness and start-attempt ownership

[`RemoteProcess::start`](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/exec-server/src/remote_process.rs#L36) awaits its lazy native client, then delegates to `ExecServerClient::start_process`. [`start_process`](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/exec-server/src/client.rs#L1033) first validates network-policy callback/timeout compatibility. Its loop then awaits the current RPC client and calls `begin_process_start`. [`begin_process_start`](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/exec-server/src/client_recovery.rs#L282) checks the actual connected RPC `Arc` by pointer equality, checks disconnection, and increments active starts under the connection mutex. A failure here loops before any process-start send. This is not completed-RPC replay.

The sender inserts a native per-process `SessionState`, constructs an `ActiveProcessStart` and a `PendingProcessStartSession`, then moves the selected RPC client and request into a spawned start task. That task performs one `call_rpc(EXEC_METHOD, &params)`; success marks the native session recoverable. Explicit non-transport error removes that exact session; transport closure schedules cleanup. [`The two drop guards`](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/exec-server/src/client.rs#L363) own active-start decrement and pending-session cleanup. A new admission callback must pass denial through this result/error path or explicitly preserve these cleanup responsibilities; an early `?` that abandons result reporting would change the native error contract.

[`Recovery waits for active starts`](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/exec-server/src/client_recovery.rs#L440) before installing a replacement RPC connection. [`resume_once`](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/exec-server/src/client_recovery.rs#L487) constructs a new RPC client, initializes it, and recovers previously started processes through read/termination operations. It does not resend their original start requests. An owner callback waiting within an active start must therefore be bounded and must not recursively wait for the same client's recovery to complete. That would create a liveness cycle.

### The initialize response must be retained per actual RPC connection

[`InitializeResponse`](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/exec-server-protocol/src/protocol.rs#L87) has only `session_id` and optional `environment_info`; stock `ExecParams` and `WireExecParams` have no admission token. [`initialize_rpc`](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/exec-server/src/client.rs#L766) obtains this typed response and stores the stable executor session in `Inner.session_id: OnceLock<String>`. It rejects an unexpected session on recovery, then sends `initialized`. A stable session ID does not identify the current attachment.

The #31 router modification returns an additional `crewConnectionId` in raw initialization JSON, obtained from the receiver's attached `SessionHandle`. Its process route separately extracts `crewAdmission` before decoding the native `WireExecParams`; the handler constructs receiver-owned session/connection/attachment context. See [the archived receiver patch](../evidence/wayfinder/confinement/codex-receiver-fixture.patch). The current stock sender's typed initialization response ignores that extra field. There is no stock accessor for a Crew connection ID and no token-issuance path. The extension must retain initialization context associated with the exact selected `RpcClient`, including recovered clients. A global last-connection string risks rebinding an old in-flight task to a newly installed attachment.

### Typed send preserves exact parameters but still has waits

[`ExecServerClient::call_rpc`](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/exec-server/src/client.rs#L1297) delegates typed serialization to the selected `RpcClient` and maps its result. [`RpcClient::call_untraced`](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/exec-server/src/rpc.rs#L562) uses immediate call-slot admission. [`call_inner`](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/exec-server/src/rpc.rs#L625) awaits the pending-request mutex, atomically checks disconnection/registers the RPC request, serializes with `serde_json::to_value`, and awaits queue send and response. Owner issuance before this call does not eliminate those waits. #31's after-preparation receiver guard is still required to order spawn against publication.

The exact comparison must be the receiver-decoded `ExecParams`, including defaults and normalized sandbox policy cwd. The native `ExecParams` includes argv, cwd, environment policy and values, shell-snapshot request, TTY/stdin flags, argv0, sandbox and managed-network intent; [`WireExecParams::from`](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/exec-server-protocol/src/protocol.rs#L368) fills a legacy missing sandbox cwd from process cwd. Serializing an actual complete native `ExecParams` gives the owner a stronger basis than a handwritten model/tool approximation. JSON map insertion order is not authority; compare decoded structured values as #31 does. `ExecMetadata` remains telemetry, explicitly not authorization.

### Caller cancellation currently permits a transient start followed by cleanup

The start task is spawned independently. If its caller drops while the task is waiting or sending, the task can still complete a start; it detects failed result delivery/acknowledgement and schedules cleanup. [`cleanup_process_start`](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/exec-server/src/client.rs#L1341) retries termination on recoverable closure, then removes the matching native state. Moving issuance inside that task retains this lifetime: a cancelled caller can still obtain admission and send, followed by termination. The #31 explicit receiver cancellation control does not establish no-spawn-on-native-caller-cancel. A revised cancellation contract needs separate implementation and native runtime evidence.

### Owned MCP startup can share the sender seam only on the executor route

[`ExecutorStdioServerLauncher::launch_server`](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/rmcp-client/src/stdio_server_launcher.rs#L578) requires explicit cwd, converts argv/environment to UTF-8, obtains a native logical process ID and calls `ExecBackend::start`. It uses default metadata, raw stdin/stdout pipes, no TTY, no shell snapshot, no sandbox intent and no managed-network intent. An installed sender callback in the remote native backend can cover this actual launcher without inventing thread/tool attribution from its absent metadata. The issuer must explicitly allow the owned startup request and cwd, rather than accept arbitrary requests because metadata is empty.

[`codex-mcp` chooses the launcher](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/codex-mcp/src/rmcp_client.rs#L1230): local environments use `LocalStdioServerLauncher`; nonlocal environments use the resolved executor backend. Local startup bypasses the proposed remote sender seam. Preserve #30's explicit `crew_owned` / `crew-executor` selection, and record actual executor-start traces to prove the branch exercised. Merely configuring an MCP server does not demonstrate native executor startup admission.

Launcher backend errors become `io::Error` and then native `StartupOutcomeError`. Process backend errors become [`UnifiedExecError::create_process`](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/core/src/unified_exec/process_manager.rs#L1351), and RPC server errors preserve code/message through `ExecServerError::Server`. This establishes error plumbing, not a verified ACP rendering. #30's explicit `Warning` is emitted by its core callback; a later sender/receiver denial does not automatically execute that earlier warning branch. Test actual ACP failed-tool/startup/error events, or add a precisely scoped native notice and test that path. Do not silently fallback to direct local spawn after an admission error.

## Smallest proposed join

This is a candidate interface for the parent's public prototype, not an accepted production topology.

1. Explicitly install a native sender issuer object during public bridge startup, outside model/tool parameters. Pass the issuer actual `ExecParams` and initialization context from the selected, ready RPC connection. Separate the application/native sender-to-owner channel from executor RPC, model input, environment variables and telemetry. #31's inherited owner descriptors are a public harness authority, not real MDM/account provenance. The prototype's public supervisor may mediate issuance/publication through separate inherited channels while the receiver owner retains the authoritative grant state and lock.
2. Retain an optional typed `crewConnectionId` in initialization, plus receiver executor identity if independently supplied, with that exact `RpcClient`. Missing enabled-mode context denies. Populate it on native initialization and resume; preserve stable native session checks. Do not issue against `Inner.session_id` plus caller-supplied connection strings.
3. Inside the actual native start task after readiness/active-start registration, obtain a fresh owner-issued opaque handle for the exact decoded request, native attempt, executor, stable session and current attachment. Bounded owner-channel timeout/closure/malformed response errors must flow through the normal result path. MCP startup uses its actual parameters and an explicitly admitted owned-server context; do not fabricate core thread metadata for it.
4. Serialize a dedicated native process envelope containing the actual params and `crewAdmission`, then send through the same selected native `call_rpc`. The #31 receiver strips the handle and independently validates exact request/context/current revision under its one-use guard after preparation. Issue responses and token-bearing wire fields are capabilities in the public fixture; sender-provided generation labels are still diagnostic. Native retries obtain a new grant for each new effect attempt; an uncertain sent result is cleaned up rather than blindly replayed.
5. The receiver owner is the publication/revocation linearization point. The supervisor routes publish/issue commands to that owner, which retains the single policy lock through spawn completion/error. Sender and receiver do not claim a shared Rust lock across processes. A revision copied to sender memory or a digest check cannot replace receiver ownership. Issuance can become stale during pending-mutex/queue/receiver preparation waits; receiver rejection is the intended fail-closed result.

The public owner can deliberately inject malformed/missing/misbound/reused handles in its response for matched negative controls. Those are issuer-fixture tests of the actual native transport and receiver rejection, not an assertion that model input controls the issuance channel. Trace which negative is an owner denial before send and which is a receiver denial after a native send; both need a working native allow control.

## Limits and required evidence

Actual ACP → core → typed native sender → receiver → requested child, and actual owned MCP startup, must be exercised by the parent. Pair allow with owner unavailable/malformed, absent/forged/stale/revoked/misbound/reused handles, real or labelled deterministic readiness wait, current-connection resume, restoration and cleanup/error observations. A wire-only Python sender or recording peer cannot substitute for native sender execution. Record issued versus sent versus guard-acquired versus spawn-completed versus published separately.

The final-spawn seam leaves snapshot/proxy/prewarming and other preparation effects uncovered. The startup grant does not authorize MCP `tools/call`, OAuth, recovery or continuation; does not terminate already-started children on revocation; does not establish atomic attachment locking, immutable cwd/inode/worktree contents, full role/skill/config context or real issuer provenance. Installed provider/account paths, production topology and long-term maintenance ownership remain unresolved; specialist/human owners remain proposed.

## Source fingerprints

SHA-256, paths relative to `codex-rs`. Current hashes were observed during this audit; concurrent parent source composition is distinguished explicitly.

| File | Upstream hash | Observed hash |
| --- | --- | --- |
| `exec-server/src/client.rs` | `4cdc9200f3523de964b2ae387332343b10b9657c240674c1448e72c5649ec0bb` | same |
| `exec-server/src/client_recovery.rs` | `2c133cc9bccd904621ab41995e5a89906156810151970c9a3397aa77c02dd1d7` | same |
| `exec-server/src/rpc.rs` | `2b4fb518a3f6c769744992dea98e4d957cbb739de3b91f8fbec464f7bd2a9a79` | same |
| `exec-server/src/remote_process.rs` | `ca7b00a2faec610fed0e44f96fd826eeb77c1e232a166c62e680ccdc3c8ea822` | same |
| `exec-server-protocol/src/protocol.rs` | `6ef5940f3fd348b63b331299c80426b3aedcfa503e01e956d3301139733ed9fe` | same |
| `rmcp-client/src/stdio_server_launcher.rs` | `4c3e81d4e723ff0be9a760c76b7198b78ef4aafe3e1e2e7895ee60248c73d448` | same |
| `codex-mcp/src/rmcp_client.rs` | `b8562b3a6def953aab82fc8e728785010da932d5dce4526b0e8df861a7e3e685` | same |
| `exec-server/src/server/registry.rs` | `cdfbe1f16f2eadc1f9da6a343e0629aa9b40c64c606407a3c28ffcb671277891` | `e8c778b402eacb5124bfa879f5312f84d402a83cb6ee6bdf99460e5f69e759b3` |
| `exec-server/src/server/handler.rs` | `e2b823136b45065f7948eff12563a30258710eef6708fa58ffbc6b34caee6333` | `271bb0f6baf22ff51390db80302a29cf1389e89a6f95d094c8a4bd54e92c9351` |
| `core/src/unified_exec/process_manager.rs` | `ec4878fbe0da4d8e4ae40629702a6c509a7168fd5494c0453d4081cd8f7fc235` | `4f86624c67b20c133eda5d5efb44b40d0fc42347e1f617aba619e0af8fe140be` |
| `exec-server/src/crew_receiver_admission.rs` | prototype only | `c26eb184397594d19903f0d2584b0ca29c3000dff1451cac00753e474f9500d2` |
| `exec-server/src/crew_receiver_fixture.rs` | prototype only | `4c2873a7e672b95f0b8ecb26a7c0b19fc7b459d1664e27a87bb18c7e6fdb4c0e` |
