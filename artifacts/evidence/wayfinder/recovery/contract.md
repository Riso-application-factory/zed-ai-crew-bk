# #10 — Intent, fact và đối soát trước tiếp tục

**Quyết định kỹ thuật cho planning:** durable intent trước dispatch; giữ operation/attempt/source identity; sau mất owner/result phải đối soát theo operation-specific postcondition trước khi xét retry hoặc tiến dependency. Không hứa exactly-once, transaction chung Git/run store/GitHub, rollback hoặc cancellation=completed. Chưa chọn database/production supervisor.

Nguồn đã giữ: [mô hình #3](https://github.com/Riso-application-factory/zed-ai-crew/issues/3#issuecomment-6107520661), [OUTPUT #4](https://github.com/Riso-application-factory/zed-ai-crew/issues/4#issuecomment-6107773445), [quality #8](https://github.com/Riso-application-factory/zed-ai-crew/issues/8#issuecomment-6108162948), [plan #9](https://github.com/Riso-application-factory/zed-ai-crew/issues/9#issuecomment-6187902710), [candidate/failure #18](https://github.com/Riso-application-factory/zed-ai-crew/issues/18#issuecomment-6187570049). Lựa chọn nghiệp vụ và quyền thao tác cũ không đổi.

## Identity và dữ liệu tối thiểu

- Session/run/plan revision/cycle-step occurrence/attempt và native/ACP session là các identity riêng theo #9. Retry tạo attempt mới nhưng không tạo operation mới chỉ để né một operation còn uncertain.
- Operation record giữ stable operation ID, project/repo/target entity/ref, payload/source/binding/criteria versions, precondition và postcondition được code hiểu, external correlation/idempotency contract khi thật sự có, owner/attempt và durable intent phase. Không lấy prompt hoặc status text làm external evidence.
- Code kiểm input/authority/freshness và lưu intent trước khi giao effect. Sau đó giữ external facts, return/terminal evidence và reconciliation observations riêng; cache run status được dựng từ facts. Đã có intent không chứng minh effect đã chạy; thiếu result không chứng minh effect chưa chạy.
- Trusted owner kiểm origin/identity và quyền ghi ledger. Hash trong probe chỉ phát hiện identity/content khác trong fixture, không phải MAC, authentication, full provenance hoặc OS-protected store. Concrete store transaction/access/schema/upgrade và supervision thuộc #14; source precedence thuộc #11.

## Đọc state không đồng nghĩa quy kết actor

| Quan sát | Trạng thái / hành vi |
| --- | --- |
| Không có/hỏng/misbound intent | Chặn do integrity; không tạo operation mới âm thầm. Có thể giữ quan sát external nhưng không suy zero historical effects từ việc thiếu record. |
| Không đọc được external state | Chặn read-back; giữ lỗi/retry-read context cho UI. Không coi API/path error là not-applied và không dùng repo/endpoint khác bằng phỏng đoán. |
| Current khớp desired postcondition | Ghi fact rằng postcondition hiện thỏa. Actor/attempt attribution chỉ được ghi khi có bằng chứng riêng; không suy từ current state trùng. Session OUTPUT vẫn qua verifier riêng. |
| Current khớp old precondition | Chỉ biết current chưa ở desired postcondition; không kết luận operation chưa từng có effect hoặc có thể phát lại mọi loại mutation. |
| Một phần sub-operations đã đạt | Giữ từng fact, lỗi và remaining work; không ghi whole operation/session thành achieved hoặc rollback tự động. Compensation là operation riêng, cần quyền/preconditions mới. |
| Current khác cả old và desired | Uncertain/conflict; không ghi đè current để ép expected result. Dùng operation-specific reconciliation hoặc hỏi Human về trade-off còn lại; không hỏi Human về facts có thể query được. |
| Source/spec/config đã đổi | Giữ fact đã xảy ra, invalidates applicability/remaining plan; không xóa lịch sử. #11 chọn affected closure/replan và input versions; #12 chọn approval/check applicability. |
| Cancel có effect | Dừng dispatch mới; giữ và đối soát fact. Chỉ có terminal custody mới kết luận effect đã dừng; cancelled turn/EOF không đủ. #20 kiểm native active cancel/resume thực. |

## Retry, tiếp tục và kết thúc

Retry không được suy từ timeout, missing result, generic error, provider end_turn hoặc restart. Chỉ được xét khi operation contract xác định tính idempotent/known-not-dispatched/supported idempotency key, preconditions/source/authority còn hiện hành, terminal/lifetime owner cho phép và policy retry đã được cấu hình. Không tự chọn số lần/thời gian retry trong #10. Các read-only reconciliation retries cũng cần bound/backoff của #11/#14; probe chỉ thực hiện hai lượt đọc cố định để quan sát lặp, không biến số2 thành retry budget sản phẩm.

Một target/entity đang có mutation uncertain phải giữ durable reservation/operation identity để tránh concurrent duplicate work; cụ thể transaction/lock owner thuộc #14. Read-back-confirmed postcondition có thể trả artifact cho verifier nếu các criterion/identity khác cũng đủ. Nó không tự cấp approval hoặc chứng minh actor, source provenance, arbitrary quiescence hay whole OUTPUT.

Resume/restart nạp ledger và revalidate current binding trước dispatch, không dùng ACP conversation history làm recovery store. Result đúng old attempt được ghi vào lịch sử đúng scope nhưng không làm plan revision mới tiến. Stale/duplicate/forged result không được credit lần nữa. Prototype #9 đã kiểm logic run/revision/attempt fencing; #10 không tự nhận đã chạy native packet replay hoặc total-supervisor replacement.

Đối với S04/S05 và các OUTPUT khác, giữ run execution state, finding/verdict và OUTPUT evaluation riêng. Uncertain/partial/cancelled có thể là trạng thái báo cáo trung thực; requested fix/deploy không được đổi thành report để báo achieved. Một assessment có finding inconclusive chỉ hoàn tất khi chính bounded report/evidence đó thỏa OUTPUT đã nhận theo #4.

## Phép thử đã thực thi

`python3 artifacts/evidence/wayfinder/recovery/probe.py` chạy12 fixture Git local, không có remote. Git objects và refs thật dùng actor `CREW_PUBLIC_FIXTURE`, email `.invalid`; không phải commit của Human hoặc target-project. Process-local Git config bỏ global/system config; không sửa user settings/credentials. Source/public assignment/intent/receipt ở cache dùng một lần.

- Eight owner processes thật bị SIGKILL và được parent trực tiếp wait/reap: trước intent,sau intent,sau effect trước receipt,sau receipt,external drift,source drift sau effect,cancel-intent sau effect và intent corruption. Parent còn sống đóng vai replacement reconciler, đọc lại file/refs; không claim một service khởi động lại sau cả host/supervisor chết.
- Nine `effect-issued` entries thuộc worker: các CAS Git thật cho old→new; partial case có ref đầu đã đổi và CAS ref thứ hai thất bại. Parent fixture component đổi ref thành third value để tạo current-state ambiguity; component này được ghi riêng, không gán thành native provider effect.
- Hai lượt đối soát mỗi fixture chỉ có các lệnh Git `rev-parse` trong command recorder; không có thêm effect-issued entry. Chúng cho cùng classification. Không dùng điều này để tuyên bố một external API bất kỳ exactly-once.
- Missing/corrupt intent chặn integrity; source đổi trước effect có zero effect-issued entry; source đổi sau effect giữ postcondition và needs-replan; cancel-intent sau effect giữ fact; actor khác đổi ref dẫn uncertain. Tạm di chuyển endpoint repo thuộc fixture làm actual read-back lỗi; phục hồi endpoint rồi query lại thấy desired state mà không phát lại mutation. Không fallback quét repo khác.
- Lượt đầu11 tình huống và source/report trước bổ sung attempt ID/command recorder/read-back-unavailable được giữ ở `v1-*`. Lượt cuối12 tình huống là số liệu chính, không giấu lượt trước hoặc gọi tất cả là các phép kiểm độc lập.
- Pure JS model có12 walkthrough và classification khớp12 observation của Git probe. Browser thực hiện59 DOM clicks trong12 walkthrough; model luôn giữ OUTPUT implement unachieved vì không có PR/main. Đây không phải actual Human feedback, GitHub integration hoặc UI latency benchmark. Mô hình browser chỉ dùng memory; actual persistence/crash evidence là probe Python riêng.

File store demo dùng fsync/temp-replace và local SIGKILL; chưa kiểm power loss, corrupted filesystem, DB WAL/migration, multi-process lock hoặc durable total-supervisor recovery. Không chọn JSON files làm production database. Không có actual provider cancellation/network race, GitHub mutation/review/idempotency, rollback hoặc full native policy proof; #19/#20/#14 giữ các quyết định/phép kiểm thật tương ứng.

## Lỗi và UI / handoff

UI phải nêu phase/operation/attempt/source,target,postcondition đã biết/unknown,partial effects,lỗi đọc/integrity và hành động tiếp theo. Không thay actual error bằng end_turn/completed. Debug/raw payload phải tuân data/retention policy #7; không buộc Human đọc secret-bearing transcript để quyết định. #13 cần walkthrough/feedback Human thật về các trạng thái này.

#11 nhận immutable source identity, drift và retry-policy requirements; #19 nhận operation-specific GitHub read-back/uncertain/approval contract, không được lấy fixture Git làm server evidence; #20 nhận terminal-proof/cancel distinction; #12 nhận partial/rework/version/approval applicability; #14 chọn concrete store/transactions/locks/supervisor/transport/upgrade. Không có product trade-off mới được chọn thay Human trong #10; nếu concrete integration sau không đáp ứng invariant, đưa exact counter về quyết định đó. Full map và spec/build vẫn chưa complete.
