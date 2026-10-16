# Receiver admission lease source audit

Status: source-only findings and proposed prototype seam for [Wayfinder decision #31](https://github.com/Riso-application-factory/zed-ai-crew/issues/31). No source patch, executable build, runtime control, tracker mutation, production authority or receiver lease was performed or verified by this audit. Return these findings to the separate receiver prototype; they do not resolve Confinement #18.

## Sources and scope

Inspected public OpenAI Codex `rust-v0.160.1`, extracted at `/private/tmp/crew-receiver-v0.160.1/codex-rust-v0.160.1/codex-rs`. Source fingerprints below were captured before the parent prototype's edits. Read ticket #31 and [the preceding native effect audit](wayfinder-native-effect-admission.md). No private configuration, account, credential, managed source or runtime data was read. Findings refer to this pin only.

## Established source facts

### The receiver owns process state, but stock admission is absent

[`ExecMetadata`, `ExecParams` and `WireExecParams`](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/exec-server-protocol/src/protocol.rs#L292) contain no owner-issued admission token or policy revision. Metadata is explicitly telemetry rather than authorization. The process ID is a client-chosen logical handle scoped to an executor session. Cwd is a URI interpreted at launch time; equality of its text does not establish inode identity or immutable worktree contents. These are separate claims and should not be conflated.

[`ExecServerHandler::exec`](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/exec-server/src/server/handler.rs#L181) requires an initialized, attached session before awaiting `session.process().exec`. [`ProcessHandler`](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/exec-server/src/server/process_handler.rs#L20) forwards to its `LocalProcess`; it does not pass a receiver-owned session/connection identity or a fresh attachment predicate to process start. Executor registration is provided through `ProcessTelemetry`, and currently contributes attribution, not admission authority. A callback that trusts sender-supplied session or executor labels without independently obtaining receiver context cannot establish those bindings.

### Final spawn has a concrete seam after waits

[`LocalProcess::start_process`](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/exec-server/src/local_process.rs#L370) awaits sandbox/network preparation and Unix shell snapshot preparation. It subsequently awaits the process-map lock, rejects an existing process ID and inserts an identity-bearing `Starting(Arc<ProcessStart>)` reservation. It then calls and awaits `spawn_process` at line 424. The smallest final-spawn callback seam is after these preparations and reservation, immediately before that call. A denial there must remove only its own matching reservation; otherwise a valid retry can fail with an already-existing process ID. Native spawn-error cleanup already uses pointer equality for this purpose at lines 438–454.

After spawn, [`the registration block`](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/exec-server/src/local_process.rs#L460) reacquires the process-map lock. If the reservation disappeared, it terminates the new child and reports cancellation. [`terminate_process`](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/exec-server/src/local_process.rs#L722) removes a `Starting` entry; this does not by itself cancel the suspended spawn future. Consequently, this existing cancellation path is not proof that no transient OS child was created. `shutdown` drains reservations and terminates running sessions; policy publication currently does neither automatically.

[`spawn_process`](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/sandboxing/src/spawn.rs#L43) preserves platform sandbox selection and awaits the PTY/pipe or Windows session launcher. On Unix, [`spawn_helper::spawn`](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/utils/pty/src/spawn_helper.rs#L71) synchronously launches the helper, then awaits environment transmission and target-exec acknowledgement. Its `StartingChild` drop guard kills incomplete launches on cancellation at lines 187–202. Holding an admission guard only until entry to `spawn_process` would leave these waits outside the ordering; retain it until spawn completion/error and state the exact end point. This audit did not execute cancellation tests or prove descendant cleanup.

### Preparation can already perform effects

[`prepare_managed_network`](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/exec-server/src/process_sandbox.rs#L353) builds and starts a network proxy before final spawn. [`ShellSnapshotCache::prepare`](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/exec-server/src/shell_snapshot.rs#L86) may wait for a shared capture, maintain retry/backoff state and replay cached state. [`capture_snapshot`](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/exec-server/src/shell_snapshot.rs#L304) materializes snapshot transport and actually spawns a capture shell at line 380, under the prepared sandbox. Shell startup executes before the eventual requested command. [`LocalProcess` prewarming](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/exec-server/src/local_process.rs#L809) also invokes preparation separately from requested process start.

An after-preparation callback protects the final requested spawn. It does not retroactively authorize or erase proxy startup, shell capture, snapshot-cache mutation or prewarming. A public prototype may explicitly disable snapshots/proxy to isolate the final-spawn question, or test those effects separately; it must name that restriction. Cleanup of a prepared proxy following a new callback denial needs an explicit code/error-path review and runtime observation, not an assumption.

### Connection and executor-session lifetimes differ

[`SessionRegistry::attach`](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/exec-server/src/server/session_registry.rs#L65) creates a UUID session and a `ProcessHandler`, or resumes the same handler when a detached session is still valid. It rejects resume while another connection is active. A fresh connection gets a new connection UUID. The production detached-session TTL is 30 seconds; expiry shuts down the session's process handler. [`SessionHandle`](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/exec-server/src/server/session_registry.rs#L257) supplies receiver-owned session/connection IDs and attachment status. Connection resume should therefore be an explicit admission binding choice: reject a prior attachment's lease, or deliberately authorize reuse under a stable session. It must not arise accidentally from a token that binds only a process ID.

[`RequestDispatcher`](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/exec-server/src/server/request_dispatcher.rs#L236) selects a request route against disconnection and aborts request tasks at shutdown. Optional concurrent dispatch reserves separate capacity for cleanup/control methods. [`ConnectionProcessor`](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/exec-server/src/server/processor.rs#L204) checks attachment when processing incoming events, then closes client requests and shuts down the dispatcher/handler on disconnect. Cancellation can drop a callback future or guard; reservation cleanup must account for drop as well as explicit errors. These source facts do not prove any actual reconnect race.

[`The stdio transport`](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/exec-server/src/server/transport.rs#L145) serves one connection and shuts down its session registry afterward. Detached sessions cannot resume through a replacement stdio connection. Actual same-executor session-resume evidence therefore requires an exercised resumable transport; a restarted stdio binary is a replacement/new-session control.

### Sender readiness is not process-start replay

[`RemoteProcess::start`](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/exec-server/src/remote_process.rs#L36) awaits a lazy client. [`ExecServerClient::start_process`](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/exec-server/src/client.rs#L1033) loops to obtain a current RPC client and register an active start before sending. [`begin_process_start`](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/exec-server/src/client_recovery.rs#L282) compares the current client pointer and connection state. This loop is before the actual process-start send; it is not evidence that a completed start RPC is replayed automatically. After send, a spawned task preserves cleanup when the caller does not receive the resulting session. [`cleanup_process_start`](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/exec-server/src/client.rs#L1341) retries termination on recoverable transport closure, not arbitrary process-start execution. Sender retry and receiver attempt consumption must distinguish an unsent request, an uncertain sent outcome and an actually started process.

### Owned MCP startup uses the same backend, not the same tool-call lease

[`ExecutorStdioServerLauncher`](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/rmcp-client/src/stdio_server_launcher.rs#L547) starts a server through `ExecBackend::start` at line 620 with explicit cwd, pipe stdin and non-TTY framing. Stock startup uses default metadata, no shell snapshot, no process sandbox intent and no managed-network intent in that request. A callback inside `LocalProcess::start_process` can cover a launcher using that backend if the authority is explicitly installed there. A callback only in the RPC handler misses direct backend callers. This source connection is not an executed MCP compatibility result.

After startup, the executor transports raw bytes; it does not inspect MCP `initialize`, `tools/list` or `tools/call`. The launcher's source explicitly distinguishes process ownership and descendant lifetime from caller-controlled process IDs. The existing [`MCP catalog guard`](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/codex-mcp/src/client_tool_catalog.rs#L176) holds its catalog read lock through preparation/execution, but its publication lock is unrelated to a Crew owner policy revision. Gating one MCP server spawn does not gate subsequent calls, OAuth/recovery/continuation or filesystem/child effects.

## Proposed minimum explicit interface and ordering

The following is a candidate for the separate prototype, not upstream behavior or a settled production decision.

1. Install a typed callback/authority object explicitly into the bounded receiver backend. Supply receiver-owned session/attachment and executor-instance context alongside the actual request. Preserve native sandbox, environment filtering, managed-network, process-map and transport ownership. Missing admission must fail closed only in the explicitly enabled fixture mode; do not claim stock compatibility.
2. The public fixture owner publishes an immutable revision and issued-attempt records under one shared asynchronous lock. A record binds issuer, revision, operation, fresh attempt identity, executor instance, receiver session/attachment, worktree/cwd and the exact request fingerprint. Bind argv, environment, sandbox/network/snapshot intent as applicable; caller-provided identity strings alone are insufficient. An opaque, unguessable handle looked up in owner state can demonstrate this relationship; merely echoing a generation or public digest cannot. Public fixture authority is not real account/MDM provenance.
3. After preparation/reservation, receiver validation acquires an owned read/effect guard, compares current revision/revocation and receiver context, and atomically marks the attempt consumed in owner-controlled state. Keep that guard through `spawn_process` completion/error. Duplicate attempts, wrong context, missing/unknown handles and old revisions reject before the requested spawn. Retry after consumption obtains a new owner-issued attempt; retry after transport uncertainty never blindly reuses a capability to create another process.
4. Publication/revocation acquires the same write lock and returns after it changes owner state. If publication wins first, an older admission rejects. If a validated spawn already holds the read guard, publication waits for that spawn's guarded end point, then takes effect for future attempts. This is ordered revocation, not instantaneous cancellation. Queued write intent is not published revocation. Avoid lock cycles: do not await effect-task completion while holding the authority write lock, and do not hold the receiver process-map lock across authority acquisition or arbitrary callback work.
5. Define cancellation explicitly. Drop must release the effect guard; explicit denial and cancellation must release the matching reservation and prepared resources. Already-started children remain running unless a separate owner mechanism terminates them. Revoke-before-spawn and revoke-after-start have different success criteria. Every error must reach the native RPC/backend caller, with no silent fallback to stock start.

Because the current source does not automatically derive all these owner/context relationships, the implementation must either inject and test them explicitly or name the missing contract precisely. A synthetic callback with sender-controlled labels can validate a selected public harness, but cannot be reported as a general receiver-validated lease.

## Required runtime evidence for the parent prototype

Use matched positive controls and actual native requested markers. Cover stable valid admission; missing/unknown/forged handle; stale/revoked revision; session/attachment/worktree/executor/attempt/request mismatch; publication during a reproducible preparation wait; valid restoration; duplicate/retry attempt; and actual connection replacement/resume according to the transport exercised. Record publication-requested versus publication-completed and guard-acquired versus spawn-completed separately. A held-guard publication control should prove the declared ordering without calling the pre-publication child a denial failure. Observe native error propagation, retry reservation reuse and cleanup. MCP startup needs its own real launcher control if compatibility is claimed. Full host confinement, real issuer provenance and all preceding/subsequent effects remain outside this source audit.

## Inspected baseline SHA-256

Paths relative to `codex-rs` at the source pin above.

| File | SHA-256 |
| --- | --- |
| `exec-server-protocol/src/protocol.rs` | `6ef5940f3fd348b63b331299c80426b3aedcfa503e01e956d3301139733ed9fe` |
| `exec-server/src/local_process.rs` | `3f7ede21bfa47aa7133787f2e94409a12efc20f5f47c5a37e1e1c5eafc543c62` |
| `exec-server/src/process_sandbox.rs` | `aad9cce5a24e7fba5f4d1d832ed01da05efe1e5864ca463459d003c99ff6e60b` |
| `exec-server/src/shell_snapshot.rs` | `70d6a89371392c6c5fd42fc8610e853ca1c99c6ed16e6af0a8039689c2dede00` |
| `exec-server/src/remote_process.rs` | `ca7b00a2faec610fed0e44f96fd826eeb77c1e232a166c62e680ccdc3c8ea822` |
| `exec-server/src/client.rs` | `4cdc9200f3523de964b2ae387332343b10b9657c240674c1448e72c5649ec0bb` |
| `exec-server/src/client_recovery.rs` | `2c133cc9bccd904621ab41995e5a89906156810151970c9a3397aa77c02dd1d7` |
| `exec-server/src/server/handler.rs` | `e2b823136b45065f7948eff12563a30258710eef6708fa58ffbc6b34caee6333` |
| `exec-server/src/server/process_handler.rs` | `b66ee1d37fa927a1ad74f67dcf57d093cd2056860f01a708c3809ba279241536` |
| `exec-server/src/server/session_registry.rs` | `a5569a40aa9f7d0bacd80a57b3429a9b090c84e23aa341d0f488c02118f3cb26` |
| `exec-server/src/server/request_dispatcher.rs` | `4fa528536219a1fc7697df1da4358f6bf9635656fdf74b806e6cdf648c3020d3` |
| `exec-server/src/server/processor.rs` | `cd8a5db6940ec31bd7a8aed80455653893b3d4cefee7942b494f758acbbba154` |
| `exec-server/src/server/transport.rs` | `29924b0aa1388773f6e398674d2d9d108b2ed18faf63e2b941b6427068c8f409` |
| `exec-server/src/server/registry.rs` | `cdfbe1f16f2eadc1f9da6a343e0629aa9b40c64c606407a3c28ffcb671277891` |
| `sandboxing/src/spawn.rs` | `6a92bc742aaca33e210f9d494d7f19722c96ec2698f2c239718ae053244d645c` |
| `utils/pty/src/spawn_helper.rs` | `4e6c76118340772e6f25677e3904b86f1d722ab290653a3211bb8f85ee16a128` |
| `utils/pty/src/pipe.rs` | `c147cbbf3c9bde1f15c83ded3c4641a6b5ca290f3639256b2cf5b4f7efe5d820` |
| `utils/pty/src/pty.rs` | `840f07e5875466ae2da0182c36f7041cebd6f11ce7b73e265b48d738c6ebac80` |
| `rmcp-client/src/stdio_server_launcher.rs` | `4c3e81d4e723ff0be9a760c76b7198b78ef4aafe3e1e2e7895ee60248c73d448` |
| `codex-mcp/src/client_tool_catalog.rs` | `c2872ff795348220e40911f4ba91cff95fa3ebc07c7051d7e62a423a333d1a93` |
