# Native filesystem effects after sender/receiver admission

Status: source audit complete; runtime controls are proposed here and belong to the Coordinator's actual #18 probe. No native patch, tracker write, private config access or runtime execution was performed by this research agent.

Scope: OpenAI Codex `rust-v0.160.1`, local source including the bounded public #30/#31/#32 prototype. The source archive hash is `5226394058e04c5404fe737b84fbede38ebf41d88a72eb8b27f3d1c6ea23373b`. Each audited source hash below was checked against `artifacts/evidence/wayfinder/confinement/codex-sender-full-source-index.json`; modified files are prototype facts, not behavior attributed to the upstream tag. Canonical prior result: [sender prototype #32](https://github.com/Riso-application-factory/zed-ai-crew/issues/32#issuecomment-6138247102).

## Custom apply_patch contract

The model must emit a `custom_tool_call`, name `apply_patch`, with a raw patch in string `input`, and `call_id`; a `function_call` with JSON arguments does not match this native handler. Handler matches only `ToolPayload::Custom`, parses the patch, selects the turn environment, verifies through that environment's filesystem using its cwd and sandbox, then executes the verified action. [Native handler](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/core/src/tools/handlers/apply_patch.rs#L315-L409), [response protocol](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/protocol/src/models.rs#L1134-L1165).

