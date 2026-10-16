# Codex dispatch generation and revocation: pinned source audit

Status: **source-only finding; awaiting runtime evidence and a native binding decision**. This continues [Confinement #18](https://github.com/Riso-application-factory/zed-ai-crew/issues/18) under Human's [direction A](https://github.com/Riso-application-factory/zed-ai-crew/issues/29#issuecomment-6126432147). No executable, adapter, model, test, real account, credential, host managed preference or private configuration was run/read by this audit. Root owns the bounded synthetic counterfactual and tracker resolution. Candidate gates below are proposals, not implementation or verification.

## Sources and pins

Native source is OpenAI's [rust-v0.160.1](https://github.com/openai/codex/tree/rust-v0.160.1/codex-rs), extracted at `/private/tmp/codex-rust-v0.160.1/codex-rs`; the existing public bridge scratch copy is `/private/tmp/crew-codex-bridge-source-v0.160.1`. Line anchors below refer to that scratch copy, **including the three modified files**, unless explicitly marked ACP. The 27 other files in the fingerprint inventory were byte-compared against the original extraction and matched. Existing scratch modifications were read, not edited.

ACP source is installed `@agentclientprotocol/codex-acp` **2.2.2**, `/Users/binn/Library/Application Support/Zed/external_agents/registry/npx/codex-acp/node_modules/@agentclientprotocol/codex-acp/dist/index.js`, SHA-256 `6b1bb4e7e9caaf4aeca1042ae4aec2dc2433b0b0e4b1614b38a4d41699902aaa`. ACP line anchors refer to this exact bundled artifact, not repository HEAD. Prior inputs: [bridge seams](wayfinder-codex-native-bridge-seams.md), [raw ingress audit](wayfinder-codex-raw-ingress.md), [bridge runtime report](https://github.com/Riso-application-factory/zed-ai-crew/issues/18#issuecomment-6127320165), and [ingress runtime report](https://github.com/Riso-application-factory/zed-ai-crew/issues/18#issuecomment-6127987349). This audit does not repeat those runtime results.

## What the existing bridge actually gates

`ConfigManager::crew_public_fixture_gate` observes the effective native config and emits its public receipt; it rejects the named unsupported features, notify/memory/hooks and unowned MCP entries. Its receipt explicitly says `completeExternalAdmissionLease:false`. It is called after four final materialization/rebuild paths: latest with session layers, retained session config, default config and full CLI/request load. [Modified manager](/private/tmp/crew-codex-bridge-source-v0.160.1/app-server/src/config_manager.rs:100), calls at **293, 320, 420, 535**.

The added turn-start check obtains the retained thread config and invokes `load_latest_config_with_session_layers(...).await...?;`. **The newly materialized config is discarded**, rather than installed in the running session. The provider check then validates the retained route separately. Steering has the equivalent added pre-submit check. Therefore this detects named unacceptable policy at the check point; it does not provide a whole-context generation, update the active turn's config, or grant authority to later tools. [Modified turn start](/private/tmp/crew-codex-bridge-source-v0.160.1/app-server/src/request_processors/turn_processor.rs:537), [steering](/private/tmp/crew-codex-bridge-source-v0.160.1/app-server/src/request_processors/turn_processor.rs:1042).

Core `TurnContext` holds a turn-scoped `Arc<Config>`, separate initial settings, step settings, provider, role instructions and environment snapshots. The router clones the same turn from `StepContext` into `ToolInvocation` and calls the registry. These retained objects do not automatically consult app-server `ConfigManager` on every call. [Turn context](/private/tmp/crew-codex-bridge-source-v0.160.1/core/src/session/turn_context.rs:305), [router](/private/tmp/crew-codex-bridge-source-v0.160.1/core/src/tools/router.rs:350). A system requirements change after the app-server turn check can consequently precede a later native dispatch without another bridge check. This is a source-derived hypothesis for root's runtime counterfactual; it is not a claim that an escape, real policy violation or newly enabled hook executed.

## Why the existing revision is narrower than the needed lease

`ApplicationPolicySnapshot` contains network revision, destination policy and cloud bundle. The native loader serializes refreshes, reads local application policy, captures cloud policy, publishes it and later checks both revision and snapshot pointer identity. This is a real guard against superseding that application-policy load. It is **not** a revision of all user/project/system/default/requirements sources, session ownership, role bytes, worktree identity, executor profile and all runtime mutations. Unrelated feature requirements need not change a destination-network revision. [Snapshot fields](/private/tmp/crew-codex-bridge-source-v0.160.1/app-server/src/config_manager.rs:59), [refresh/check](/private/tmp/crew-codex-bridge-source-v0.160.1/app-server/src/application_network.rs:68).

A candidate native admission lease would need to bind the admitted effective context to a thread/turn and operation, with explicit policy-source revision and revocation semantics. Comparing a network diagnostic number alone would incorrectly claim broader coverage. Re-reading files once at dispatch also leaves a check/use interval unless the policy owner controls publication and the effect boundary participates in the same admission protocol. Filesystem path identity needs its own stable ownership binding; hashing a path string is insufficient. These are design requirements inferred from the observed split ownership, not a selected production contract.

## Native effect boundaries and remaining waits

| Surface | Observed native path and retained authority | Candidate check placement and limit |
| --- | --- | --- |
| Common model tool dispatch | Parallel runtime waits for tool readiness and its read/write execution lock, then invokes router. Registry may run pre-tool hooks **before** calling the handler. `ToolInvocation` carries `Session`, `StepContext`, retained turn, call ID and cancellation token. `parallel.rs:196–226`; `router.rs:350–383`; `registry.rs:527–653, 811`. | A common admission check belongs after readiness/parallel lock acquisition and **before pre-tool hooks**. A handler-only check would leave those hooks as an earlier effect. Further asynchronous waits still require an effect-boundary recheck or a held revocable lease. |
| Unified shell/exec | Handler prepares environment/permissions and allocates process ID before manager execution (`handlers/unified_exec/exec_command.rs:388–444`). Orchestrator awaits approvals/network approval and can retry (`orchestrator.rs:52–104, 317, 493`). Runtime prepares shell/sandbox/network and opens sessions (`runtimes/unified_exec.rs:619–736`). Manager eventually calls remote `backend.start*` or local `spawn_process` (`unified_exec/process_manager.rs:1317–1356, 1413–1428`). | Check before each spawn/start attempt, after approval and preparation waits. A registry-only check can go stale while approval waits. The independent executor must validate its own bound generation at effect admission for a race-resistant remote claim. Existing process metadata is thread/turn/call attribution, not that new lease. Long-lived processes and `write_stdin` need separate revocation handling. |
| MCP | Handler captures a `PreparedMcpCall` (`handlers/mcp.rs:200–221`). Native approval may wait or use remembered approvals (`mcp_tool_call.rs:265–290, 1480–1592`). `handle_approved_mcp_tool_call` executes preparation under a captured catalog; preparation can persist approvals, upload/rewrite files and await sandbox metadata (`mcp_tool_call.rs:438–544`). `PreparedMcpCall::call_with_preparation` checks catalog snapshot, then adds trusted context and sends actual `tools/call` (`codex-mcp/src/binding.rs:304–371`). | There is already a **catalog snapshot lease**, which must be preserved. It is not a full current-policy lease. Recheck before irreversible preparation and before transmission after awaited preparation/trusted-context work; bind the exact admitted server/connection/environment/catalog too. A check after `tools/call` cannot undo server effects. MCP startup and autonomous server behavior are distinct boundaries. |
| Apply patch | Both the direct tool and shell interception converge on `execute_verified_patch`; this derives permissions, emits begin and awaits orchestration (`handlers/apply_patch.rs:360–383, 456–488, 504–608`). Runtime directly calls the selected environment's filesystem through `apply_patch_with_options` (`runtimes/apply_patch.rs:168–198`). | It is a filesystem effect, not necessarily a shell process. Recheck after patch approval at runtime entry before the first read/write operation, including shell interception and every retry. An executor process-start gate alone misses this path. Multi-file writes and rollback/partial delta require semantics beyond a single pre-write hash. |
| Native subagents | V1/V2 tools prepare child config then call `agent_control.spawn` (`handlers/multi_agents/spawn.rs:98–139`; `multi_agents_v2/spawn.rs:133–207`). Child shared config **clones the parent's turn config** and modifies model/role/runtime settings; resume likewise derives from that turn (`agent/child_config.rs:123–157`). | Check child creation/resume and its own later dispatch, bind parent ownership and constrain child context. App-server-only new/resume admission does not establish coverage of this core-internal path. Current bridge gate does not categorically reject native multi-agent features. No child runtime was tested by this audit. |

For the original-equal files in the table, the first-party links are under `https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/` followed by the stated path and line. Local anchors preserve the exact inspected extraction and scratch context; fingerprints below make source identity reviewable.

## Source refresh, synthetic Base64 and recovery

The fixture `main.rs:165–184` reads `managed-defaults.toml` and `managed-requirements.toml` once at startup and encodes those bytes into `LoaderOverrides`. `ConfigManager` stores that value and passes **clones** into later loads (`config_manager.rs:48–49, 338, 524`). The native managed-requirements loader decodes the override string when present; local application policy skips host preference synchronization for a supplied Base64 override (`config/src/loader/managed_requirements.rs:80–103`; `application.rs:60–72`; `macos.rs:294`). Ordinary managed defaults follow the same supplied-string path (`layer_io.rs:121–126`). Editing the original synthetic managed-input file in the same process does not mutate the retained string. A replacement process does re-read it. That is public fixture behavior, **not evidence of live host MDM freshness**.

By contrast, system requirements load from the explicit file on each requested managed/full load (`managed_requirements.rs:101–103`; `application.rs:51–56`). Legacy managed-file loading reads the explicit file again (`layer_io.rs:107–118`). Full requirements composition retains system, cloud, legacy and managed-preference sources in the native composition order (`managed_requirements.rs:105–124`). Fresh checks see those files when invoked; the existing bridge has no continuous file watcher or automatic per-tool reload.

ACP's automatic recovery and provider restart synthesize resume requests from saved session ID/cwd/additional directories/MCP **inside the adapter**, bypassing the external raw ingress proxy (`ACP 43338–43349, 43451–43466`). Its process generation and session lifetime checks fence lifecycle publication (`ACP 43420–43425, 43472–43473`), not the full policy/context lease. Root's candidate recovery test should preserve the actual unchanged adapter trace and replacement initialize/resume sequence, never substitute a recording peer.

Native cold ordinary resume does load a fresh config through the gated `ConfigManager::load_for_cwd`, but holds a prepared-config cache and loops after releasing the thread-list permit (`thread_processor.rs:3924–3966`). Reusing a prepared config when `ResumeConfigState` still matches is not a complete policy generation check; a change between materialization and subsequent publication remains a source-level race to test. Already running attach returns through `resume_running_thread` (`thread_processor.rs:3669–3686`); it should not be assumed to re-materialize full policy. Parent-owned V2 children resume through their parent rather than caller config (`thread_processor.rs:3778–3825`). These distinct paths must preserve native ownership checks.

Core's legacy user-config reload explicitly says feature gates and legacy notify remain session-static (`core/src/session/mod.rs:2205–2212`). It refreshes selected layer-backed state, so it is not a replacement for full lease admission. Native daemon recovery is also distinct from ACP process recovery: its recorded-turn helper excludes remote identities/configurations (`core/src/session/daemon_recovery.rs:13–32`). Root's independent remote-executor fixture should not report automatic daemon continuation from that helper.

## Cancellation is available; policy-triggered revocation is absent from this patch

ACP cancel requests mark the active prompt and invoke native interrupt (`ACP 45422–45429`). Native app-server submits `Op::Interrupt`, with acknowledgement tied to terminal turn state (`turn_processor.rs:1639–1654`); core interrupt calls `abort_all_tasks` (`session/mod.rs:5054–5060`). Task abort cancels the token, waits a grace interval, aborts the task handle and calls task cleanup (`tasks/mod.rs:921–966`). Parallel tool runtime reacts by aborting its dispatch task and emitting aborted output (`tools/parallel.rs:249–280`). That does not retroactively undo an MCP request or completed patch.

Background terminal termination exists separately (`tasks/mod.rs:903–907`); the default task abort hook is a no-op (`tasks/mod.rs:205–220`). Do not equate an emitted `TurnAborted` or task future cancellation with proof that all executor children, long-lived MCP servers or background terminals were terminated. Root needs real named PID/endpoint/late-call controls for such a claim. The three-file public patch installs neither a live policy-change observer nor a policy-triggered call to these cancellation paths.

## Smallest bounded test seam for this session

Keep the already verified modified binary and unchanged installed ACP identity. On the public synthetic backend, mutate the **explicit system requirements file** exactly before sending a tool response for an already admitted turn. Observe a harmless in-worktree exec marker or an owned MCP event, retain the changed requirement for a subsequent-turn reject control, then restore it for a positive control. Repeat with malformed public requirements if useful. If the harmless tool executes, record **mid-turn stale-admission counterfactual observed**, not escape or newly enabled hook execution. Outside-access executor controls remain separate.

For managed-source contrast, mutate only the original synthetic managed-requirements file before a subsequent turn: expected source-derived result is unchanged retained Base64 until replacement startup. For recovery, stop only the recorded public fixture process after a persisted turn, retain public system/managed revocation across restart, and inspect actual replacement initialize/resume/backend behavior. Failure at initialize is evidence of replacement startup rejection; it is not a successful resumed-session policy check. Baseline recovery must first show the real adapter replaces native and restores a persisted session. No private credentials or actual MDM sources are necessary.

Return criteria: root links ordered mutation/receipt/tool/native-wire evidence and exact binary/adapter/source identities, distinguishes observed stale behavior from a proposed fix, and keeps #18 open while the native full-context dispatch/revocation contract is unresolved. This audit executed none of these proposed controls.

## Maintenance implication and remaining fog

Closing only the observed between-turn gap can remain in the small app-server patch; closing the native effect boundary spans core registry, approvals/retries, exec manager, MCP catalog/transport, direct filesystem patching, child ownership and recovery. `ConfigManager` is app-server-owned while those effect paths are core/MCP/executor-owned. A production contract therefore needs an explicit dependency seam and propagation of admitted identity/revocation, rather than importing app-server code into core or reconstructing authority from an environment variable. Exact source hashes alone do not decide update cadence, fork support, patch review ownership or release authority. None is approved here.

Remaining fog includes full policy-source provenance/generation publication, role/skill/worktree drift, native internal turns and child dispatch, effect admission across remote executor, cancellation of already running operations, recovery cache races, all startup/background effects and the complete three-provider matrix. A bounded runtime counterfactual advances the decision; it does not resolve those items or complete Wayfinder.

## Fingerprint inventory

SHA-256 of inspected native files; `modified` means existing public bridge patch, all other entries compared byte-equal to the original extraction:

```text
app-server/src/main.rs 11a7909ad4958e6eaf6dac967742e2418eb0c78b541dd5be62f49fa08300efd0 modified
app-server/src/config_manager.rs a16ac473f19e72f6d73c5eff0d5e057b3a9edff3a89a78cae24765dcaaf0bd70 modified
app-server/src/request_processors/turn_processor.rs f5e20d18ee38b874c6c08be0be433a11fb727c9f71995cf5525d03c88c0bf6ad modified
app-server/src/request_processors/thread_processor.rs 786c0549e5c1b57421e03472f3b8c3519090849fb8401163f3da5cc2c55b66be
app-server/src/application_network.rs 210f384f48bea9d1d32a1003f008893986d797943a775430afe49e0d48cfde0b
core/src/tools/registry.rs d2793cddbdc34ad8d849a991dafd5871a3ce8ded4b33c446b2c1934e1cee526b
core/src/tools/router.rs 2f1fe58ffc726342eeb77c9aff3fff79fadc5abcf414e7af7ba018058a8c649c
core/src/tools/parallel.rs b924fb1e123e503a896fa99d661e61da0c54139f7729e71ac61405d7dd636408
core/src/tools/orchestrator.rs bc818810e1bd48001157d33df59b4624033595d13fd5070598b997627ee2e2f3
core/src/session/turn_context.rs 96c7e354170c784204d50cf3cd218133f9a906a887d9c06261f836f95dd0edd1
core/src/session/session.rs 2da4c0288596269fbb6df6d9a04a389e8ce0184be77ee2bcce765521e63702a5
core/src/session/mod.rs d05728bce8f6f3163d20ec4286bf964d7dfda27a39d4669b755e7a2e51849ecf
core/src/tasks/mod.rs 400b02b66b2fd829ee876d5094c0ae3ab5fb52536ce2d220f8b257e6ee6c41c5
core/src/tools/handlers/unified_exec/exec_command.rs 4698f6c794151998d52bb6cc6088dd03039e9665fd11037f6c22cf0d4fdd2f2d
core/src/tools/runtimes/unified_exec.rs 01025d66dba5aaabbf40a564f0b74b345caa15b4f1df9c503857ab7a66928cd1
core/src/unified_exec/process_manager.rs ec4878fbe0da4d8e4ae40629702a6c509a7168fd5494c0453d4081cd8f7fc235
core/src/tools/handlers/mcp.rs f52c5ea41ecdc41f0dad9a69494baf9ffbc23e90cd6bc6f8802b1c577775570d
core/src/mcp_tool_call.rs 509b10168a9a1e99abd0f5722ad5bf377645dbb2e446ead6c324daef7a84af58
codex-mcp/src/binding.rs 3b9f48ce4f861f979b465fb8ce4c3ef25a0ca0c2aa57d0a8b85429eb298a4ac8
core/src/tools/handlers/apply_patch.rs 75cc617033b88d61e306e1cc48e1c2552a3eaa9a6002da834d6f5c643d960cad
core/src/tools/runtimes/apply_patch.rs 058641f57de8b390d1b4915deb283f67d11234ec7c4a44e2f0d7dca43e3ca992
core/src/agent/child_config.rs 33d4dd6f70e6640af1059398272c48b88e16e0005105fbd1ef13e30456d39a39
core/src/tools/handlers/multi_agents_v2/spawn.rs 91102f864043adbf49f7d5aff6778d358ea99ad8c3d7d59fb913950fad60784f
core/src/tools/handlers/multi_agents/spawn.rs b7ae7ee5342abd4c572ef994bc01a0acdc0a659a4a418860c72ada5e32193ce2
core/src/session/daemon_recovery.rs e7f7a38c29655d033e4cc8a50a8c1866d22fb4f566ac8ddceaaa4093061a9178
config/src/loader/application.rs 2ba93ecbbfbc777ea004a135a2241a4df0ec68c1a7946cb16b51fc853a810da3
config/src/loader/managed_requirements.rs 00595b2320674364053537213638735da20739a8864c92947bd4acb869fcf448
config/src/loader/layer_io.rs a090443bc10317e086cd552c29ec4558917bdade0b397abd5229f81f93042022
config/src/loader/macos.rs 36dd560f67b3b45bf3d63f4e2b962571456c12dc2f9ca55304f8729d49bee0c5
config/src/state.rs b9a64a791a8b7884626890f06b13fb4630c5160f75ead198635e0111ecdd46ed
```
