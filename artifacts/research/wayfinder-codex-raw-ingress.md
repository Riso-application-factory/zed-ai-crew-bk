# Codex ACP raw ingress: source audit and bounded gate candidates

Status: source-only audit for [Confinement #18](https://github.com/Riso-application-factory/zed-ai-crew/issues/18), following Human's [direction A](https://github.com/Riso-application-factory/zed-ai-crew/issues/29#issuecomment-6126432147). This audit ran no adapter, native runtime, model, policy loader or credential/config extraction. Candidate rules and test cases below are not implemented or verified by this document. The root session owns the public fixture and actual runtime evidence.

## Primary sources and identity

The owning primary source is installed `@agentclientprotocol/codex-acp` **2.2.2**, `/Users/binn/Library/Application Support/Zed/external_agents/registry/npx/codex-acp/node_modules/@agentclientprotocol/codex-acp/dist/index.js`. SHA-256: `6b1bb4e7e9caaf4aeca1042ae4aec2dc2433b0b0e4b1614b38a4d41699902aaa`. Every bundle line range below refers to that exact artifact, including its bundled ACP SDK and Zod code. Package manifest SHA-256: `517931a5786327ec60e877a992b7872f2d6a1c6d588e60834ff1a794d1b9f6c5`; manifest version was read. The manifest identifies the [adapter's first-party repository](https://github.com/agentclientprotocol/codex-acp) and the [versioned public package](https://www.npmjs.com/package/@agentclientprotocol/codex-acp/v/2.2.2). Repository HEAD is not evidence that it matches this installed bundle; the local artifact hash and line ranges are the pin.

Prior confirmed role/skills evidence is [ticket #17](https://github.com/Riso-application-factory/zed-ai-crew/issues/17#issuecomment-6109950179), with local [report](../evidence/wayfinder/role-skills/report.md) SHA-256 `ca3aaf3751666fda0b76e0725ece7fc03a00f349d34969ab3f4131818b9c3262` and [probe source](../evidence/wayfinder/role-skills/probe.py) SHA-256 `ba9f87ebf53dd96361b76fa0cb0aed1bf1693b1a3eeb7df556dca60200a171df`. This audit read those artifacts; it did not repeat their execution.

## Where input loses information

SDK schemas run before the adapter handlers: `parseParams` calls `parser.parse(params)` (bundle **27216–27224**); the registered entry points use these parsed request specs (**27249–27260**, **45716**). Regular bundled Zod `object` has no catchall by default (**22302–22309**); its parser allocates a fresh object, walks only known shape keys, and returns without copying unknown keys (**7767–7809**). Thus unknown request and MCP fields can disappear before native config sees them.

The SDK's `vecSkipError` catches each bad member and filters it out (**23970–23972**). `defaultOnError` catches an entire invalid value; `requiredDefaultOnError` rejects a missing value but catches parse failures for a present value (**23913–23929**). These are compatibility behaviors, not an admission policy. A present malformed `mcpServers` collection can become `[]`; a mixed array can lose malformed server entries. Malformed additional directory values can similarly disappear. A native gate downstream of this adapter cannot attest or reject fields/members already discarded.

| ACP request | Recognized scope fields in this pin | Owning bundle lines |
| --- | --- | --- |
| `session/new` | `cwd` string; optional `additionalDirectories`; required `mcpServers`; optional/null `_meta` record | 25222–25227 |
| `session/load` | Same, plus required `sessionId` | 25228–25234 |
| `session/fork` | `sessionId`, `cwd`; optional `additionalDirectories`, optional `mcpServers`, `_meta` | 25244–25250 |
| `session/resume` | Same as fork | 25251–25257 |

These schemas accept string directory members and compatible MCP variants; they do not establish worktree ownership. New/load require the MCP field to be present, whereas fork/resume permit omission. Do not attribute omission rejection to stock fork/resume.

## Roots, trust and skill scope

`readAdditionalDirectories` chooses `additionalDirectories ?? _meta.additionalRoots`, requires strings, nonempty entries and absolute paths, and deduplicates by exact string against `cwd` (**35820–35854**). The legacy metadata reader first removes nonstring/blank values, trims strings and deduplicates them; malformed metadata can disappear. An explicit `[]` suppresses metadata roots. This code does not resolve symlinks, collapse `..`, check existence, compare inode identity, or prove containment in an admitted worktree.

New/resume/load/fork all call this root reader and share `createSessionConfig` (**35227–35354**, fork helper **34654–34684**). The mapper marks every root `projects[root].trust_level = "trusted"` and appends additional directories to writable roots (**35437–35456**, **35855–35870**). `refreshSkills` derives `<additional root>/.agents/skills` and updates the native extra-roots setting before the session load (**35504–35515**). Therefore admitting a root also changes project-trust, writable and skill-discovery scope; checking it only at eventual file access misses prior effects.

The actual turn mapper adds saved additional directories to the selected workspace-write sandbox policy (**35598–35616**, **35878–35885**). An upstream gate should bind the admitted canonical worktree identity and scope to each session and verify scope again for all four opening methods. The candidate can reject relative/escaping/symlink roots rather than invent a broader product scope. Race-free filesystem identity between validation and eventual native use remains a separate boundary problem.

## MCP collisions and discarded configuration

Stdio MCP recognizes `name`, `command`, `args`, `env`; HTTP recognizes `name`, `url`, `headers`, `type:"http"`. Env/header entries have string `name` and `value` plus optional `_meta` (**24150–24154**, **25181–25221**). ACP/SSE variants parse but the adapter explicitly rejects them; HTTP maps URL/headers; stdio maps command/args/env (**35517–35540**). The mapper emits no `environment_id`, cwd or executor ownership field. An extra raw `environment_id` field can be stripped by the SDK and is not made effective by the mapper.

`sanitizeMcpServerName` replaces every Unicode `White_Space` character with `_` (**33354–33357**). `createSessionConfig` maps names through it, filters requested names that already occur in effective config **or any returned config layer**, then builds an object with `Object.fromEntries` (**35460–35501**). Filtering is normally enabled; `DISABLE_MCP_CONFIG_FILTERING="true"` disables it (**35816–35822**). Two distinct raw names can sanitize to the same name. Same-name duplicate array entries can collapse, with later values overwriting earlier values. Requested entries shadowed by native config disappear from the final native request. Even an existing disabled entry can cause the request to be skipped; the adapter's own diagnostic states that (**33362–33368**).

Env and HTTP header arrays also become `Object.fromEntries`, so exact duplicate names collapse (**35517–35540**). Header name comparison at this JavaScript step is case-sensitive; downstream HTTP behavior must not be inferred from that. A conservative candidate should reject case-insensitive header duplicates before conversion and reject unapproved env/header values or additions. None of these source observations is evidence that an exploit executed.

For the existing public bridge fixture, native preconfiguration of `crew_owned` with `environment_id="crew-executor"` is the binding under test. Validating a requested same-name server is necessary even when the adapter later retains the preconfigured entry. Admission based only on the final native MCP map cannot distinguish an approved raw request from an attempted shadow that was discarded.

## AIR role binding must be preserved

The actual role channel is `_meta.jetbrains.air.customInstructions` as a string. `airCustomInstructions` extracts exactly that path (**28093–28102**); new-session passes it as native `developerInstructions` (**35331–35342**). The reader does not require a wire-level AIR `version` field. The prior actual role probe populated this exact path without such a version requirement (probe **244**). Crew's confirmed versioned role binding must not be silently replaced with a newly required adapter wire field.

Load/resume/fork do not call `airCustomInstructions`; they rebuild config and rely on persisted/forked native context (**35227–35303**, **34654–34675**). The prior role evidence observed R1 preserved on **load**, while live skill files changed to R2; it did not prove explicit resume/fork or hot role replacement (role report sections “What was actually observed” and “Remaining limits”). A candidate gate can preserve the agreed role bytes/revision in its own binding and permit the known AIR metadata shape; it must not report that sending new instructions on load replaces the native role.

Reject unknown/scope-affecting metadata before stripping, while keeping validated agreed role metadata. Unsupported AIR fork metadata should be rejected or separately admitted: `_meta.jetbrains.air.fork` version1 selects a message/fingerprint/occurrence by reading thread history (**34689–34725**). Blanket `_meta` rejection would break the confirmed role channel; blanket `_meta` acceptance would leave legacy roots and future capability fields unreviewed.

## Other entry points and internal recovery

Provider/auth mutation is independent of session/new. `providers/set` accepts `providerId`, `apiType`, `baseUrl`, optional header record (**25164–25172**), applies gateway config (**35026–35059**, **35097–35117**) and restarts/resumes active sessions (**43293–43362**). `authenticate` accepts `_meta.gateway` with baseURL, headers and providerName (**34938–34949**), or `_meta["api-key"].apiKey` (**34837–34845**); startup env `DEFAULT_AUTH_REQUEST` can seed authentication (**45656–45688**). These paths require separate account/provider admission; a session-only raw gate cannot protect them. Candidate public fixture routing should deny them, not perform real login or silently authorize provider changes.

`session/set_mode` changes the mode consumed on the next turn. `session/set_config_option` permits mode, collaboration mode, model, reasoning effort and fast mode (**43644–43759**); collaboration mode calls native `thread/settings/update` (**35619–35625**). A new-session-only gate cannot attest those subsequent values. A bounded fixture can reject mutation methods it has not exercised, clearly recording that support is incomplete.

`_codex/hooks/trust` verifies hash then writes `hooks.state.<key>.trusted_hash` with config batch write and reload (**39928–39951**, parser **45632–45635**). Goal resume/set can start turns; steering and legacy model extension methods are separately registered (**42515–42590**, **45716**). They must enter their own validated dispatch boundary or remain denied in the candidate. No inbound ACP remote-control start method was found in this bounded registered-method inventory: `remoteControl/status/changed` is an ignored native notification (**31486**). That absence is not proof that every native extension is absent or confined.

Automatic app-server recovery and provider restart reconstruct resume requests internally from saved cwd/additional directories/MCP (**43338–43349**, **43451–43466**). Such requests do not traverse a proxy placed solely between the external client and adapter. Adapter session/recovery counters fence local lifecycle publications; they are not a complete config/policy/owner generation lease. An external raw gate must be paired with a native admission boundary for recovery, or explicitly leave that behavior unverified.

Startup `CODEX_CONFIG`, `MODEL_PROVIDER`, `CODEX_PATH`, `DEFAULT_AUTH_REQUEST` are separate inputs (**45655–45687**). Raw ACP validation cannot attest their provenance. The fixture/native bridge must retain its launch identity checks and native effective-config gate; none is superseded by this ingress candidate.

## Proposed executable checks for the root fixture

Use unchanged installed adapter plus the existing modified native binary. Observe that an upstream rejection produces **zero forwarded adapter input and zero backend/executor call for that request**, with a successful ordinary session/prompt control before or after the denial. Hash/session receipts attest the gate's own observation; do not substitute a recording peer for actual native execution.

| Case | Expected bounded candidate result / evidence |
| --- | --- |
| Exact admitted cwd, `mcpServers:[]`, agreed AIR role | Forward intact; actual session/new and prompt succeed. Preserve metadata bytes/meaning; no claim of role obedience from an admission receipt alone. |
| Approved `crew_owned` request with exact public command/args and empty env | Forward; actual owned MCP dispatch succeeds through the preconfigured independent executor. Show dedup final config does not replace raw validation. |
| Same name with different command/args or added env; unknown `environment_id`/cwd | Reject before adapter; prove no requested executable runs. |
| Duplicate same MCP name; `crew owned` versus `crew_owned`; Unicode-space collision | Reject all ambiguous raw entries before `Object.fromEntries` or sanitization. |
| Duplicate env name; extra dummy env value; duplicate or case-shadow HTTP headers | Reject before collapse; public dummy values only. HTTP is allowed only if independently owned/validated, otherwise reject unsupported transport. |
| Missing/malformed MCP collection; mixed malformed member; wrong-type env/header | Reject explicitly; do not count SDK fallback/drop as successful raw validation. |
| Relative, empty, `..`-escaping, outside or symlink additional root; invalid member | Reject before skill-root/trust update. If `..` remains in-scope canonically, reject ambiguous spelling or normalize under an explicitly documented candidate rule. |
| Canonical root list plus conflicting `_meta.additionalRoots`; malformed legacy roots | Reject shadow/hidden expansion even if canonical `[]` would suppress it in this adapter. |
| Unknown top-level config/model override or metadata capability | Reject unknown fields before schema stripping; report supported subset rather than full ACP compatibility. |
| session/load, session/resume, session/fork with same admitted session and scope | Positive controls using a real persisted fixture thread; reject each method's scope/MCP variants. An unmaterialized-thread fallback is a distinct observation. Source shows resume can fallback to native thread/read (**35187–35224**), so inspect trace before claiming successful native resume/config reapplication. |
| Unknown/foreign session ID on prompt/open, provider/auth mutation, hooks trust or unadmitted config/goal/steering extension | Reject absent an independently bound permitted context. Cancel/close can retain safe owner-bound handling; source-only availability does not prove cancellation semantics. |

JSON duplicate object keys require attention before the ordinary decoder collapses them: a gate that starts from a parsed dictionary cannot retrospectively prove no duplicate key occurred on the wire. If the fixture parses raw JSON itself, detect duplicate member names recursively and reject, including envelope/params/nested metadata. This is a proposed parser control, not observed installed adapter behavior in this audit.

## Return limits

The upstream gate can attest what exact bytes/fields it received, which candidate rules it applied, and what it forwarded to this adapter identity. Combined actual traces can show that accepted public input reached the native bridge and independent executor, while named rejected input stopped upstream. It cannot alone attest all native startup/effective policy sources, race-free whole-context generation, internally synthesized recovery requests, every tool path, actual host/MDM policy provenance, real credentials, role/skill revision drift or complete three-provider confinement. Keep those limits in #18; do not close it from raw-ingress checks alone.
