# #12 — Integration, findings, checks, review và merge

**Status: resolved — planning contract, clarified by later Human feedback in #13.** Human xác nhận 1A/2A; sau đó làm rõ: US/PR đã đáp ứng yêu cầu ban đầu thì OUTPUT đã đạt, ý tưởng/yêu cầu bổ sung tạo US mới. Re-review/invalidation chỉ xét thay đổi thực sự áp dụng cho công việc chưa hoàn tất hoặc sai lệch với binding của chính thao tác; không dùng yêu cầu mới để hạ kết quả US cũ. Các nguồn/hạn chế runtime giữ nguyên, không có lượt probe production mới.

## Nguồn và điều đã chốt

- [Baseline D7/D10/D16/D17](https://github.com/Riso-application-factory/zed-ai-crew/issues/2#issuecomment-6106608411): một integration branch/một PR mỗi Story; implement OUTPUT là PR merged vào main; Human review trên GitHub; head đổi sau approval phải check/duyệt bản mới; Crew merge sau kiểm approved head. Không hỏi lại các lựa chọn này.
- [#10 recovery](https://github.com/Riso-application-factory/zed-ai-crew/issues/10#issuecomment-6188321867): durable intent trước effect, reservation và operation-specific readback; missing receipt không là not-applied; fact khác OUTPUT.
- [#11 binding/drift](https://github.com/Riso-application-factory/zed-ai-crew/issues/11#issuecomment-6188891332): immutable attempt, affected dependency closure, current policy admission; selector equivalence do schema/verifier, không model tự đoán whitespace. Comment/metadata không đổi input selector không phải spec revision. Không dùng issue.updated_at làm production spec version.
- [#23 check inventory](https://github.com/Riso-application-factory/zed-ai-crew/issues/23#issuecomment-6111628747), [#8 quality](https://github.com/Riso-application-factory/zed-ai-crew/issues/8#issuecomment-6108162948): Developers xác định applicable mandatory checks; missing/skipped/zero-tests không là pass; independent evaluation từ expected behavior, không suy spec từ code.
- [#19 actual gateway](https://github.com/Riso-application-factory/zed-ai-crew/issues/19#issuecomment-6194553428): real Bot author/Human review/current-head fixture check/merged parents, create và merge response-loss/controller restart/readback; source/head/check/review/base counterexamples với giới hạn đã khai báo.
- ADR-0001 giữ LLM đề xuất, code resolve/admit/dispatch/verify; không lựa chọn database, transport hoặc provider thay #14. D4/R5 vẫn cho phép Human sửa issue trên web, vì vậy chỉ serialize các Crew writers không chứng minh khóa mọi remote edit.

## 1. Tích hợp theo Story

Code giữ Story/repo/worktree/integration-ref/PR identity và source binding; task result có producer/attempt/base/head/tree và intended changes. Task worktree không tự push main/merge PR hoặc tự ghi achieved. Owner kiểm đúng repo/path/ref, actual current source/authority, expected input heads và operation reservation trước tác động.

Tích hợp tuần tự trên integration branch của Story dưới trusted effect owner; nhiều task computation độc lập có thể chạy song song. Trước mỗi merge/cherry-pick, kiểm index/worktree ở trạng thái đã biết; unexpected dirty/staged content chặn thay vì reset/clean để làm command pass. Git index.lock không thay logical operation reservation/ledger. Durable intent giữ expected old integration head và incoming task head; receipt ghi actual return/index/parents/tree/ref. Không giữ database transaction qua Git/network.

Nếu task head đã stale do source/assignment thay đổi, giữ nó như old-attempt fact và replan affected work. Không chồng patch của một attempt cũ vào generation mới chỉ vì file trông áp dụng được. Không tự tách PR mới cho mỗi rework; cập nhật đúng Story integration/PR identity. Scope mới ngoài Story đi qua impact/refine/ordering đã được Human cho phép, không âm thầm nới acceptance.

## 2. Conflict và findings

Conflict là trạng thái cần xử lý, không completed/failed-with-no-effect. Đọc unmerged index stages/MERGE_HEAD/rebase state và actual HEAD để biết đã có phần effect nào; không chỉ tìm chuỗi conflict marker trong text. Giữ worktree task và phần integration đã biết; không tự reset main hoặc hủy tất cả công việc độc lập.

LLM/worker có thể đề xuất patch resolution, code vẫn admission scope và kiểm kết quả. Mechanical resolution chỉ dùng quy tắc/tool đã được project cho phép với expected behavior rõ. Conflict buộc chọn nghiệp vụ/scope chưa có answer phải hỏi Human; không lấy “ours/theirs” làm mặc định nghiệp vụ. Sau resolution, verify không còn unmerged state, pin actual resulting tree/parents và chạy applicable checks trên bản tích hợp mới.

Findings giữ source/criterion/version/severity, reproduction/expected-versus-observed và disposition evidence. Finding chặn delivery theo #8 không được tự đóng bởi tác giả chỉ bằng “đã sửa”; independent verifier kiểm lại code/test/expected behavior và đúng tree. False positive/not-applicable cần reason/evidence có provenance; không xóa finding để đổi OUTPUT. Minor findings chỉ được xử lý theo quality decision đã có, không tự định nghĩa exception mới ở đây.

Rework tạo attempt/result/check revision mới theo #9/#11, không tạo operation identity mới để né mutation uncertain. Retry giữ bound/backoff của project policy; thay đổi input, fix hoặc readback được ghi là lý do mới. Không lặp lại cùng mutation mù khi timeout/API lỗi hoặc chạy “đến xanh” bằng cách bỏ test.

## 3. Check applicability và evidence

Developers xác định check set từ criterion/risk/project source, consume inventory #23 và actual changed dependencies. Ghi command/filter/features/toolchain/config/source tree/head/base/producer/attempt, actual exit status/test count/artifacts và phạm vi không đo. Với Crew, dùng `./script/clippy`; GPUI/native/UI/NFR cần đúng harness/seams/workload, không suy từ generic unit tests hoặc synthetic marker.

Check trên integration tree ứng với base và task heads hiện hành. Khi head/base/criteria/config thay đổi, dùng dependency rule để xác định evidence nào stale và phải chạy lại; không dùng check ID/commit giống nhau làm điều kiện duy nhất. Nếu không có verifier cho equivalence thì giữ stale/blocked, không model tự miễn. Artifact/source hash không thay authenticated producer/current authority.

Same-head rerun với cùng check definition/config/provenance chưa tự đổi Human review fact: trong lúc check pending/fail thì không merge; chỉ xét lại khi actual applicable check đạt. Nếu required definition/criteria/semantics mà review dựa vào đã đổi, áp dụng invalidation và Human re-review ở mục5; không biến mọi lần rerun cùng definition thành yêu cầu review mới. Missing/skipped/cancelled/neutral/zero-tests không tự đổi thành pass; accepted/enqueued không là completed effect hoặc OUTPUT.

## 4. Head đổi và review facts

Review phải là actual GitHub fact của authorized Human, APPROVED trên exact current head; App/worker/chat không tự tạo Human approval. Dismissed/stale/wrong actor/wrong head/changes requested không được credit. Giữ review cũ trong history kể cả đã invalidated; đây không phải xóa một Human decision.

Code/head đổi sau approval thì check lại affected scope và Human review head mới theo D10/D17. Base drift cần refresh integration từ actual base, resolve conflict nếu có, verify mới và re-review nếu resulting head đổi. Không rebase/force-push main để giữ một approval cũ trông hợp lệ. Không gọi check ở worktree rời chưa tích hợp là bằng chứng của integration tree.

## 5. Quyết định Human đã chốt: 1A và 2A

**Q1 / A — input của chính US đang làm thay đổi trước merge, code head không đổi:** dừng merge, cập nhật bản review với revision/bằng chứng mới và yêu cầu Human review lại PR. Điều kiện là thay đổi đã được xác định áp dụng cho chính US còn đang làm; một ý tưởng mới không tự thay acceptance của US này. Không cho Crew tự kết luận approval cũ đủ cho criteria mới. Review cũ vẫn là fact của binding cũ; GitHub còn hiện APPROVED không làm nó applicable tự động.

Code theo dõi explicit input selectors và revision/content identity theo #11, không dùng issue.updated_at làm spec version. Comment/metadata ngoài selector không invalidates criteria. Nếu equivalence chưa được schema/verifier xác định thì raw input edit tạo affected stale state; LLM không tự miễn với lý do “chỉ sửa lời”. Những thay đổi làm expected behavior, scope hoặc mandatory check definition mà Human đã xem khác đi đều phải trình bày bản review mới.

Gateway giữ review request/bundle revision, exact head/tree/source/criteria/check-definition refs và record actual Human review ID/actor/commit khi re-review bundle mới. Cùng head vẫn cần một actual new review đáp ứng yêu cầu đã trình bày; không lấy old review ID, một timestamp edit, model assertion hoặc chat “đồng ý” làm GitHub approval. Prototype #19 timestamp freshness là experiment guard, không production applicability rule. Detailed review-bundle DTO/history belongs to the chosen architecture/spec; this contract fixes the behavior and source/identity requirements.

**Q2 / A — preflight/readback, chấp nhận giới hạn không nguyên tử:** kiểm current binding/authority/effective server rules trước dispatch; gửi expected approved head SHA; đọc lại actual PR/ref/object/source/check/review sau effect. Không tuyên bố full-tuple CAS hoặc mọi thay đổi remote đều được nhìn thấy. Không nới server review/check rules để làm command thành công.

Nếu post-effect observations không khớp binding của chính thao tác đã được giao (ví dụ actual merged head H1 khác approved head H2), hoặc chưa chứng minh được outcome của binding đó, giữ actual merged/partial/uncertain facts và đối soát phần sai lệch thực. Không dùng ý tưởng mới, acceptance của US mới hoặc revision xuất hiện sau merge làm lý do chặn OUTPUT của US đã đáp ứng yêu cầu ban đầu. Không tự rollback main hoặc mở rộng quyền để sửa. Compensation/follow-up là công việc riêng có preconditions và authority; không gửi merge lần nữa. Giới hạn này là trade-off Human đã chấp nhận, không stronger at-instant guarantee của phương án B.

Readback chỉ cho evidence trong cửa sổ quan sát; không chứng minh mọi transient edit hoặc all-party atomicity. OUTPUT evaluation mang binding/evidence/time của yêu cầu US được giao và limits. US đã đáp ứng yêu cầu ban đầu, check/review và merge đúng phiên bản thì đã đạt OUTPUT, kể cả Crew nhận phản hồi merge chậm hơn thời điểm xuất hiện một ý tưởng mới. Yêu cầu bổ sung đi vào US mới qua refinement và ordering; không kéo dài US/PR cũ hoặc tự đổi kết quả đã đạt. Một sai lệch thực với yêu cầu/binding ban đầu vẫn phải ghi nhận đúng; không gọi đó là ý tưởng mới để che lỗi. Timing phát hiện hoặc timestamp sửa issue không tự quyết định thay đổi thuộc US nào.

Không hỏi lại D10/D17, không thêm nút Approve/Re-run hoặc gate Human cho từng retry. Hai lựa chọn được lưu trong resolution #12; chỉ revisit nếu có evidence làm contract không đáp ứng mục tiêu.

## 6. Merge, errors và phục hồi

Trước mutation, gateway kiểm project/repo/ref/PR/actor/current authority, bound source/head/base/check/review và actual relevant policy/capabilities; thiếu mandatory fact chặn route tương ứng. Mỗi target-project cần actual allowed integration/auth/setup scope riêng; quyền fixture đã cấp không tự cho sửa ruleset target hoặc dùng Human credential review.

Code gửi expected approved head SHA, durable merge intent và stable entity/operation identity. Sau response, query actual PR/ref/object relationship để nhận merged fact; 2xx/enqueued/open PR không phải OUTPUT. Gate/review/merge fact tách OUTPUT evaluation; policy Q2/A ở trên điều khiển trường hợp post-effect gap.

409/precondition mismatch: đọc lại source/head/base/check/review, không đổi expected SHA để retry mù. 405/not-mergeable/rule failure: giữ actual reason và merged readback; không tắt rule để chạy lại. 401/403: ghi auth/permission condition, không tự mở rộng scope/PAT/account. Network error/response loss: operation uncertain, reservation còn giữ; đọc lại đúng PR/entity, không POST/PUT lần hai nếu chưa có quyết định operation-specific. Same operation đã merged chỉ trả existing receipt. Compensation là operation riêng cần authority/preconditions mới, không auto rollback main.

Nếu cả owner/controller chết hoặc receipt/source không đọc được, giữ uncertainty; không tự phát grant hay suy not-applied. Existing #10/#20 custody/reconciliation contracts áp dụng, concrete lifetime/store/transport vẫn #14. Cancel/turn terminal không làm Story achieved và không xóa committed effects.

## 7. Kiểm chứng đã thực hiện

**Mới trong #12:** `local-integration-probe.py` tạo một Git repo/worktree synthetic riêng, global/system Git config không đọc, hooks trỏ empty fixture directory, actor/email fixture `.invalid`. Không sửa target-project/repo Crew source.

- Actual merge cùng dòng conflict return1, ba unmerged stages; integration HEAD và main giữ nguyên, MERGE_HEAD đúng incoming task.
- Explicit fixture resolution tạo merge commit có đúng hai parents; đây là expected value của fixture, không tự chọn business-rule resolution.
- Check actual fail1 trên integration; rework commit mới check0. Giữ head/tree của từng check, không gán check cũ cho tree mới.
- Independent fixture component dịch main; local expected-old ref CAS return128 và không ghi đè moved base. Refresh integration từ actual base rồi check mới đạt. Worker worktree HEAD/content giữ nguyên.
- Local successful ref CAS chỉ kiểm primitive; không thay GitHub PR/Human gates và không gọi Story OUTPUT achieved. Public Git fast-export/hash và command recorder lưu để đối chiếu.

**Kế thừa:** #19 actual GitHub reviews/checks/mutations/races/loss/recovery và #10 local Git crash/readback, với giới hạn nguyên bản. Không gọi chúng là lượt rerun #12. Không có production Crew engine, arbitrary GitHub atomicity, power-loss/full supervisor/private-worker proof. Chưa chạy Human UX #13 hoặc whole C01–C11/S01–S15 audit #15.

**Return:** #12 complete trong phạm vi contract/decision với actual Human 1A/2A và cited Git/GitHub evidence. Chuyển #13 để kiểm Human hiểu waiting/error/recovery/evidence/version/next action, tiếp tục #14 các cơ chế kiến trúc còn mở và #15 whole-product closure. Map chưa complete, chưa tổng hợp spec/build tickets hoặc claim sản phẩm đã giao. Không rerun probe/GitHub mutations trong lượt synthesis này.
