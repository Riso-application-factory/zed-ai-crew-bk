# Codex native bridge: bounded prototype seams

Status: source audit; proposed fixture seams, not executed runtime evidence or a production architecture decision. Human selected direction A in [decision #29](https://github.com/Riso-application-factory/zed-ai-crew/issues/29#issuecomment-6126432147). Return to [Confinement #18](https://github.com/Riso-application-factory/zed-ai-crew/issues/18).

Sources inspected: the local `rust-v0.160.1` source extraction under `/private/tmp/codex-rust-v0.160.1/codex-rs`, the buildable snapshot copy under `/private/tmp/crew-native-snapshot-source-v0.160.1`, and installed ACP 2.2.2 `dist/index.js`. The copied workspace includes earlier fixture/build accommodations: it must be identified as a modified build, never attested as the installed stock executable. No runtime, account, real managed preferences or credential was read by this audit.

## Smallest executable and bootstrap injection

The workspace already defines a standalone `codex-app-server` executable in the `codex-app-server` package. Building `-p codex-app-server --bin codex-app-server` avoids the CLI/TUI entry point, although the package still depends on core and many extension crates. Its pinned toolchain is Rust 1.95.0. A successful small config-library build does not demonstrate this larger executable will build. [Package](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/app-server/Cargo.toml#L1), [toolchain](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/rust-toolchain.toml#L1).

`app-server/src/main.rs` constructs `LoaderOverrides` and passes it directly to `run_main_with_transport_options`. Replace only that fixture construction in a scratch copy with explicit fixture-owned paths and two nonempty base64 inputs. Do not use the existing `DISABLE_MANAGED_CONFIG` shortcut or `without_managed_config_for_tests`, both of which provide empty MDM inputs. Keep `ignore_managed_requirements`, `ignore_login_requirements`, `ignore_user_config` and `ignore_project_config` false. `LoaderOverrides` has `user_config_path`, `managed_config_path`, `system_config_path`, `system_requirements_path`, `managed_preferences_base64` and `macos_managed_config_requirements_base64` fields. [Entrypoint](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/app-server/src/main.rs#L81), [override fields and shortcuts](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/config/src/state.rs#L41).

The app-server startup performs native `ConfigManager::load_startup_config`, then authenticates from that config and loads config again with the cloud loader installed. Supplying one initial external snapshot would not replace the actual native loading sequence. Force strict config in the prototype launch so loader failures cannot take the normal non-strict default repair path. [Bootstrap](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/app-server/src/lib.rs#L530), [strict/fallback branch](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/app-server/src/config_manager.rs#L321).

Explicit nonempty MDM requirement injection bypasses the actual preference synchronization call while preserving native requirement parsing and composition for those synthetic values. That is a public fixture identity, not a method of obtaining or superseding host policy. System requirements still load from their explicit file. [Local application requirements](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/config/src/loader/application.rs#L50).

Installed ACP `startCodexConnection` invokes `CODEX_PATH` with `['app-server', ...startupArgs]` (installed bundle line 27852). A public wrapper must validate/remove that first literal argument before exec of the standalone app-server, retain strict-config and pass remaining actual arguments. stdout must remain native JSON-RPC; record fixture audit events separately. This uses actual ACP transport and a modified native binary, not a recording peer.

## Native admission boundary and full effective config

`ConfigManager::load_with_cli_overrides` refreshes application policy, merges request overrides after startup CLI overrides, invokes `codex_core::config::ConfigBuilder`, checks the policy load generation, then applies network policy, runtime feature enablement and arg0 paths. A bounded fixture gate placed immediately before its `Ok(config)` observes the native effective result after managed and runtime constraints. Startup-only flags cannot be treated as immutable because request overrides come after them. [Full load and merge](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/app-server/src/config_manager.rs#L429).

Useful native types/accessors:

```rust
// Existing types/accessors; illustrative observation, not a complete admission rule.
let hooks_enabled = config.features.enabled(codex_features::Feature::CodexHooks);
let has_notify = config.notify.is_some();
let servers = config.mcp_servers.get();
let requirements = config.config_layer_stack.requirements();
```

`Config` publicly holds the full `config_layer_stack`, effective `features`, `notify` and constrained MCP map. Its features use `ManagedFeatures`; that wrapper normalizes configured features to required pins before returning the value, then enforces those pins on subsequent mutation. Thus `hooks=false` in a request does not erase mandatory `hooks=true`; gate the resulting native value, and reject unsupported mandatory capability rather than disabling it. Requirement provenance is accessible through the layer stack instead of the external projection. [Config](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/core/src/config/mod.rs#L609), [MCP field](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/core/src/config/mod.rs#L880), [managed normalization](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/core/src/config/managed_features.rs#L69), [requirements accessor](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/config/src/state.rs#L364).

Thread creation loads native config before project-trust processing and may reload with newly persisted or in-memory project trust. A gate in the central full-load function sees those resulting full loads; it should not claim the first load captures the final trust state. [Thread start](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/app-server/src/request_processors/thread_processor.rs#L1338).

Two retained-session helpers rebuild and return their own effective config: `load_latest_config_with_session_layers` and `load_retained_session_config`. A gate only in the central loader does not automatically observe their final rebuilt values. Place the same gate after the final runtime features in these helpers if the prototype exercises those paths. [Retained session paths](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/app-server/src/config_manager.rs#L210).

## Freshness at turn boundary

Native `turn_start_inner` calls `check_thread_model_provider` before turn submission. That check reloads current managed requirements and application policy but compares retained model/provider selection and definition; it does not validate all hooks/MCP/features for the retained session. To test a changed fixture after preflight, an explicit prototype turn gate can rebuild with `load_latest_config_with_session_layers(&current.config_layer_stack, current.cwd.as_path())`, then validate the complete rebuilt result before `submit_core_op`. [Turn boundary](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/app-server/src/request_processors/turn_processor.rs#L523), [provider-only check](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/app-server/src/config_manager.rs#L266), [native rebuilding helper](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/app-server/src/config_manager.rs#L210).

The manager keeps cloned `LoaderOverrides`. Base64 strings injected at startup are retained synthetic values, not a live file watcher; mutate `system_requirements_path` for a genuine subsequent native reload contrast, or explicitly implement fixture-source refresh if base64 itself must change. No claims of continuous OS/MDM change observation or race-free whole-config generation follow from these seams. [Manager fields](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/app-server/src/config_manager.rs#L44), [full load clone](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/app-server/src/config_manager.rs#L459).

## Independent executor and MCP selection

Native `environments.toml` supports `default`, `include_local=false` and explicit remote `[[environments]]` entries with `id` and `url` (or stdio `program`, never both). A prototype can use an independently started confined websocket executor and an explicit selected ID. Using stdio would cause the app-server to spawn that executor as its child with additive environment; that is not the already tested independent launch. A loopback websocket server still needs its own strict outer confinement and endpoint controls. [Environment configuration and validation](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/exec-server/src/environment_toml.rs#L24), [transport selection](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/exec-server/src/environment_toml.rs#L145).

```toml
default = 'crew-executor'
include_local = false

[[environments]]
id = 'crew-executor'
url = 'ws://127.0.0.1:FIXTURE_PORT'
```

MCP config has its own `environment_id`, defaulting to `local`. Thread default selection is insufficient. Preserve an explicit owned native MCP configuration with the executor ID, reject unadmitted local entries, and verify dispatch rather than guessing from environment selection. [MCP type](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/config/src/mcp_types.rs#L233), [defaulting](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/config/src/mcp_types.rs#L557).

Installed ACP `createSessionConfig` deduplicates requested MCP names against config/read and merges remaining servers; `createMcpSeverConfig` emits command/args/env for stdio or URL/headers for HTTP, without environment ID (installed bundle lines 35437–35530). An unmodified adapter can retain preconfigured owned MCP entries through deduplication, but raw incoming requests must still be validated separately: native final config cannot attest an input the adapter discarded. That limitation was already observed in #18 and is not fixed by this native seam.

## Synthetic endpoint policy caveat

The native application network restricted policy allows exact normalized HTTPS/WSS hosts; plain HTTP/WS loopback would be denied if an application domain policy activates Restricted mode. For a public synthetic provider/executor contrast, nonempty MDM feature requirements can coexist with an unmanaged synthetic application transport policy; an outer sandbox/gateway must enforce allowed fixture endpoints. Do not call this a demonstration of bypassing or disabling real host policy. Alternatively provide TLS fixtures and exercise native restricted transport explicitly. [Allowed protocols](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/http-client/src/network_policy.rs#L28).

## Return criteria and gaps

Root owns patch, build and actual runtime tests. A useful result binds hashes of the modified source/executable and unchanged installed adapter, observes native startup/session creation, contrasts mandatory requirements and malformed/change-after-preflight rejection, and observes actual tool dispatch plus outside file/process/FD/egress deny controls with positive controls. This audit executes none of those checks. It identifies a bounded patch seam, not a full supported external bridge contract. Whole-config race/generation, raw ingress validation, other native dispatch paths, dynamic feature updates, credential bootstrap, host FS/terminal, cancellation and complete three-provider confinement remain outside this source-only finding unless the actual prototype proves them.
