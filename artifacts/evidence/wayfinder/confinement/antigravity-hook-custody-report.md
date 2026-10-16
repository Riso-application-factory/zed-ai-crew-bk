# #62: bounded owned hook-command custody

Status: measured bounded candidate complete; broader Confinement #18 and destination map #1 remain open. This is a decision prototype, not production implementation or full filesystem confinement.

## Answer and boundary

Unchanged installed Antigravity ACP1.3.0 can launch a fixed public owned command through its actual project `PreToolUse` / `view_file` hook. The surviving supervisor obtains the real connecting process PID from the kernel, checks its observed adapter parent/process group and exact command, registers two independent NOTE_EXIT subscriptions while that process is live, then authorizes input handling and the hook reply. The registered hook children join the existing strict ACP/native/MCP membership and terminal proof. A foreign process is still rejected; cleanup and inventory absence do not replace terminal evidence.

The registered boundary precedes this fixed handler's tool-input consumption and allow/deny reply. Python startup, source loading and preparation before its HELLO are outside that proof. This result does not answer whether every relevant preparation/native effect has an authoritative gate. Source/config policy ownership through the later native file effect remains unresolved in #18. Earlier trusted-once handler/config canary-read counterexamples remain valid.

## Executed seam

Source: `antigravity-hook-custody-probe.py`, 28,549 bytes, SHA256 `5a5034257d85b3a8df7d6ce5a04b86a8889fc309ea993e56f5740ebef2622c44`. Final-source runs c10–c14 and their executed source snapshots are byte-identical. Earlier iterations and their snapshots remain available.

Installed pair is unchanged from entry evidence:

| Artifact | SHA256 |
| --- | --- |
| `agy_acp_server.par` | `cb1f0725183ed922079ed6cd1d2422b4d8be44c5fb60be3e9a43d8d04f99a18e` |
| `localharness_external` | `b04b00662c1821b953a409d43626b86d1229130eeb2078b4e1304c06199aa481` |

The actual hook command is direct Python argv through the documented command field. Successful runs introduce no shell child. The early `exec` prefix attempt was invalid because this ACP invokes `asyncio.create_subprocess_exec`; its real FileNotFoundError is retained in c3. The installed binaries were neither extracted nor patched.

The source is outside the worker's writable worktree and scratch, granted literal read-only access. An actual component under the engine profile attempts an O_WRONLY open without truncation and receives OS EPERM. The hook socket has an exact literal grant; there is no wildcard Unix-socket grant. The project hook configuration remains writable and is compared with the fixed expected text at prepare/admission/input checks; this is not immutable policy authority.

