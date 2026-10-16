# #14 / #67 — Execution scope, candidate và target publication

**Status: technical planning decision.** Chọn protected exact-byte candidate + independent checks + fenced owner publication trên host hiện tại; sửa universal physical-stop predicate của các technical contracts cũ. Human không bị yêu cầu nới một requirement chưa từng đặt. Không claim full native/private/product qualification từ quyết định hoặc experiments này. ADR-0004 giữ rationale; glossary không chứa cơ chế triển khai.

## Quy tắc được chọn

Một **execution scope** có actual worker/native/effect identities, mutable roots/descriptors/rights và reservations riêng. Một **candidate artifact** có exact path/mode/blob manifest, project/source/operation/attempt lineage và immutable content identity trong owner-protected store. **Publication target** là official entity như Git ref/PR/tracker/environment hoặc artifact namespace. Chúng không đồng nhất với Story, process group hay native conversation.

Giữ custody/reservation theo actual effect target và dependency, không mặc định khóa mọi artifact của Story chỉ vì một PID cũ còn sống. Cũng không dùng tên thư mục mới để che việc retry cùng external mutation uncertain. Root/parent/native turn exit và actual stopped scope là facts riêng; không xóa fact hoặc nhả raw grants bằng cách đổi tên generation.

| Loại hành động                                      | Điều kiện tiếp tục                                                                                                                                                                                                                           |
| --------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Nhập candidate từ old workspace                     | Scoped reader/importer chụp exact allowed regular-file bytes/path/mode vào protected objects; pin provenance/source và current authority. Không hứa atomic/final live tree; incomplete/read error là error, không accepted snapshot giả.     |
| Check/review candidate                              | Verifier đọc đúng pinned source; write targets/tmp/cache chỉ ở verification scope riêng. Returned edits là candidate mới. Check pass cần actual applicable criterion/producer/result/evidence, không chỉ exit0 hoặc native completed.        |
| Công bố candidate đã kiểm                           | Trusted publisher có intent/rights, exact candidate/check/source/criteria tuple; official Git/index/objects/refs và gateway chỉ owner ghi. CAS/read-back/approval giữ #10/#12. Không lấy latest mutable worktree làm input sau verification. |
| Scope mới độc lập                                   | Không dùng roots/raw FDs/native session/grants của scope cũ; không cùng reserved external entity/operation hoặc phụ thuộc result chưa rõ. Old reservation và cleanup/resource debt vẫn hiển thị.                                             |
| Retry mutation hoặc reuse/delete scope cũ           | Vẫn cần operation-specific reconciliation/terminal proof theo #10/#20/ADR0002. New attempt/op ID/directory không mở khóa một PR/push/deploy/file target uncertain.                                                                           |
| Claim command/engine đã dừng hoặc revoke raw rights | Vẫn cần actual supported custody/terminal evidence. Seal artifact, ACK transport, timeout, PID absence và graph polling không thay điều kiện này.                                                                                            |

Ví dụ: command G1 tiếp tục ghi `G1/worktree/source.rs`. Owner đã capture candidate C1 và checker kiểm đúng C1 trong scope V1. Nếu G1 không thể ghi C1/V1/official ref hoặc gọi gateway cũ, publisher có thể tích hợp C1; không ghi “G1 đã dừng” hoặc áp old check cho bytes khác. Nếu G1 đã gửi push lên ref R mà kết quả chưa rõ, R vẫn reserved: không tạo operation push mới chỉ vì có C1 hay G2. Read-back của R vẫn theo #10.

## Import và kiểm chứng cụ thể

