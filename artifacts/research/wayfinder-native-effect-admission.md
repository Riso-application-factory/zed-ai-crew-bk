# Native effect admission seam audit

Status: source-only findings; proposed seam; no build, runtime check, tracker mutation, production authority, or race-free lease verified by this audit.

Destination: answer the receiver/transport boundary portion of [decision #30](https://github.com/Riso-application-factory/zed-ai-crew/issues/30), within the Human-selected bounded native bridge investigation. This does not resolve Confinement #18.

## Source boundary

Read only the public Rust `rust-v0.160.1` source copy at `/private/tmp/crew-codex-bridge-source-v0.160.1`. This audit did not read account configuration, credentials or private managed sources. The files cited below were hashed at audit time; exact hashes distinguish the inspected baseline from subsequent scratch patches. Installed ACP is outside this narrow native audit; no ACP execution or new compatibility conclusion was made.

The upstream source link prefix is `https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/`. Line references below name exact files in that source tree. Source facts and proposed design are separated.

## Established source facts

### Stock executor has no admission-generation protocol

[`exec-server-protocol/src/protocol.rs:292–403`](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/exec-server-protocol/src/protocol.rs#L292) defines `ExecMetadata` as optional executor telemetry, explicitly not authorization. Its two fields are `thread_id` and `tool_call_id`. `ExecParams` and `WireExecParams` carry process ID, command, working directory, environment, sandbox intent, managed-network intent/context, and optional shell snapshot, but no Crew policy/context generation, capability token, issuer, or admission lease. `process_id` is a connection/session-scoped logical handle, not an OS PID or authority token.

The router deserializes `WireExecParams`, converts it to `ExecParams`, and dispatches it through initialized executor session state: [`exec-server/src/server/registry.rs:89–100`](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/exec-server/src/server/registry.rs#L89), [`server/handler.rs:181–198`](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/exec-server/src/server/handler.rs#L181). Local process launch consumes the metadata only for telemetry correlation, filtering the tool-call ID for safe correlation: [`local_process.rs:286–301`](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/exec-server/src/local_process.rs#L286). An arbitrary metadata field or generation label does not become validated executor authority. There is no complete generation-bearing authority field in these stock process parameter structures.

Managed-network enforcement is real sandbox intent, not a general policy lease. `http-client/src/network_policy.rs:119–230` defines a revision for destination-policy publication; that revision is not carried as a common Crew session/role/root/MCP/context generation in `ExecParams`. Do not rename it as a complete admission generation.

### A core callback before backend start does not eliminate executor waits

[`core/src/unified_exec/process_manager.rs:1312–1358`](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/core/src/unified_exec/process_manager.rs#L1312) constructs the remote/snapshot execution request and awaits the backend. Sandbox retries may reuse a public unified-exec process ID for a different executor process. A policy owner must bind each actual effect attempt, not assume the ID proves a single immutable attempt.

[`exec-server/src/remote_process.rs:36–49`](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/exec-server/src/remote_process.rs#L36) awaits lazy client readiness before `start_process`. [`exec-server/src/client.rs:1033–1156`](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/exec-server/src/client.rs#L1033) awaits RPC recovery/readiness, may loop before registering a start attempt, and sends `process/start` in a spawned task. This is not evidence that a completed process/start RPC is transparently replayed; the observed loop is before the sent attempt. Remote registry recovery itself also has retry/sleep/re-registration paths: `exec-server/src/remote/direct.rs:141–229`.

On the receiving executor, [`local_process.rs:370–435`](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/exec-server/src/local_process.rs#L370) awaits sandbox/network preparation, shell snapshot preparation, then process-map locking before awaiting `spawn_process`. Thus both sender-side and receiver-side waits remain after a core-level early validation. Snapshot/network preparation can itself perform work; a check immediately before final spawn covers spawn admission only, not all preceding preparation effects.

### MCP preserves a catalog lease, but not a complete Crew policy lease

[`codex-mcp/src/binding.rs:170–287`](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/codex-mcp/src/binding.rs#L170) binds an exact managed client, immutable MCP configuration/permission profile, catalog snapshot, tool identity, server metadata, timeout and plugin context. `server_environment_id()` identifies the captured server environment; it is not a fresh Crew policy generation. Catalog revision exists internally rather than as a complete public policy-generation interface.

[`codex-mcp/src/client_tool_catalog.rs:180–199`](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/codex-mcp/src/client_tool_catalog.rs#L180) rejects stale prepared catalogs before running preparation and retains the catalog read guard until the execution closure finishes. Its comparison permits an equivalent complete tool definition for some non-explicit-refresh changes; it is not always strict numeric-generation equality. Source tests `binding_tests.rs:327–406` exercise stale-call preparation suppression and refresh blocking; this audit read those tests but did not run them.

[`codex-mcp/src/binding.rs:304–365`](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/codex-mcp/src/binding.rs#L304) first awaits `prepare()`, then awaits `add_trusted_access_context`, computes remaining timeout, then calls the lower-level MCP client, all under the catalog guard. A check placed only in caller preparation occurs before the trusted-access wait. The public `call_with_preparation` signature has a single preparation callback; there is no independent after-trusted-context policy-admission callback in that baseline.

Preparation is not guaranteed pure. [`core/src/mcp_tool_call.rs:465–542`](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/core/src/mcp_tool_call.rs#L465) can apply approval state, mark memory mode, rewrite/upload file arguments and await sandbox metadata. A post-preparation callback protects the eventual `tools/call` boundary, not all these earlier effects.

Even after trusted-access context, [`rmcp-client/src/rmcp_client.rs:816–903`](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/rmcp-client/src/rmcp_client.rs#L816) awaits OAuth refresh and service operations before transport send. `run_service_operation` at lines 1376–1411 may recover an expired HTTP session and retry the operation. The general transient retry helper at lines 1414–1508 applies retryable transient errors specifically to `tools/list`, not arbitrary `tools/call`; do not claim unrestricted tool-call retry. Modern MCP multi-round input at [`rmcp-client/src/tool_input.rs:39–130`](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/rmcp-client/src/tool_input.rs#L39) can await replies, Human inputs, or a sleep and then send another call round. It includes a protection against replaying some continuation failures, but does not add a Crew admission-generation lease.

## Proposed minimum interfaces and checks

1. **Bounded MCP prototype:** add a separate admission callback after `add_trusted_access_context` and before the lower-level `call_tool`, inside `run_with_snapshot`; retain existing preparation semantics. A callback must return an error that stops the send. Retain owner-supplied immutable expected context plus an explicit effect-attempt identity; never accept generation metadata from model/caller as authority. This seam is sufficient to test the particular public stdio initial-call gap if the fixture has no OAuth/recovery/continuation wait, but is not generic transport-level closure.
2. **Bounded process prototype:** a core callback after approvals/preparation and immediately before each backend effect attempt can prove rejection of the already reproduced delayed-model-dispatch counterfactual. Call it a dispatch recheck, not a receiver-validated lease. Stock process metadata cannot carry an enforceable lease without explicit executor/protocol changes.
3. **Complete lease candidate:** an actual policy owner must publish an immutable versioned context and coordinate replacement with live effect guards. Sender and receiver must agree on the trusted issuer, complete covered context, attempt identity, lease acquisition/expiry/revocation, cancellation and recovery. The receiving executor needs a real supported admission mechanism after its waits, before each relevant effect. MCP needs the check/guard at each actual transport attempt and continuation, and separate treatment of preparatory effects. These are unresolved interface/ownership decisions, not existing native guarantees.

The catalog's existing guard is a useful concurrency shape, not evidence that policy source files or a Crew context already share that lock. If an external actor can replace managed files outside the owner guard, a digest-before-send still leaves a race. Public synthetic owner callbacks may establish a bounded prototype; they cannot establish real MDM/account/provider provenance or production revocation semantics. Retaining a generation receipt, recording an arbitrary MCP `_meta` value, or placing a generation in executor telemetry is diagnostic unless the receiving authority validates it under the agreed lease.

Expected return to #30: exact modified-file diff, callback/error path, delayed valid-to-invalid/changed-context and malformed-source controls, an unchanged-policy positive, actual native command and MCP effect observations, recovery behavior, and explicit uncovered waits/effects. No policy-owner acceptance, release permission or long-term fork responsibility is supplied by this audit.

## Inspected file hashes

SHA-256; paths relative to the public source-copy root above.

| File | SHA-256 |
| --- | --- |
| `exec-server-protocol/src/protocol.rs` | `6ef5940f3fd348b63b331299c80426b3aedcfa503e01e956d3301139733ed9fe` |
| `exec-server/src/server/registry.rs` | `cdfbe1f16f2eadc1f9da6a343e0629aa9b40c64c606407a3c28ffcb671277891` |
| `exec-server/src/server/handler.rs` | `e2b823136b45065f7948eff12563a30258710eef6708fa58ffbc6b34caee6333` |
| `exec-server/src/local_process.rs` | `3f7ede21bfa47aa7133787f2e94409a12efc20f5f47c5a37e1e1c5eafc543c62` |
| `exec-server/src/remote_process.rs` | `ca7b00a2faec610fed0e44f96fd826eeb77c1e232a166c62e680ccdc3c8ea822` |
| `exec-server/src/client.rs` | `4cdc9200f3523de964b2ae387332343b10b9657c240674c1448e72c5649ec0bb` |
| `exec-server/src/remote/direct.rs` | `d8c1d1b17f603aec2d38920782ba1cd7868bfaf82811e650157b76f217e94a97` |
| `exec-server/src/process.rs` | `63b2029f79a5bc7f07575c66afe4025f888173f2932041b30e6d94315c356dc4` |
| `core/src/unified_exec/process_manager.rs` | `ec4878fbe0da4d8e4ae40629702a6c509a7168fd5494c0453d4081cd8f7fc235` |
| `codex-mcp/src/binding.rs` | `3b9f48ce4f861f979b465fb8ce4c3ef25a0ca0c2aa57d0a8b85429eb298a4ac8` |
| `codex-mcp/src/client_tool_catalog.rs` | `c2872ff795348220e40911f4ba91cff95fa3ebc07c7051d7e62a423a333d1a93` |
| `codex-mcp/src/trusted_access.rs` | `9349f480faa0f3594b03bad68da6f2eb6582a1122c134a0983b548eff03a3463` |
| `codex-mcp/src/binding_tests.rs` | `8e57201f49a6a961f083d19f1e4fb36d49cad5f72b6144c35ee4fffa8dc4c1c5` |
| `core/src/mcp_tool_call.rs` | `509b10168a9a1e99abd0f5722ad5bf377645dbb2e446ead6c324daef7a84af58` |
| `rmcp-client/src/rmcp_client.rs` | `7b4dae74647e0db5d22bc26f49781a0b455beb35f0f3a36a1f067236cd2bf89d` |
| `rmcp-client/src/tool_input.rs` | `a140e1c94972f9b763a31c8b031f9fe7f333383f56f440fb7f23b2deaf89d8d4` |
| `http-client/src/network_policy.rs` | `65569bcb7f943980ab0ff8f75352d3de4f738bd369c527cff0018a03c30bd8bf` |
