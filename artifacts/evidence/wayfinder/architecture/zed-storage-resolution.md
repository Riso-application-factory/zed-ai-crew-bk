# #14 — Storage foundation resolved: reuse Zed

**Status: actual Human direction confirmed; source-inspected technical mapping; implementation/qualification not executed.** #14 remains open for its other mechanisms. Human's answer to the proposed SQLite-vs-journal question was: “hãy sử dụng nền tảng của zed, zed đang lưu trữ thế nào thì giờ sử dụng cái đó”. This selects reuse of Zed persistence; do not record it merely as approval of the earlier private-store proposal, or ask the same A/B question again.

## Existing Zed behavior observed

| Area | Source finding | Crew use / implication |
| --- | --- | --- |
| App state | `AppDatabase` shares `ThreadSafeConnection`; inventory-registered `DomainMigration` entries, `static_connection!` wrappers; `db_path(database_dir(), release_channel)` | Crew domain/tables and versioned records follow this pattern; same platform/path/channel resolution |
| SQL primitives | `sqlez` wraps SQLite; queued writes run on a worker per URI in the process, reads are thread-local; savepoints release/rollback on Result | Await transactional ledger writes; do not block GPUI foreground or invent a new storage engine |
| Metadata | Sidebar `ThreadMetadataDb` implements Domain and uses shared AppDatabase | Concrete nearby integration example for Crew run metadata, not proof that chat history is a ledger |
| Native thread history | `ThreadsDatabase` uses `data_dir()/threads/threads.db`; JSON payloads compressed with zstd | Zed uses more than a single DB; do not merge Crew truth into a native thread blob or promise all provider/native history is Crew-controlled |
| Normal mode | App DB initialization sets WAL and synchronous=NORMAL | Preserve platform defaults; no power-loss claim or silent global tuning from earlier independent SQLite probes |
| Open failure | App-level fallback and low-level `Connection::open_file` can use in-memory storage; actual `Connection::persistent()` is public | Critical Crew writer verifies actual persistence; fallback/stateless is a visible blocked storage condition for effects, not a new empty run |
| Fire-and-forget | `db::write_and_log` background-spawns, logs errors and detaches | Suitable patterns for some platform UI preferences are not sufficient to acknowledge a critical intent/receipt commit |
| Migrations | Domain SQL/text history, savepoints and change detection exist; no general future-version rejection proved by inspected loop | Crew still needs explicit schema/codec/applicability compatibility checks before writes/effects; reuse is not a downgrade guarantee |

## Concrete integration decision

The in-Zed controller is the Crew ledger writer and uses a Crew domain wrapper backed by the app's existing connection. In one awaited queued transaction/savepoint, validate store/versions and record intent/reservation before outbound dispatch; after the effect, record receipt and evaluate OUTPUT against the original US binding. No network/Git/native effect occurs inside a SQL transaction. SQL/commit failure propagates to Crew state/UI; cancellation of a waiting UI task is not proof a queued write or external effect did not occur.

Effect owners retain their operation-specific custody/readback responsibilities but do not gain shared AppDatabase access from a worker or arbitrary model-supplied path. If Zed dies before receiving/persisting a receipt, the committed intent remains uncertain. Recover against actual PR/ref/file/process facts using #10/#20; missing receipt is not permission to replay, nor evidence of a completed OUTPUT. Source/generation/owner-channel publication and safe reconnect remain actual unresolved mechanisms in #14; this storage decision does not certify them.

## Correction to historical source narration

The comment above `db::open_db` describes retries and moving the DB directory to backup, but the inspected function body does not implement that rename/retry sequence: it tries the file path then sets a failure flag and uses memory. No rename/archive behavior is claimed from that comment. Low-level open-file fallback can also happen without the App-level failure flag, so persistence must be checked at the actual connection. These are source findings, not newly executed corruption probes.

## Validation and remaining frontier

Read and fingerprint11 relevant source files at unchanged HEAD, including exact excerpts and the mismatch between comments/body. No real DB opened, no migration/settings/code modified, no new runtime test or power-loss measurement. Earlier standalone Python SQLite probe results retain their original scope and do not verify Zed AppDatabase integration.

[ADR-0003](../../../../docs/adr/0003-reuse-zed-persistence.md) records confirmed direction, alternatives and coupling consequences; exact copy accompanies this ticket evidence. The old storage A/B proposal is historical and superseded for the selection. Engine-in-Zed B and all earlier data/quality/US completion choices remain.

Next independent technical work in #14: map controller/effect-owner channel/custody/reconnect and schema/upgrade admission to this shared platform, then native profile/policy/role/skill/host publication and packaging/resource seams. Do not mark #14 closed or whole fog exhausted from a storage choice. Provider/model/MCP and private-profile admission are not selected by implication.