Successful model-history output is `custom_tool_call_output` with the same `call_id` and an `output` **plain string**. It is not a nested JSON result object and does not serialize the internal success flag. The string starts `Exit code: 0\nWall time: <rounded seconds> seconds\nOutput:\nSuccess. Updated the following files:\n`, followed by `A`, `M`, `D` path lines. Failed execution/verification gives model-visible error text; the probe must inspect content and on-disk effects as well as ACP updates. [Tool output mapping](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/core/src/tools/context.rs#L307-L339), [custom response mapping](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/core/src/tools/context.rs#L578-L596), [plain-string serializer](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/protocol/src/models.rs#L2250-L2262), [formatted output](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/core/src/tools/mod.rs#L99-L125), [patch summary](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/apply-patch/src/lib.rs#L860-L876).

Example input for the unchanged native custom tool:

```json
{"type":"custom_tool_call","call_id":"crew-public-patch-positive","name":"apply_patch","input":"*** Begin Patch\n*** Add File: public-marker.txt\n+PUBLIC_MARKER\n*** End Patch"}
```

## Filesystem effects are a distinct frontier

Fact in archived prototype: common dispatch checks public policy at `core/src/tools/registry.rs:532–535` before hooks. `ApplyPatchRuntime::run` then obtains the selected filesystem and calls `apply_patch_with_options` after the orchestrator's approval/sandbox work; it has no new admission check or effect guard. `_ctx` is unused in that runtime. Hence the common dispatch check exists, but there is a source-supported interval between it and direct filesystem effects. [Upstream runtime](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/core/src/tools/runtimes/apply_patch.rs#L155-L207), [orchestrator invocation](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/core/src/tools/handlers/apply_patch.rs#L522-L608); prototype registry is separately hashed below.

Remote `write_file`, `create_directory`, `remove` and `copy` await the selected lazy client then send individual `fs/*` RPCs with path/options and a cloned filesystem sandbox context. The wire requests contain no #31 admission token/attempt/generation binding. The receiver handlers require initialized transport and call `FileSystemHandler`; they do not route through the prototype admitted process handler. The #31/#32 receiver check is instead in modified `LocalProcess` immediately before the actual process spawn. **Inference:** that process lease cannot by itself admit or revoke these independent filesystem operations. This is a contract gap to test, not an observed escape. [Remote FS](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/exec-server/src/remote_file_system.rs#L112-L153), [remove/copy](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/exec-server/src/remote_file_system.rs#L264-L306), [wire write](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/exec-server-protocol/src/protocol.rs#L606-L649), [receiver mutation handlers](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/exec-server/src/server/file_system_handler.rs#L183-L222), [remove/copy receiver](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/exec-server/src/server/file_system_handler.rs#L298-L335). Modified receiver forwarding is separately hashed below.

A sandboxed FS operation may itself launch a native filesystem helper through `FileSystemSandboxRunner → run_command → spawn_command`. The helper command directly invokes `Command::spawn`; it does not enter `LocalProcess::exec_admitted`. Helper env has an explicit allowlist and macOS descriptor policy is explicit. **Inference:** generic process-start receipts do not prove the helper or its FS mutation was admission guarded. [Sandboxed FS runner](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/exec-server/src/sandboxed_file_system.rs#L99-L113), [helper execution](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/exec-server/src/fs_sandbox.rs#L392-L434), [direct helper spawn](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/exec-server/src/fs_sandbox.rs#L519-L551).

## Native semantics the matrix must preserve

- Add/update uses FS writes; missing-parent recovery can create directories. Delete reads the prior content/metadata then removes the file. Move writes the destination then removes the source; there is no atomic rename operation in this path. Paths resolve against selected environment cwd. [Sequential patch effects](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/apply-patch/src/lib.rs#L504-L718).
- A later failure can leave an already committed prefix or destination write. The implementation returns accumulated delta; failed writes can mark it inexact. The event layer explicitly preserves partial patch delta while returning a failed visible item. A failure status must not be counted as zero side effects without checking every marker/path. [Delta and failure](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/apply-patch/src/lib.rs#L423-L454), [write failure](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/apply-patch/src/lib.rs#L489-L501), [event failure handling](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/core/src/tools/events.rs#L418-L440).
- Native patch approval checks both update source and move destination. Code explicitly retains a platform sandbox even when paths appear writable because hard links can refer outside writable roots. Runtime follows symlinks when an otherwise-required sandbox has not been bypassed; otherwise it uses no-follow operations. Host `LocalFileSystem` validates sandbox host paths and chooses sandboxed writes from the explicit context; without a relevant sandbox context it selects unsandboxed FS. This does not establish a symlink/hard-link race result. [Patch safety](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/core/src/safety.rs#L90-L124), [source/destination check](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/core/src/safety.rs#L146-L177), [symlink choice](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/core/src/tools/runtimes/apply_patch.rs#L175-L197), [host FS choice](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/exec-server/src/local_file_system.rs#L95-L126).
- Workspace-write defaults protect `.git`, `.agents`, `.codex`, `.aws` metadata. Native resolution includes the existing `.git` file's `gitdir:` target for worktrees/submodules, alongside the pointer itself. Explicit grants and configured profiles affect the result; ordinary public root markers are not a substitute for real linked-worktree fixture evidence. [Default metadata policy](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/protocol/src/permissions.rs#L853-L868), [gitdir protection](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/protocol/src/permissions.rs#L2347-L2389).

## Proposed controls, not executed research results

Use unchanged ACP plus the archived #32 native candidate and public synthetic backend responses. Pair every deny with a working add/update/delete/move positive, inspect the selected worktree and outside canaries, and retain actual custom output plus ACP failure updates. Check missing/stale binding at dispatch; policy change during genuine approval wait is the smallest source-supported post-dispatch test. Test stable restoration after deny. Check absolute outside path, `..`, symlink-to-outside and public `.git` pointer/target controls under the actual requested profile. Keep direct raw `fs/copy` tests separate from custom patch Move, and clearly distinguish sender-dispatch revocation from receiver effect admission.

If a precise post-dispatch timing is unavailable in unchanged ACP, record that limit; do not substitute a source-only prediction for a native race result. A new receiver-FS lease design would need an explicit owner/attempt and held guard through each selected mutation, plus defined partial-commit and cancellation semantics. That is a proposed technical decision, not a confirmed implementation.

Child agents are a conditional separate control: both native spawn versions pass `Some(step_context.environments.clone())`, and child config starts from the parent's effective config plus captured settings/overrides. Parent admission must not be treated as proof of a distinct child thread's binding. No child runtime was exercised by this audit. [V1 environments](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/core/src/tools/handlers/multi_agents/spawn.rs#L116-L139), [V2 environments](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/core/src/tools/handlers/multi_agents_v2/spawn.rs#L184-L207), [child config](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/core/src/agent/child_config.rs#L102-L154).

## Audited source fingerprints

Every row matched the existing sender source index. GitHub links above describe pinned upstream files; prototype modifications are identified in this table and canonical #32 evidence.

| File | Status | SHA-256 |
| --- | --- | --- |
| `core/src/tools/handlers/apply_patch.rs` | pinned upstream | `75cc617033b88d61e306e1cc48e1c2552a3eaa9a6002da834d6f5c643d960cad` |
| `core/src/tools/runtimes/apply_patch.rs` | pinned upstream | `058641f57de8b390d1b4915deb283f67d11234ec7c4a44e2f0d7dca43e3ca992` |
| `core/src/tools/context.rs` | pinned upstream | `9a9acc6daab2112bd9ca1a07a1e1f2105b20a7f565878157c726ee0d7a3e00cc` |
| `core/src/tools/events.rs` | pinned upstream | `3feb1b42fd5d645af3d1761e3a0761ae19210c601d6ffa85c9a873a236eff912` |
| `core/src/tools/mod.rs` | pinned upstream | `510635e3ae67e4c63e1feaf6d645f25dc2590f54391645dddeec0a84c1d17616` |
| `core/src/tools/registry.rs` | archived prototype | `da0004d2bf8b566232d1f92333f2fce10fb35b5ff832d35849f0e003078f94ba` |
| `core/src/safety.rs` | pinned upstream | `e51f66251ebc60c4db8d2a5458ada9960a8845b49d7fabeb3bc2ae04b00f6b72` |
| `apply-patch/src/lib.rs` | pinned upstream | `5e6f736f3a4b66c1d651baa9b85ce6e6921de170faf901e6c2f78ad9a383eaf9` |
| `protocol/src/models.rs` | pinned upstream | `916a595e136f4d7e32ce259f50678ffe818af2ea6a92f54d8628f57bfd4d43e7` |
| `protocol/src/permissions.rs` | pinned upstream | `78a1c80b9b5cda29c563538d90003005d26c411eea533bd00db54a185a6aed54` |
| `exec-server-protocol/src/protocol.rs` | pinned upstream | `6ef5940f3fd348b63b331299c80426b3aedcfa503e01e956d3301139733ed9fe` |
| `exec-server/src/remote_file_system.rs` | pinned upstream | `4be3c98e04bdb8a225c3e53dd90506e197a37f2717d57619d21db9501d65c93a` |
| `exec-server/src/server/file_system_handler.rs` | pinned upstream | `fb61d2a279deb6bda510141c31355b0ac0b3456b89015531a8c0f08acc27410c` |
| `exec-server/src/server/handler.rs` | archived prototype | `271bb0f6baf22ff51390db80302a29cf1389e89a6f95d094c8a4bd54e92c9351` |
| `exec-server/src/local_file_system.rs` | pinned upstream | `e682413100b3fcee03d285f4108c16ece9d3da34c217a2910afc3c22fc568aa3` |
| `exec-server/src/sandboxed_file_system.rs` | pinned upstream | `f6112a8981bdb8c626405e52d64845a7bc9fc7ded5d1c73a10ca195cc9d9dbaf` |
| `exec-server/src/fs_sandbox.rs` | pinned upstream | `fb983066336be47363b12a5d3358d38b4e9ebee0c6c7fac67a7504711ce6e26d` |
| `exec-server/src/local_process.rs` | archived prototype | `ee27515d5dc9bb970e7ffdef2236c131da7a2fdca39c5e0ec3915bc0b40ee421` |
| `core/src/tools/handlers/multi_agents/spawn.rs` | pinned upstream | `b7ae7ee5342abd4c572ef994bc01a0acdc0a659a4a418860c72ada5e32193ce2` |
| `core/src/tools/handlers/multi_agents_v2/spawn.rs` | pinned upstream | `91102f864043adbf49f7d5aff6778d358ea99ad8c3d7d59fb913950fad60784f` |
| `core/src/agent/child_config.rs` | pinned upstream | `33d4dd6f70e6640af1059398272c48b88e16e0005105fbd1ef13e30456d39a39` |