Kernel peer PID is obtained using macOS SOL_LOCAL / LOCAL_PEERPID (0 / 2), as defined by the [primary XNU header](https://github.com/apple-oss-distributions/xnu/blob/main/bsd/sys/un.h); local SDK/platform metadata is in `antigravity-hook-custody-archive/platform-source.json`. The supervisor also observes PPID, PGID and full argv. Caller-supplied PID claims never establish enrollment. Epoch/token, one pending birth from actual model-call issuance, current source/config, and actual input conversation/path are checked. The hook input has no native call ID: correlation is explicitly sequential session/path/time, not a generic concurrent call-ID contract or a universal PID-reuse defense.

Independent terminal queues and readers exist in the surviving supervisor, separate from the original fixed adapter/native/relay watches. Real queue events must match the registered PID/tag/filter/flags. Retirement requires both hook exit acknowledgments plus the existing independent core witness and direct adapter wait. Hook NOTE_EXIT is non-child terminal observation, not direct reap.

## Final-source evidence

| Run | Actual experiment | Result |
| --- | --- | --- |
| c10 | Genuine hook peer connects; registration deliberately withheld before input; actual managed admission callback checks graph | Exact extra process rejected; unknown-member latch and terminal-proof failure retained; no enrolled hook/input/authorized result, no accepted closure. Expected unquiescent timeout. |
| c11 | Append whitespace to owned hook configuration after actual workspace trust | Current fixed source/config mismatch poisons epoch before a new state hook dispatch. Prior public read succeeds; state/restore attempts are fenced, not vendor file-denial outcomes. Fresh epoch restores public/state/MCP controls. Two accepted closures, four registered hooks/eight independent exits. |
| c12 | Normal joined ACP/MCP flow, byte-preserving config rewrite and actual native roots notifications | Two clean epochs; six registered hooks/12 independent exit events. Public → state policy denial → restored public in each epoch; actual MCP public before/after and same-path OS EPERM. |
| c13 | Actual owner SIGKILL while state hook held before input; hold independent terminal ACKs | Primary public read already completed. State hook consumes no input and gets no authorized reply/receipt. Early replacement exits3; replacement after kernel exits but before held ACK release also exits3. Two held hook ACKs released before quiescence; fresh epoch passes controls. Five hooks/10 independent exit events, two accepted closures. |
| c14 | Actual owner SIGKILL after state hook input, before reply authorization | State input observed, no authorized reply/receipt. Early replacement exits3; fresh epoch starts only after terminal proof and passes controls. Five hooks/10 independent exit events, two accepted closures. |

Each final-source enrolled hook is tested with a separate connection from that same live process: replay, stale epoch, foreign token and forged PID envelope are all denied. An actual foreign supervisor peer using the correct epoch/token is also rejected by real parent identity. c10 tests the actual unregistered child, not a fabricated PID.

During every final-source successful registration, a trusted callback actually reaches the MCP admission wait while the real hook is pending; registration then permits its full graph check to continue. This is a managed callback, not a native packet. Real native MCP roots notifications are independently recorded and admitted. c12 has nine wait records; c11/c13/c14 have five/five/seven. No arbitrary-interpreter allowlist, unknown-member reset or missing-process exclusion was added. Current inventory is refreshed after waiting and checked against the exact registered members.

Workspace-trust permission remains separate from real MCP allow-once. The declared relay keeps an empty model URL, and its same-session/call effect has a real permission and direct helper receipt. The same app-state path denied by the owned file hook is denied by the separate MCP OS floor with PermissionError/errno1. Worker exit0 merely serializes that failure and is not counted as read success.

Actual model packets are correlated by call ID and deduplicated across history. Across final-source c10–c14, 13 public-file results and five explicit policy-hook denials reach the model request unchanged. There are separately two unregistered-child fenced attempts, two source-drift fenced attempts, and four owner-loss attempts with no matched model result. ACP `end_turn` in a fenced prompt is not promoted to file success/denial. Native/client wire snapshots are retained without packet repair; this ticket makes no truthful-client-terminal parity or native-history repair claim.

## Retained iterations

All c1–c14 outputs/results/source snapshots are retained. c1's initial lock placement deadlocks before dispatch and times out, including its actual broken-pipe traceback. c2's pending-birth wait and invalid command fail; c3 exposes the real unsupported `exec` executable. None enrolls a hook or establishes closure. c4 normal success; c5 adds real peer/source-write negatives; c7/c8 validate earlier owner-loss versions. c6 fails because the fixture did not propagate the held-ACK flag into the baseline module: primary retirement actually succeeds, `ackearly` legitimately starts, then the observer incorrectly expects denial and times out. It is not evidence of bypassing a held-ACK gate. c9's clean flow does not test the intended pending wait because scheduling misses it; a real wait barrier is added before c10. No failed iteration is relabeled a successful custody test.

Across the final source: 20 enrolled hooks/40 independent exit events, eight accepted epoch closures, plus the intentionally unquiescent c10 epoch. Earlier versions have their own counts in the verification output; they are not silently aggregated as final-source evidence.

Run `python3 artifacts/evidence/wayfinder/confinement/antigravity-hook-custody-verify.py`: 397 current assertions and all 656 unchanged prior archive entry pins pass. These are recorded-evidence checks, not 397 independent runtime tests. The verifier records 261 collected fixture PIDs absent after execution as cleanup evidence only. Twenty-four recorded socket endpoints are checked after their supervisors exit; seven old fixture socket files are removed explicitly. That socket cleanup supplies no missing terminal proof. Pre-retirement snapshots explicitly carry `fullNormalClosureClaim: false`; accepted closure requires separate recorded proof. Earlier source tracebacks use a stable filename that has since changed; the exact executed source snapshots, not current line numbers, govern review.

## Return to #18

The narrow birth/input/reply/terminal integration question is answered; return the measured candidate to assigned #18. No additional prerequisite ticket is introduced by this result. The next bounded parent investigation should inspect current source/config authority during the interval after dispatch preparation/owned hook decision and before the native file effect, using real late-mutation and restoration controls. A writable project file plus trust-once or successful post hoc model fencing cannot establish pre-effect enforcement.

Remaining #18 fog includes pre-HELLO preparation, mandatory hook availability/reload, late source/config and full policy/provenance authority, arbitrary descendants, PID reuse, concurrent native calls, aliases/timeout, private and host paths, broader native effects, subagents/compaction, durable supervisor restart/reclaim, real-provider comprehension and production topology. Active cancellation/resume stays in #20. No Human configuration, credentials, provider billing/model change, installed upgrade/fork, production integration, commit or PR was performed or required. Map completion and spec synthesis remain pending other decisions and fog.
