# Hợp đồng native filesystem launcher cho quyết định #36

Trạng thái: **nghiên cứu source hoàn tất; kiểm chứng runtime tích hợp thuộc Coordinator, chưa được thực hiện bởi researcher này**. Chỉ tạo báo cáo này; không sửa native source, policy, tracker, không chạy helper hay thay đổi dữ liệu/credential thật. Human đã chọn A ở [#35](https://github.com/Riso-application-factory/zed-ai-crew/issues/35#issuecomment-6140879471): launcher giới hạn độc lập, worker vẫn cách ly, quyền helper nằm trong giao hai trần, lease giữ đến hoàn tất và reap. Báo cáo không mở lại lựa chọn đó hoặc chọn XPC/đóng gói/bảo trì production.

## Kết luận có thể dùng ngay

Có seam nhỏ tại `FileSystemSandboxRunner::run`, nhận **sandbox typed ban đầu và payload helper typed thực**, trước khi chuyển thành lệnh spawn. Nó có thể thay nhánh khởi chạy bằng adapter được cài rõ ràng. Tuy nhiên hai giá trị này **không chứa** native receiver session/connection, attempt, token và source scope; cần truyền thêm binding đã admission tại receiving effect task. Gắn callback tại spawn rồi tin argv/profile do receiver gửi không đáp ứng hợp đồng #36. [Runner upstream](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/exec-server/src/fs_sandbox.rs#L84-L142); receiving admission của patch được pin trong [kết quả #33](https://github.com/Riso-application-factory/zed-ai-crew/issues/33#issuecomment-6139161784), local `exec-server/src/server/registry.rs:166–280`, `crew_fs_admission.rs:35–45`.

Một prototype tích hợp có positive và deny thật, rồi chỉ ra receiver chết trong lúc helper còn sống, là điều tra hợp lệ để xác định **hợp đồng authority/lease còn thiếu**. Nó không phải candidate an toàn hoàn tất. Owner hiện nay nằm trong receiver; việc giữ guard bằng detached Tokio task bảo vệ cancellation của route khi receiver còn chạy, không làm owner sống qua receiver death. Host giữ child handle ngoài receiver phải có authority/publisher và lifecycle riêng; không thể xem owner FD EOF là helper đã kết thúc. Đây là suy luận source, chưa phải kết quả runtime mới. Local `crew_receiver_fixture.rs:28–33,47–88,152–177,184–219`; `server/registry.rs:173–185,267–279`.

## Luồng thực và điều kiện ACP chạm helper

| Phần | Source chính và kết luận |
| --- | --- |
| Runtime apply_patch | Local `core/src/tools/runtimes/apply_patch.rs:87–114,171–205` tạo `FileSystemSandboxContext` khi `attempt.sandbox_requested`; scope thread/turn/call/workspace/role/executor bọc thực thi patch. Source này có patch #33; phải dùng snapshot đã pin, không đối chiếu nó như source upstream nguyên bản. |
| Chọn attempt | [Orchestrator upstream](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/core/src/tools/orchestrator.rs#L265-L302) xác định `sandbox_requested` riêng với `SandboxType`. Remote executor đặt concrete process sandbox thành None nhưng không tự xóa `sandbox_requested`. Bypass hoặc policy không cần sandbox có thể khiến FS context None. Retry có thể đổi context sau approval; phải đo đúng attempt. |
| Receiver | Local `server/registry.rs:166–280` normalize typed request, acquire guard, gọi handler trong effect-owned spawned task, báo completed rồi drop. `server/handler.rs:381–444` kiểm initialized rồi gọi FileSystemHandler. |
| Native FS route | [LocalFileSystem](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/exec-server/src/local_file_system.rs#L105-L131) chọn sandboxed writes bằng `should_write_into_sandbox`, không chỉ kiểm Some. [SandboxedFileSystem](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/exec-server/src/sandboxed_file_system.rs#L167-L210) tạo helper request từ params thực và đặt **payload sandbox=None**: policy nằm ở launcher, helper dùng direct FS. Copy/remove đi cùng runner, `sandboxed_file_system.rs:296–340`. |
| Native helper | [Protocol](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/exec-server/src/fs_helper.rs#L53-L99) có operation/params cụ thể, không phải shell command. [Entrypoint](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/exec-server/src/fs_helper_main.rs#L36-L95) đọc một request, chạy direct implementation, trả native response rồi thoát. Cần EOF/newline đúng; host có thể giữ stdin làm barrier đã gắn nhãn, không sửa response để giả positive. |

Vì vậy actual ACP/native helper route có thể đạt được với attempt/policy phù hợp. **Chưa đo** nó trong #36. Fixture cũ dùng external policy và `sandbox:null` không chứng minh helper seam đã được gọi qua ACP. Một native wire positive tích hợp và một ACP direct-write positive chỉ là hai bằng chứng riêng, chưa phải ACP→helper positive.

## Giao hai trần: phần hỗ trợ và phần chưa có hợp đồng

Pinned upstream không có hàm intersection được tìm thấy trong các module policy/manager/Seatbelt đã đọc. Không kết luận toàn repository hay mọi OS không có cơ chế khác. [Policy `resolve_access`](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/protocol/src/permissions.rs#L1007-L1048) chọn root cụ thể nhất, rồi precedence cùng độ sâu là Read < Write < Deny; Write gồm quyền Read (`permissions.rs:120–136,4438–4441`). Glob hỗ trợ Deny và được xử lý riêng (`permissions.rs:454–457,1090–1236,1973–2028`). Đọc tập entries như một union allow đơn giản sẽ bỏ mất carveout và deny.

Seatbelt compiler xây mỗi allowance độc lập. [Writable-root preparation](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/protocol/src/permissions.rs#L1546-L1624) ghi nhận điều này và xử lý read-only subpaths. [Seatbelt compiler](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/sandboxing/src/seatbelt.rs#L896-L1089) có loại trừ đọc/ghi, bảo vệ metadata, symlink logical/target, ancestor rename, daemon transport và fcntl. **Ghép hai allow sections không tính giao.** Với fixture chỉ có tập root allow đơn điệu, filter `require-all(floorPredicate, requestPredicate)` cho từng lớp Read/Write là hypothesis có thể kiểm bằng OS; mọi allowance bổ sung phải nằm trong cả hai trần. Đây là đề xuất giới hạn, không phải compiler đã xác minh cho toàn bộ native policy.

Các shape chưa có mapping chính xác nên phải reject rõ ràng hoặc làm ticket riêng: globs, deny/read-only subtree rồi reopen writable descendant, special/project/tmp/home roots chưa materialize, non-POSIX URI, missing-root behavior, symlinked authority root, metadata carveout và arbitrary enforcement profiles. Reject không được silently biến thành unsandboxed fallback. Traversal/sibling/symlink phải có nội dung và non-effect controls thực, không chỉ lexical path test.

**Runtime reads dễ bị bỏ sót:** runner [thêm Minimal và helper binary read roots](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/exec-server/src/fs_sandbox.rs#L118-L140) qua `add_helper_runtime_permissions` (`fs_sandbox.rs:243–269`). Minimal kích hoạt platform defaults (`permissions.rs:962–974`), còn compiler có base profile và platform sections (`seatbelt.rs:1048–1068`). Nếu một trần chỉ cho đọc worktree nhưng launcher thêm các quyền này sau khi tính giao, lời hứa exact intersection bị thay đổi. Fixture giới hạn phải nêu rõ runtime floor được tin cậy, so sánh từng supplemental grant với cả hai trần, hoặc trả unsupported/runtime-unavailable. Cấm lặng lẽ mở `/`, `/dev`, executable path hay network để đạt positive.

Native spawn **không thừa hưởng toàn bộ environment**: `utils/pty/src/child_command.rs:94–102` gọi `env_clear`; `fs_sandbox.rs:35–61,519–551` dùng helper allowlist, explicit macOS descriptors và ReturnError fallback. Vì vậy nhận định ambient env inheritance từ `command.envs` đơn lẻ là sai. Adapter ngoài phải giữ hoặc thu hẹp mức kiểm soát đó, `close_fds`/fixed env và fixed executable; không nhận argv/profile/FD tùy ý từ worker.

## Authority, cancellation và receiver death

Hiện `Owner::command(publish/revoke/issueFs)` và `FsOwner::acquire` dùng cùng RwLock; read guard giữ revision/grant trong receiver (`crew_receiver_fixture.rs:47–88,94–110,152–177`). Bound fields là operation, normalized params (gồm policy), session, connection, executor, attempt, scope và one-use bit. Policy/current source publisher thật nằm ngoài phạm vi public fixture, không được gọi synthetic control FD là provenance thật.

Guard bao handler await nên bình thường helper reply/reap xong mới completed/drop. Nhưng [NativeChild Drop](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/utils/pty/src/posix_child.rs#L370-L393) gửi kill rồi reap ở thread riêng khi object bị drop; không phải bằng chứng synchronous quiescence trước guard release. SIGKILL receiver không chạy Rust Drop. Child ngoài ancestry do host sở hữu không tự mất khi receiver chết. [Direct copy](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/exec-server/src/local_file_system.rs#L927-L977) dùng spawn_blocking; cancel waiter không đồng nghĩa dừng filesystem effect. Một lease protocol phải chứa cả rejected launch, IO/error, timeout, disconnect, cancel, receiver exit, child exit và reap, không chỉ success callback.

Barrier scenario nên ghi: native request/guard held → host helper PID đã tạo và chờ stdin → receiver exit đã wait/reap → owner FD EOF/authority unavailable → helper PID còn sống → host hủy/reap hoặc thả payload có nhãn và ghi effect/result. Không có publisher sống để nhận publish thì **không** tuyên bố đã đo publication race hoặc stale-policy effect. Kết luận đúng là authority availability/lease continuity chưa được đáp ứng. Nếu thả payload sau receiver chết rồi nó ghi, ghi rõ đây là deliberate public counterexample, không production guarantee.

Move là chuỗi write destination rồi remove source, có delta từng bước. [Native Move implementation](https://github.com/openai/codex/blob/rust-v0.160.1/codex-rs/apply-patch/src/lib.rs#L619-L674) có thể để destination đã ghi khi remove thất bại. Không thêm rollback whole-patch hoặc immutable copy source vào completion tiêu chí #36.

## Smallest candidate interface và exact successor

Đề xuất interface: `launch(admittedBinding, typedSandbox, typedFsRequest) -> awaited native result`. Binding do receiver authority cấp sau normalize/acquire, gồm actual native session/connection/executor, source revision/context, attempt, operation/content/worktree/policy, fixed helper identity. Launcher lấy trần worker từ owner tin cậy, không từ receiver assertion; tự compile supported shape, tự chọn fixed argv/env/FD, sở hữu child handle và lease. Kết quả phân biệt không launch, launch thất bại, native denial, completed effect, cancel/disconnect và reaped. Publish/revoke phải đi cùng owner, chờ lease lifecycle thực, không hai publisher riêng dùng generation tương tự.

Nếu diagnostic chứng minh topology positive nhưng owner biến mất trước helper reap, câu hỏi successor chính xác là: **Authority nào sở hữu admission/publication/revocation và helper lease ngoài receiver, và protocol nào giữ hoặc thu hồi quyền khi receiver/connection chết đến khi child đã reap?** Đây là prerequisite/prototype contract, không build ticket. Các fields/IPC/packaging ngoài khả năng fixture vẫn là fog hoặc ticket nghiên cứu khi phrased được, không chọn âm thầm production architecture.

## Bằng chứng trước đó và Apple primary sources

[#34 canonical measured result](https://github.com/Riso-application-factory/zed-ai-crew/issues/34#issuecomment-6140172881) ghi native helper trong receiver ancestry setup exit71; exact independent helper allowed-write thành công và readonly denial thực; bỏ request policy làm mất readonly, chỉ giữ broader request làm mất outer write floor. R3 7/7 expected observations gồm **hai unsafe comparisons**, không 7/7 bảo vệ. Đây là prior Coordinator evidence được đọc, không rerun hoặc independent measurements của researcher.

Local Apple SDK `sandbox.h:23–27` nói sandbox_init khi đã sandboxed trả lỗi và bỏ profile mới; header deprecated. [Apple inheritance docs](https://developer.apple.com/library/archive/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW3) mô tả child inheritance qua entitlements. [Apple XPC docs](https://developer.apple.com/library/archive/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/CreatingXPCServices.html) mô tả service independently sandboxed/managed và lifecycle bất ngờ. Đã mở lại hai nguồn Apple trong lần nghiên cứu này. Chúng hỗ trợ phân tách authority; không chứng minh Crew XPC đã triển khai hoặc hợp đồng cancellation/publication đúng.

## Fingerprints của source đã đọc

Root native: `/Users/binn/.cache/crew-wayfinder/fs-helper-v0.160.1/codex-rust-v0.160.1/codex-rs`. Hashes mô tả snapshot **trước prototype #36**, không source hay binary Coordinator có thể sửa sau đó. Public archive SHA-256 `5226394058e04c5404fe737b84fbede38ebf41d88a72eb8b27f3d1c6ea23373b`; #33 source index SHA-256 `69f5fec93587d6651c2561cb958d1cdd15970b7d7b443456ba006e93705c47f9`.

| File relative to root | Bytes | SHA-256 |
| --- | ---: | --- |
| `exec-server/src/fs_sandbox.rs` | 39708 | `fb983066336be47363b12a5d3358d38b4e9ebee0c6c7fac67a7504711ce6e26d` |
| `exec-server/src/fs_helper.rs` | 18183 | `29ae0232036e374dae05a38453038b0448e779e93f56634800b76e4a40207513` |
| `exec-server/src/fs_helper_main.rs` | 3028 | `feca100953e8cb151371eaada589fccc67fab59a3efd33058691b09013d0a7f8` |
| `exec-server/src/sandboxed_file_system.rs` | 15331 | `f6112a8981bdb8c626405e52d64845a7bc9fc7ded5d1c73a10ca195cc9d9dbaf` |
| `exec-server/src/local_file_system.rs` | 47261 | `e682413100b3fcee03d285f4108c16ece9d3da34c217a2910afc3c22fc568aa3` |
| `exec-server/src/server/registry.rs` | 14858 | `13d8829ea9b03ea8a909517ef1e97a3a0d93a1bb5702ac30ad41221311ade034` |
| `exec-server/src/server/file_system_handler.rs` | 14660 | `fb61d2a279deb6bda510141c31355b0ac0b3456b89015531a8c0f08acc27410c` |
| `exec-server/src/crew_fs_admission.rs` | 4367 | `3d9d148d9b363f143d9451a874ab3d47b697aac2fe9d0ef6cd009522fd209c1d` |
| `exec-server/src/crew_receiver_fixture.rs` | 13020 | `c7ed78fff8ad9cf1775489137f6bf069fbff89bc06322a9f316652e535f2b3f2` |
| `core/src/tools/runtimes/apply_patch.rs` | 9719 | `fb71704636bed947e5c804283b29c9f9ef27eed6449d95ee2e87f390ce1238d2` |
| `core/src/tools/orchestrator.rs` | 24264 | `bc818810e1bd48001157d33df59b4624033595d13fd5070598b997627ee2e2f3` |
| `sandboxing/src/manager.rs` | 33156 | `a25017d49bb2876d1694a77efb30d4fb971ae0d6d8fd98a74bc08728d738c0af` |
| `sandboxing/src/seatbelt.rs` | 42832 | `03beaaaac24b6b856d4860ba4c1a6e3818e8cd2432dac7f23eddaef77e745ab5` |
| `sandboxing/src/seatbelt_base_policy.sbpl` | 3910 | `5103332ddb8885ee5e1926de6c0ef23a61f4e55e31297506ae05fa4e0b24ba74` |
| `sandboxing/src/seatbelt_read_only_platform_defaults.sbpl` | 7255 | `6c5c0ae4361e738053f172b635f4e1ab6287087f3d9b6e5882b0bc06922a7199` |
| `protocol/src/permissions.rs` | 179643 | `78a1c80b9b5cda29c563538d90003005d26c411eea533bd00db54a185a6aed54` |
| `file-system/src/lib.rs` | 25622 | `c57fa0ed1fb464f5d78b7e71652b946d2cd7bb3f5ef09fa7dad6e7f34869247d` |
| `utils/pty/src/child_command.rs` | 13138 | `7d3268737fa2fd37459d070271761c2d5b00e9dcbf3e14b17fd6e79158fcb532` |
| `utils/pty/src/posix_child.rs` | 19051 | `920ddf7e9bb061dba055d2841c3f39bbe0ba954117b74e387e5a2392cf976bc6` |
| Apple SDK `/Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/usr/include/sandbox.h` | 3475 | `5baa98c212110d6323f7d73f4b42b0072a4c66a826a46c8bfd8fb850305f9416` |

Research không đóng #36/#18/map, không chứng nhận full policy compiler, private provenance, process/network parity, real model interpretation hoặc release. OpenAI platform safeguard notice được Human báo là interruption của nền tảng, không phải evidence helper error và không thuộc diagnosis filesystem này.
