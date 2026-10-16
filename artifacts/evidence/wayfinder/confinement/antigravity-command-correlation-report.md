# #47: nonce-bound command receipts on actual model-facing and ACP-client surfaces

Status: **bounded prototype complete**. A concrete cross-surface identity/result route works through unchanged installed ACP. Confinement #18 and the destination map remain awaiting decisions; no production implementation or approval.

## Result and route

The wire control establishes that native preserves the provided public `functionCall.id` in its actual trajectory and functionResponse. ACP uses another toolCallId. The candidate gateway issues a fresh opaque nonce as that model/native call ID and carries the same nonce in a shell-comment suffix of the delivered single-line CommandLine. The real ACP permission rawInput echoes it. An owned registry binds nonce, original/delivered input, actual ACP session/call/permission, observed native PID and fixture owner epoch before helper spawn. The mapping does not use fixture case, response order or command text equality as identity.

The admitted fork-free pipe helper executes independently under the existing narrower OS floor and is directly reaped. A result proposal is validated from its real receipt. A real gateway HTTP proxy validates the native functionCall/functionResponse IDs and tagged input, then forwards that result to a **separate HTTP model fixture**. Only after the fixture returns an actual HTTP response does the facade publish the corresponding terminal packet to the ACP client. Model functionResponse output and client content/status match. The HTTP response is bounded handoff evidence, not a distributed transaction, durable storage or client delivery acknowledgment.

The engine still rejects its own command path and stores the original denial internally. The gateway repairs the model-facing HTTP request; it does not patch native trajectory storage. Compaction, internal subagent handling and other consumers of the original internal denial remain outside this result. Real provider/model acceptance and comprehension are untested; both HTTP endpoints and inference are synthetic/public.

## Identity evidence and controls

- Repeated commands have exactly identical original arguments but different issued nonces and ACP call IDs, separate actual effects/PIDs/receipts. Two calls in one model response likewise map independently. Their results overlap while unresolved; helper execution is serialized, not a parallel-helper proof. Final c6 reverses those result positions before validation/forwarding and still pairs the correct payloads by IDs.
- The native request `sessionId` field repeats across different actual actor/ACP instances. It is checked as a payload scope field, **not treated as unique native conversation identity**. c5/c6 copy an actual completed c4 nonce into a fresh actor and reject it before helper spawn despite the repeated field. Current ACP session and owner registry/epoch are separate scope inputs; epoch is fixture-owned, not durable supervisor identity or native attestation.
- Missing/unknown/ambiguous/current-scope mismatch/stale bound nonces reject before effect. Actual client reject, missing policy, genuine permission-wait source mutation and profile drift still reject before helper spawn, and restored positives work. A rejection with no admitted helper retains actual native rejection; no owned model receipt is fabricated.
- Outside reads and fork are actual OS EPERM. Positive helper controls retain engine/outside denial, closed parent dummy FD200, clean dummy environment and unrelated endpoint failure. Profiles remain fork-free/no-network and the engine has no PTY grant.
- Wrong-call/wrong-session/missing/replayed receipt faults follow real effects and direct reap; they publish the same explicit failed receipt to both surfaces, not completion. Model missing-call/wrong-call/wrong-scope/duplicate-response faults are rejected before upstream forwarding after the effect occurred. Client then reports post-effect model-publication failure. Restored later history receives the frozen failed envelope; no rollback or false completed repair.
- A replay of an actual native terminal is deliberately injected locally into the facade in c6 and suppressed. This is a controlled replay test, **not evidence that the vendor emitted duplicate terminals**. Repeated immutable result serialization in model history is allowed without another helper execution or client publication; it is not a fresh effect/receipt admission.

## Executed matrix and preserved failures

| Run | Evidence / status |
| --- | --- |
| w1 | Two actual installed ACP commands; supplied functionCall.id preserved, actual native session field and denied results projected |
| c1 / p1 | Wrong factory source transformation: SyntaxError before ACP initialization, retained |
| c2 / p2 | Wrong derivation seam: ValueError before ACP initialization, retained |
| c3 / p3 | Actual nonce binding and one helper effect/reap; missing json in generated publisher causes no terminal/proposal, gateway history remains rejected. `end_turn`/failure:null is not a successful prototype |
| c4 / p4 | First complete23-prompt matrix: repeat/overlap identities, admitted effects, publication and negative controls |
| c5 / p5 | Fresh complete24-prompt matrix; adds actual prior-owner nonce rejection despite same native session field |
| c6 / final | Complete26-prompt matrix; reversed pair results, duplicate model response rejection and actual terminal replay suppression |

`antigravity-command-correlation-verify.py` checks **621 expected observations**, including failures/counterexamples; these are not security requirements passed. Three successful matrices have **73 prompts,146 native model requests,136 actual upstream HTTP requests,47 helper direct reaps,76 actual delivered client terminal packets,19 completed shared receipts and10 after-effect model binding/publication fences**. All47 observed helper PIDs are absent after direct wait/cleanup; adapter/native absence is recorded. Native receipts for completed pairs remain denial internally; forwarded model payloads are separately observed at the model fixture.

Worker fsynced JSONL plus independent file readbacks establish real effects. Actual projected client/facade/native/upstream packets and acknowledgment events establish identity and order. Sources/snapshots/generated controllers/profiles/mutations/results/readbacks are exact archived evidence; raw vendor prompts/catalogue and binary payloads are excluded. The original permission/gateway/facade/worker sources and installed adapter/backend are unchanged. Result source ancestry is distinct from wrapperPins/new candidate revision; the verifier checks actual revision pins.

## Limits and next route

Scope is one owned ACP session per gateway actor, registered sequential or two-call batches, valid single-line `exec` fixture commands and no-fork pipe semantics. The shell-comment carrier is a measured provisional interface choice, not acceptance of general shell rewriting or production UI details. No PTY/daemon/async/subprocess-capable build semantics, authenticated/private six-source effective policy, atomic source publication, loaded-image attestation, durable model/client transaction, cancellation or full native conformance proven.

The new controller owns the command helper, correlation registry, gateway and publisher. It has **not** been integrated with the surviving #44/#45 supervisor/guardian, and no controller-death/lease-reclaim proof is inherited merely by importing their prior conclusions. Resolve this bounded identity/result prerequisite and return assigned #18 to **actual command controller loss and supervisor/guardian integration** before any lifetime claim. Measure a real effect/input/receipt barrier and actual symptom first; graduate another prerequisite only from precise new evidence. Keep #20 active cancel/resume distinct. Map remains draft; spec/build graduation requires the other blocking decisions and fog to be resolved.

No real model/auth/credentials/private targets/account/profile/default-model/billing/MDM/PAT changes, proprietary extraction/patch/replacement, production integration, OS service selection, external communication or fabricated specialist acceptance. Human A #29/#35 and ADR-0002 remain settled.

Canonical sources: [#47](https://github.com/Riso-application-factory/zed-ai-crew/issues/47), [preceding actual seam](https://github.com/Riso-application-factory/zed-ai-crew/issues/18#issuecomment-6161547592), [#43 client receipt](https://github.com/Riso-application-factory/zed-ai-crew/issues/43#issuecomment-6152481687), [#44 surviving helper parent](https://github.com/Riso-application-factory/zed-ai-crew/issues/44#issuecomment-6154015658), [#45 registered guardian](https://github.com/Riso-application-factory/zed-ai-crew/issues/45#issuecomment-6160790140), [#46 rejected automatic tracking/config candidate](https://github.com/Riso-application-factory/zed-ai-crew/issues/46#issuecomment-6161283032).