1. Import là typed operation qua file owner có read floor đúng nguồn/assignment. Resolve directory/object identities ở trusted code, reject traversal/special files/unsafe links; đọc qua pinned handles với no-follow/path checks thích hợp, không để importer có ambient read toàn HOME rồi tin path đã normalize. Không chuyển worker-supplied FD thành trusted source/candidate handle.
2. Owner ghi staging object mới ở protected root, tính digest trên bytes thực đã ghi và chờ finalized object/manifest record trong Zed-backed Crew store. Không hardlink worker-writable inode, không cho worker ghi object/index/manifest hoặc giữ writable handle tới đó. Không công bố partial staging.
3. Manifest gồm allowed path, kind/mode, content ref và lineage; `.git`, auth, control/runtime state không được nhập như ordinary source. Native/worktree config proposed edits dùng control-source publication operation riêng. Candidate symlinks chỉ được giữ khi policy explicitly cho phép và không mở đường đọc/ghi ngoài scope; otherwise báo unsupported trước adopt.
4. Adoption kiểm current authority/criteria/source applicability. Đây là operation nhận một artifact mới, không biến old uncertain mutation thành succeeded. Candidate có thể bị reject; raw/facts cũ vẫn giữ đúng identity và policy #7.
5. Check Adapter khai báo compiler/interpreter/formatter/harness/config, read-only inputs, writable outputs/cache/tmp, destinations và criterion evidence schema. Nếu workload cần generated source, adapter đặt nó trong declared output tree hoặc tạo candidate mới rồi kiểm lại; không âm thầm mở write toàn source root để check pass.
6. Test ghi fixture data cần scope/paths rõ trong adapter; nếu không phân biệt được source với write targets, báo capability/setup error và điều tra mapping. Không đánh dấu unsupported là green hoặc bỏ check khỏi Story Ready/DoD. Toolchain-specific maps là project configuration được validate, không một universal claim mọi build tool đều chạy read-only.
7. Publisher dựng Git blob/tree/commit từ finalized manifest, chỉ nhận actual checked candidate; Git object/alternate/index/ref/config roots ngoài worker write ceiling. Worker vẫn làm trên worktree riêng theo D3; Crew tích hợp owner-created commits của candidates vào Story integration branch theo D16. Không merge unchecked mutable worker ref.

Root command returncode, source immutability và meaningful check outcome là ba phép kiểm khác nhau. Một harness bàn giao assertion cho daemon hoặc thiếu final output chưa đủ actual criterion; streams còn mở/unknown phải được giữ trạng thái đúng. Không tự gọi mọi old descendant là harmless chỉ vì đã seal file; chỉ các effect routes đã được isolation/fence mới dùng rule này.

## Full effect mapping ở quyết định này

Kế thừa host/native mapping trong `host-publication-operations.md` và complete inventory của `common-effect-contract.md`, với phạm vi sửa rõ:

- Native/ACP buffered file và command effects đi owner-backed typed ports; mutable roots riêng attempt/scope. Stream/FD adapters giữ descriptor identity/lifetime, không hand off writable handles tới protected/current state.
- Model/provider/auth/HTTP/OAuth, GitHub/tracker, official Git refs và host formatter/LSP đều qua trusted owner/port có current-authority/generation checks; worker floor không có đường đi thẳng thay port. Native builtin/hook/subagent/autostart MCP không có mapping bị disabled/rejected, không “được phép vì prompt”. Required capability thiếu mapping là delivery blocker, không feature cut.
- Native state/bootstrap/cache/compaction là trusted integration activity với own roots/profile and supported source loader; không coi chúng thành sandboxed scratch-only code. Unknown native source/applicability chặn affected profile. Model request title/summary/compaction cũng theo bound profile/destinations như instruction lifecycle decision.
- Floor bao gồm actual object/path aliases, symlink/hardlink, clean env/FD, Unix/Mach/network endpoints và shared services. Không dùng cwd/TMPDIR/name/hash làm enforcement. Linked worktree shared `.git` không mặc định disjoint; official metadata phải owner-only.
- Current policy publication vẫn đóng admission generation cũ, chặn old gateway và giữ affected grants/reservations. Chỉ artifact publication tới protected disjoint target được tách khỏi physical whole-tree closure. Nếu quyền cũ còn đọc/ghi current/protected target, giữ blocked; không dùng candidate rule để tránh native mandatory policy/revocation.

Unknown/missing supported source là explicit preflight failure, không proof absence. Qualification của actual configured tuple phải kiểm các mapped enabled paths trước delivery. Các interfaces/algorithms/owner đã chọn ở đây và linked design cho path cụ thể; runtime floors, private profile settings và production integration chưa được chạy hoặc chứng nhận. Nếu implementation/source discovery lộ exact interface không có thì reopen precise feasibility decision, không fallback rộng hơn.

