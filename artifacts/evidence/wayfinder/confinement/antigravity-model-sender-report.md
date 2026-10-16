# Actual model-ingress sender counterexample

Status: verified bounded counterexample in assigned Confinement #18. A new precise transport/sender-admission research prerequisite is warranted. The #51 fixed-incarnation answer remains valid within its stated scope; it did not authenticate the HTTP sender.

## Measured result

Two actual runs, s1/s2, each launched four separate Python actor processes with clean PATH/HOME/TMPDIR/LANG, closed inherited descriptors and no passed private channel. Each actor connected to the candidate's public loopback model gateway, paused for independent process/socket observation, then sent one minimal public Gemini-shaped text request. The actor was neither the admitted native nor its adapter nor the supervisor; it had a separate process group. Its own UID was recorded by the fixed actor program. This is an unconfined local fixture process, not a demonstrated private-worker-floor escape.

| Actor request | Actual gateway response in both runs | Actual synthetic upstream |
| --- | --- | --- |
| Matching initially observed service/session field | HTTP200 | Exact public marker/body forwarded |
| `CREW_PUBLIC_FOREIGN_SERVICE` field | HTTP200 | Exact public marker/body forwarded |
| No service/session field | HTTP200 | Exact public marker/body forwarded |
| After actual malformed-history poisoning | HTTP409 | Marker absent; zero forward |

The accepted requests passed the existing `model-input`, `model-handoff` and `model-call-issue` graph checks against the real registered native/adapter binding. Those checks observed the correct live processes while the actual accepted TCP connection belonged to a different process. Thus a correct current graph and caller-provided service field are insufficient evidence of the sender in this candidate. No function call or helper effect was requested by these actor bodies; this experiment demonstrates model-input forwarding, not an injected command or receipt ACK.

The model HTTP body's `sessionId` is a native service field. It is distinct from the original ACP conversation's `sessionId`, which remains bound in the admitted owner and historical records. Acceptance of a matching model-body field is not authentication of that ACP conversation or its producer.

## Independent identity and delivery evidence

The supervisor retained each actual actor `Popen` handle and directly waited for its exit. The fixed actor program reported PID/parent/process group and its connected socket's local/remote endpoints. While the actor was paused before POST, `/bin/ps` independently confirmed PID/parent/process group, and `/usr/sbin/lsof` showed that PID owning the exact ESTABLISHED TCP endpoint. The gateway's actual accepted socket peer/local endpoints matched the actor's inverse tuple. A public marker header joins these observations; it is a correlation label, not an authentication credential. No Authorization header was supplied.

The upstream receiver captured exact body byte count/SHA-256 and only the public text marker projection. These matched the actor's actual sent body. Actual actor HTTP200/SSE responses and the upstream journal corroborate acceptance; a graph-only or status-only simulation was not substituted. Four independent PIDs per run distinguish the sender controls. The service field for the matching case was captured before any actor modified the receiver's retained input; subsequent `serviceField` diagnostic values can reflect the most recently accepted input and are not claimed as an independently attested native identity. Foreign and missing controls are asserted directly from their actual sent bodies.

The negative control follows a real native-driven malformed historical witness rejection. It poisons the current epoch under #51. Repairing that public record does not reopen it: the separate actor still receives HTTP409 and its marker never reaches upstream. A fresh quiescent epoch restores the exact original ACP session and executes two legitimate current public commands around a genuine retired-nonce permission denial. #50's completed model ACK versus interrupted failed client fact remains preserved; no historical helper/grant revival occurs.

## Validation and scope

Verifier passes 180 current-run expectations and 1,070 exact pin checks of prior archives (1,250 total). Two runs contain eight actual actor requests: six accepted, two poisoned-epoch denials. Actual lifetime/control evidence includes six owned helper direct reaps, six adapter direct reaps, 12 registered kernel exit events and 28 upstream HTTP requests including native-driven controls. All three registered generations per run reach quiescence before replacement, lower-level drivers complete, actor handles are directly reaped, and no observer/emergency cleanup of leaked fixture actors is required. Previous sources/results/archives remain exact.

Installed Antigravity ACP/native remain unchanged and fingerprinted. All payloads, settings, effects and model responses are public/synthetic; no real inference, provider credentials/private data, actual profile/account/model/MDM/PAT/billing change, binary extraction/patch/replacement, vendor communication, production integration, commit or PR.

Kernel/process/socket correlation here establishes the controlled fixture's observed sender. It is not a chosen production peer-authentication mechanism, universal race-free process attribution or proof that a private worker can reach this endpoint. No bearer token, secret header, IPC topology, privileged service or SDK replacement is silently selected. The result warrants researching a supported sender/admission seam and its boundary assumptions; it does not imply every possible confined configuration is infeasible.

## Next decision frontier

Precisely research which version-pinned supported seam can bind actual Antigravity model ingress to the admitted producer/incarnation before forwarding data, rejecting unrelated/replayed/misbound senders. Separate installed ACP1.3.0/native evidence from public SDK/source capabilities and caller-declared service/session values. Identify supported callback/transport/launch/auth context or OS isolation alternatives with primary sources, limitations and a bounded validation plan; do not infer capability absence from an undocumented field or apply Codex-specific fork authorization to Antigravity. If the current sender claim instead relies on reachability/isolation, establish that required contract and evidence rather than treating the unconfined fixture as a private-worker escape.

The research ticket is a planning prerequisite, not a build ticket. Its eventual bounded answer returns to #18. Private full-policy/provenance, preparation/atomic/arbitrary-tree, broader native/helperless/MCP/client effects, compaction/subagents, durable whole-supervisor trust/reclamation, real-provider comprehension and production topology/maintenance remain fog. Existing #20 owns active cancellation/resume. No extra Human permission is required for public primary-source discovery; any real product/authority choice requires its actual answer when reached.

Canonical entry: [#51 fixed incarnation](https://github.com/Riso-application-factory/zed-ai-crew/issues/51#issuecomment-6166718735), [#50 distinct history](https://github.com/Riso-application-factory/zed-ai-crew/issues/50#issuecomment-6166186756). Primary files are indexed by `antigravity-model-sender-artifact-index.json`; `antigravity-model-sender-verify.py` checks them without rerunning probes. Exact archive and canonical continuation links follow separately.
