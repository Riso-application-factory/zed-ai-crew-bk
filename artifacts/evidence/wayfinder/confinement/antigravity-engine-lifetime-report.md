# Antigravity whole-engine lifetime versus helper quiescence

Status: **existing #18 bounded investigation verified; whole-engine ownership/recovery unresolved**. Map #1 remains OPEN/draft. This does not reopen #44's narrower surviving-helper-parent result.

## Actual finding

The unchanged public #44 actors prove helper generation quiescence but leave the installed ACP/native engine outside that contract when the owner dies. In both actual before-input and after-input owner SIGKILL runs, the owner is directly waited (-9), the metadata/effect supervisor directly reaps its old children, and a new owner is admitted. The **old ACP adapter is still an orphan with PPID=1; its old native backend remains its child in the old process group and still has a live TCP listener**. Missing-key HTTP GET on that recorded native listener returns401. The new owner creates a fresh actual ACP session/native backend while the old backend is still present.

Old and new native PIDs are distinct and both are seen in their actual recorded adapter process groups at the fresh-session snapshot. Later fresh-owner permission/MCP observations bind the same new native PID seen in that group. Some fresh native/relay IDs had not yet been registered in the owner journal at the snapshot; the raw group entries are joined to the later actual journal, not invented as pre-existing owner bindings. This is ps/parent/group observation and installed-file fingerprinting, not running-image attestation or a direct native wait handle.

The old engine remains observed alive until the outside fixture cleanup: at least **12.507986375 seconds after direct owner wait** in before-input `b1`, and **11.116349417 seconds** in after-input `a1`. These are observed lower bounds, not exit deadlines or a universal daemon-lifetime claim. Normal `n1` finishes the real ACP public calls and the live owner does its group termination/direct adapter wait: recorded adapter/native/relay PIDs and group are absent afterward. In `s1`, actual supervisor SIGKILL plus owner terminal70 also leaves the adapter/native engine live; the dead-channel replacement remains denied as in #44.

**401 is the expected missing-key authorization denial.** It proves a live service is still answering, not an authentication bypass or continued post-loss native tool execution. No native API key was extracted/supplied. We did not dispatch a post-loss tool or infer private/file/network effect authority from PID/endpoint liveness. The listener's native PID and address were read using lsof for the owned fixture process only.

## Executed matrix

| Run | Measured control / result |
|---|---|
| n1 / normal | Real installed ACP resource positive and slow public effect positive; actual owner completion; known engine process group absent after normal owner cleanup. |
| b1 / owner before input | Actual before-input barrier, owner SIGKILL/direct wait, early replacement refused while old helper live, old metadata/effect direct reap/quiescence. Old ACP/native and listener survive; new actual native session overlaps old engine; fresh15-case ACP sequence completes. |
| a1 / owner after input | Worker-written actual input acknowledgment before owner SIGKILL; same helper direct-reap/replacement gate remains valid. Old ACP/native survives helper quiescence and overlaps fresh engine; fresh15-case sequence completes. |
| s1 / supervisor loss | Actual supervisor SIGKILL/direct wait; owner70 and dead-channel owner70. Old ACP/native remains live after both controller processes are gone; helper orphan and engine cleanup are outside-observer fixture actions. |

Verifier: **4 runs /126 expected observations /56 recorded PID-absence checks /36 actual helper direct reaps /18 missing-key HTTP401 observations /35 actual client ACP terminal packets**. These expectations include the lifetime counterexample and auth-denial controls; they are not126 complete product/security requirements. Final observed owned fixture PIDs/groups are absent after cleanup. Orphan PID disappearance and process-group signals do not establish authoritative native wait/reap by a surviving lifecycle owner.

## Probe change and preserved sources

Only a new outside observer was added. `antigravity-engine-lifetime-probe.py` loads immutable `antigravity-mcp-survivor-probe.py`, derives its observer function using exact-once source seams, and adds snapshots at live owner, direct owner wait, early replacement refusal, helper quiescence, fresh session, fresh owner wait, and before/after fixture cleanup. It launches the **unchanged #44 supervisor/controller programs**. Source for the derived observer is saved; actor/source/profile and installed pins are checked. No new production supervisor, provider/config/auth change or proprietary artifact patch occurs.

Snapshots retain ps PID/PPID/PGID/state/command, actual known adapter/native/relay IDs, full recorded adapter-group membership, lsof listener output, and exact HTTP response headers with `keySupplied=false`. Observer HTTP probes are external trusted fixture observations; they are not admitted worker requests or gateway-restriction proof. Native built-ins/command/MCP effect capabilities after loss are not probed in this increment.

Installed ACP1.3.0 adapter278535456 bytes SHA-256 `cb1f0725183ed922079ed6cd1d2422b4d8be44c5fb60be3e9a43d8d04f99a18e`; native backend118611392 bytes SHA-256 `b04b00662c1821b953a409d43626b86d1229130eeb2078b4e1304c06199aa481`, unchanged. Real installed ACP/native plus synthetic local Gemini SSE only; diagnostic engine wildcard loopback remains outside the admitted worker floor. No real provider inference/credentials/private data, actual account/profile/default-model/provider/billing/PAT/MDM changes, vendor extraction/replacement or production integration/commit/PR/release.

## Precise decision frontier

The now-measured missing prerequisite is a **whole-engine launch/lifetime ownership contract**: a surviving fixed launcher/guardian must account for the actual installed ACP adapter and its native backend/relay, as well as metadata/effect children, and replacement admission must not treat helper-only quiescence as whole-engine quiescence. A supported bounded contract needs positive actual ACP operation, direct/authoritative lifecycle evidence, before/after-input owner loss, early replacement denial until prior engine reclamation, fresh current-binding positive and guardian-loss limits. If the unchanged installed artifact cannot expose the necessary child ownership/termination acknowledgment, report the exact supported-interface gap and concrete options without silently selecting a production frontend or patching the proprietary executable.

That question can now become a typed prototype ticket, blocked by the already-closed #44 and blocking #18. Do not work that new ticket in the same session; this increment investigates existing #18 and hands over the newly precise frontier. Keep #20's active cancel/resume separate. Whole-engine guardian restart/orphan reclamation, same-target state recovery, effective native config/identity, fixed installed endpoint, generic/background/HTTP/OAuth/all-effects/private/atomic/provenance/production maintenance remain open; do not turn this401 observation into those proofs.

Sources: exact new observer/derived observer/profiles/results/verifier archive; [#44 canonical bounded resolution](https://github.com/Riso-application-factory/zed-ai-crew/issues/44#issuecomment-6154015658); [prior #18 single-owner effect lifetime](https://github.com/Riso-application-factory/zed-ai-crew/issues/18#issuecomment-6153119331); [#43 resource receipt](https://github.com/Riso-application-factory/zed-ai-crew/issues/43#issuecomment-6152481687). Human choices #29/#35 and ADR-0002 remain settled within their scopes; no new public-prototype Human gate or production topology agreement is inferred.