## Quarantine, tài nguyên, dữ liệu và OUTPUT

Old scope còn possible writer giữ own reservation; control publication không tự xóa nó, reuse old context hoặc nhả raw rights. Scope mới khác entity chỉ tiếp tục khi dependency/authority và capacity còn đủ. Uncertain scope vẫn tính trong pool/resource reservations; không tạo vô hạn new generations để vượt high-water. Saturation chặn admission mới, giữ cancel/reconcile/diagnostic capacity. Không hứa soft RSS target là OS hard limit hoặc mọi fork đều bị reclaim tự động.

Retention/export/delete giữ #7: minimal recovery/actual decisions không bị raw expiry xóa nhầm; raw copies/store owners theo inventory, partial failure báo thật. Quarantine không tự tạo Human investigation hold hoặc âm thầm đổi 30 ngày. Nếu actual raw expiry/delete/resource budget không đáp ứng do surviving rights thì đó là failure/blocked của requirement liên quan, không claim complete và không xóa evidence cần thiết chỉ để tiếp tục. Không hứa local secure erasure hoặc thu hồi memory đã đọc.

Session OUTPUT vẫn qua verifier theo original US requirements; old execution state/cleanup debt không bị rewrite. Nếu DoD/criterion đòi resource cleanup hoặc no outstanding affected effects mà chưa đủ evidence thì OUTPUT không achieved. Nếu criterion chỉ yêu cầu protected checked artifact/PR đã merged thì không tự thêm universal stopped-all-processes gate. New idea sau US đã hoàn thành vẫn là US mới.

## Evidence thực tế mới

- Delegated research trong `artifacts/research/wayfinder-macos-process-custody.md` audit Apple/XNU và local SDK/runtime. Coordinator kiểm lại7 SDK source hashes cùng OS/toolchain metadata, chỉ lookup library symbols, không tạo ES client. Host26.6.2 không có `es_new_descendants_client`; API27/entitlement và VM chỉ là conditional alternatives, không prerequisites mới.
- `isolated-publication-probe.py`:11 observations pass. Actual Seatbelt fixed writer còn sống, đổi old source; candidate trực tiếp/via symlink, owner Git ref/ledger và new workspace bị actual EPERM; exact trusted Git content/ref không đổi. Không native/daemon-wide qualification, atomic importer hoặc private policy proof.
- `readonly-check-probe.py`:5 observations pass. Actual Cargo/Rust1.98.1 chạy offline/frozen một std-only crate, build script tạo generated Rust trong private target; actual test `reads_generated_output` chạy và pass. Source write trong build script phải bị EPERM để build script thành công;4 input files giữ nguyên hashes. Đây là một supported out-of-tree mapping đã đo, không build Zed hoặc universal toolchain pass.
- Giữ failure sources/results: thiếu explicit read OpenSSL system config gây startup failure; restricted initial sysctl set gây Rust runtime guard-page abort. Broad read-only sysctl diagnostic pass được giữ; final policy chỉ thêm read-only `hw.*` và literal OpenSSL config cùng pinned Rust/Xcode source/toolchain read roots. Không kết luận đã tìm exact single missing sysctl hoặc policy này là minimal production profile. Không đổi real system settings, toolchains, auth, network hoặc source Zed để làm pass.

## Canonical contract amendments cần link

Research #67 có thể complete ở câu hỏi supported mechanism/options/limits. #14 nhận lựa chọn kỹ thuật và mappings ở đây. Append pointer vào #10/#11/#18/#20 và map: **physical terminal của actual effect scope ≠ bất biến/applicability của artifact**. Giữ closed evidence cũ bất biến; sửa interpretation quá rộng, không giả các probes cũ đã đo protected-publication composition.

Các lựa chọn Human/ADR1–3 không bị thay; không thêm approval gate hoặc gán Human đã duyệt ADR4/default SOUL/budgets. Đây là engineering decision dùng primary sources và bounded actual experiments. Planning có path cơ chế; production source code/native integration/GPUI/Data/private/quality/compatibility qualification vẫn là việc build và kiểm chứng sau spec.
