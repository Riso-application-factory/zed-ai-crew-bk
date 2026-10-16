---
status: accepted
date: 2026-10-16
---

# Công bố artifact bất biến, giữ custody theo phạm vi tác động

Chọn cho planning: owner nhập exact bytes/path/mode của candidate vào store được bảo vệ, kiểm chứng độc lập đúng candidate rồi tạo Git commit/publication qua owner có quyền; không dùng mutable worker worktree hoặc sự biến mất của PID làm bằng chứng. Execution scope cũ giữ reservation/uncertainty cho tới terminal/reconciliation đủ; artifact publication chỉ có thể tiến độc lập khi scope cũ không còn đường tác động tới candidate, check evidence hoặc target được công bố. Đây là sửa kết luận kỹ thuật sau nghiên cứu #67, không phải Human nới quyền, bỏ review hoặc chấp nhận artifact chưa kiểm.

Baseline Human yêu cầu worktree riêng, ngoài worktree qua Crew/gate, code giữ quyền/state/OUTPUT và kiểm chứng đúng phiên bản; không yêu cầu universal physical tree stop trước mọi publication. API hiện tại đã audit chưa cung cấp supported unprivileged arbitrary-descendant closure trên host này. Giữ điều kiện đó cho mọi artifact sẽ chặn cả command/build/test có thể fork; chỉ dùng group cleanup/watch/PID absence lại không đủ chứng minh. Protected candidate tách tính bất biến của kết quả khỏi liveness của scope cũ, đổi lại cần importer, read-only check mappings, fenced publisher và quarantine/resource accounting chặt hơn. Đổi hướng sau này ảnh hưởng đồng thời workspace, verification, Git integration và recovery nên ghi rationale riêng.

## Giới hạn không thay

- Import không hứa atomic snapshot hay trạng thái cuối của command; candidate có identity/provenance mới và checks đúng bytes. Không dùng old check/native completed hoặc late old-attempt packet để credit candidate mới.
- Physical terminal vẫn cần trước khi claim command/engine đã dừng, nhả/reuse/delete mutable scope hoặc raw descriptor/grant, và những operation mà external mutation/lifetime contract đòi hỏi. Đổi directory/attempt không vượt reservation của cùng external target/operation uncertain.
- Không hardlink/import inherited writable FD tới protected candidate; official Git objects/index/refs, credentials, ledger và publication nằm ngoài worker write scope. Source/current-authority/criteria/PR approval và meaningful verification giữ #11/#12/#23. Các required paths chưa được isolation/qualified vẫn chặn delivery, không bị xóa khỏi scope.
- Quarantine giữ actual debt/reservation và capacity; không hứa thu hồi vô hạn hoặc xóa raw data đã không kiểm soát được. #7 retention/delete cùng #8 chất lượng vẫn áp dụng, báo partial/blocked nếu chưa đạt. Không chọn OS27, ES entitlement hoặc VM làm prerequisite mới.

Quyết định này do Coordinator ghi với góc nhìn engineering từ actual delegated primary-source research và bounded OS experiments, không gán specialist bên ngoài hoặc Human đã approve chi tiết triển khai. Nguồn: [nghiên cứu #67](../../artifacts/research/wayfinder-macos-process-custody.md), [phân định scope/publication](../../artifacts/evidence/wayfinder/architecture/publication-scope-decision.md), [ADR-0001](0001-hybrid-orchestration.md), [ADR-0002](0002-limited-filesystem-launcher.md), [baseline D3/D11/D13/D16](../../artifacts/shaping/solution-scope.md). Đây là quyết định kiến trúc cho spec, không implementation hoặc production qualification.
