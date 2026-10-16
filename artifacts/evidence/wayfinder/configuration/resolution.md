# #11 — Cấu hình, version và drift của session đang chạy

**Status: resolved — quyết định kỹ thuật cho planning.** Chọn cấu hình có schema và override allowlist, binding manifest bất biến cho từng attempt, kiểm freshness trước dispatch/resume và admission của effect, cùng invalidation theo dependency thực. Không hot reload cấu hình vào attempt đang chạy. Giữ nguyên lựa chọn Human đã xác nhận; đây không phải Human vừa duyệt một cấu hình mới hoặc cơ chế đã được triển khai.

## Nguồn đã đối chiếu

- [Mô hình Session/run #3](https://github.com/Riso-application-factory/zed-ai-crew/issues/3#issuecomment-6107520661), [OUTPUT #4](https://github.com/Riso-application-factory/zed-ai-crew/issues/4#issuecomment-6107773445), [dữ liệu #7](https://github.com/Riso-application-factory/zed-ai-crew/issues/7#issuecomment-6108000427), [quality #8](https://github.com/Riso-application-factory/zed-ai-crew/issues/8#issuecomment-6108162948).
- [Role/skills #17](https://github.com/Riso-application-factory/zed-ai-crew/issues/17#issuecomment-6109950179): actual restart/load giữ role R1 nhưng Claude/Codex đọc/chạy skill R2. Conversation history không đủ để chứng minh binding đồng nhất.
- [Session plan #9](https://github.com/Riso-application-factory/zed-ai-crew/issues/9#issuecomment-6187902710): registry và identity do code sở hữu; run/plan/attempt khác nhau; result trễ không credit plan mới.
- [Recovery #10](https://github.com/Riso-application-factory/zed-ai-crew/issues/10#issuecomment-6188321867): actual local Git source drift trước/sau effect, cancel sau effect, intent corruption và read-back failure; giữ facts, không blind replay.
- [Checks #23](https://github.com/Riso-application-factory/zed-ai-crew/issues/23#issuecomment-6111628747), [native policy snapshot #28](https://github.com/Riso-application-factory/zed-ai-crew/issues/28#issuecomment-6125989553), [bounded confinement / remaining mechanisms #18](https://github.com/Riso-application-factory/zed-ai-crew/issues/18#issuecomment-6187570049).

Coordinator dùng góc nhìn Software Engineer/Business Analyst để tổng hợp contract. Không có specialist bên ngoài nhận việc hoặc phê duyệt; không phát sinh lựa chọn nghiệp vụ mới cần suy ra từ việc Human nói tiếp tục.

## 1. Hai quy tắc kết hợp cấu hình

**Giá trị mặc định:** packaged definitions → project override đã validate → explicit Session/step binding trong phạm vi yêu cầu đã nhận. Chỉ field được registry tin cậy đánh dấu overridable mới dùng precedence này. Project config không tự sửa schema, allowlist hoặc cấp capability bằng cách khai báo thêm field. Binding cụ thể phải tham chiếu definition/profile đã được admission; prompt, issue text và worker result là dữ liệu, không phải config authority.

**Ràng buộc:** quyền hiệu lực là giao các giới hạn applicable của platform/native mandatory policy, project/data policy, assignment và operation; mandatory criteria được giữ đầy đủ, không ghi đè theo last-write-wins. Project có thể thu hẹp quyền hoặc thêm criterion/check. Không được bỏ Story Ready, PR/review gate, chất lượng bắt buộc, đổi OUTPUT, nới data destination hoặc biến role text thành quyền. Policy từ nguồn có thẩm quyền vẫn có thể được sửa bởi chính authority đó; thay đổi này tạo revision mới và phải admission lại, không phải override âm thầm của project.

| Thành phần | Project được cấu hình | Không được suy ra từ override |
| --- | --- | --- |
| Playbook/cycle/step | Chọn definition/version đã biết, tham số và nhánh hợp lệ; thêm definition qua quy trình registry tin cậy | Tự thêm executable/grant, bỏ gate hoặc đưa graph chưa validate vào attempt đang chạy |
| Role/skills | Nội dung, nguồn canonical và revision theo binding đã chốt ở #17 | Instruction priority parity giữa providers; quyền sửa policy hoặc tự xác nhận completed |
| Criteria/checks | Thêm tiêu chí, chọn applicability có lý do theo schema và project; giữ mandatory baseline | Missing/skipped/zero tests là pass; model tự miễn check bắt buộc |
| Profile/model/mode | Tham chiếu profile được phép, admission theo tác vụ/dữ liệu | Tự đổi provider/account/plan/billing khi quota hoặc capability thiếu |
| Data/retention | Thu hẹp dữ liệu/đích đến; explicit investigation hold đúng scope | Tự public hóa nội dung target-derived hoặc thay thời hạn Human đã chốt |
| Retry/resource limits | Giá trị trong envelope đã được qualification và giới hạn cấp trên | Timeout nghĩa là chưa có effect; retry vô hạn; các con số của probe trở thành budget sản phẩm |

Parser từ chối schema/version/field/definition/capability không biết, reference sai project/type hoặc override bị cấm. Trả lỗi có field, nguồn, expected/observed revision và tác động; không bỏ qua field rồi chạy bằng default. Config không hợp lệ chỉ chặn dependency bị ảnh hưởng nếu phần độc lập có đủ binding/policy riêng đã biết. Không dùng một lỗi policy toàn cục làm lý do cho nhánh vẫn được admission bởi chính policy chưa đọc được đó.

## 2. Binding manifest của attempt

Mỗi attempt trỏ tới manifest immutable, code resolve từ nguồn authoritative, gồm:

- Project/repo/tracker identity; worktree canonical path và object/checkout identity; Story/subject/spec refs; head/base và input artifact lineage.
- Schema/registry/playbook/cycle/step/verifier versions; criteria và check applicability revision; typed input refs với project/type/version/selector.
- Role/skill contents hoặc immutable artifact refs, hashes và canonical realpaths; delivery channel; adapter/backend/executable pin, profile/model/mode và capability qualification revision.
- Policy nguồn, effective constraints và provenance/status của loader; assignment/operation scope; env/FD/process-info/egress/control-path/resource envelope; data classification và permitted destinations/consent refs.
- Session/run/plan revision/cycle-step occurrence/attempt identity, native/ACP conversation refs riêng; policy/source generation và owner/terminal-custody refs. Credentials chỉ là opaque trusted auth refs, không là payload manifest hoặc LLM context.

Manifest dùng schema có version, typed fields và deterministic UTF-8 JSON: object keys được sắp theo quy tắc schema, arrays giữ thứ tự có nghĩa, không chấp nhận duplicate keys/non-finite numbers. Bản bytes nguồn được hash riêng; chỉ các field/selector có semantics được schema định nghĩa mới có thể có normalized content revision. Content hash/revision dùng để nhận diện, không phải chữ ký, proof nguồn tin cậy hoặc atomic lease. Codec lưu trữ/migration và giao thức authenticated publication cụ thể thuộc #14; không lấy diagnostic JSON từ worker làm manifest tin cậy.

Một issue number hoặc `updated_at` không là spec version: comment tiến độ không tự sửa spec. Manifest chỉ rõ canonical body/attachment/typed section nào là input. Thay đổi ngoài các selector đã biết không tự invalidate phần không phụ thuộc; thay đổi trong input chưa phân loại được thì conservatively stale. Không giao LLM quyền tùy ý tuyên bố một thay đổi là semantic-equivalent để giữ gate cũ.

## 3. Snapshot, materialization và freshness

Chọn **immutable runtime view + current-authority admission**, thay vì đọc trực tiếp file role/skills live trong attempt. Trusted owner tạo runtime materialization từ revision đã resolve, giữ read-only đối với worker và pin native delivery channel. Canonical project source vẫn có thể thay đổi; thay đổi đó không sửa snapshot đang chạy nhưng phải được phát hiện để ngăn dispatch/effect mới dựa trên quyền hoặc đầu vào đã cũ.

Giữ binding #17: Claude/Codex AIR role metadata; skills canonical `.agents/skills`, Claude native bridge theo profile đã kiểm; Antigravity versioned prompt fallback, không coi là native system tier. Chỉ `session/load` thành công không đủ: phải revalidate role/skill/source/profile/policy trước resume. Nếu native route không thể materialize/map đúng nguồn hoặc không xác lập effective policy đầy đủ, capability đó chưa được admission. #14 phải chọn concrete bridge/host mapping; không giả có SDK flag chưa được chứng minh.

Các file control/config active không được worker tự sửa để thay policy giữa effect. Việc Story thực sự cần sửa `.agents`/control source vẫn được phép đề xuất như artifact và áp dụng qua owner/broker được giao quyền, sau khi generation cũ đã retire; không thêm Human approval cho mọi ordinary write đã được phép. Whole-directory freeze của #63 chỉ là bằng chứng bounded, không tự biến thành final authoring workflow. #14 phải giải quyết tách editable source/runtime view và native host consumers thực tế.

Kiểm drift trước step, trước resume, trước effect admission và khi authority bị revoke. Pre-step compare một mình không đóng race giữa kiểm và effect; owner phải giữ generation/authority qua effect và terminal custody theo #18/#20. File hash khớp không chứng minh managed policy không có. `null`, projection thiếu hoặc denied loader không là proof không có MDM/native requirement; trusted loader phải phân biệt load thành công/absence có căn cứ/unsupported/error. Unknown mandatory source chặn route tương ứng.

## 4. Thay đổi khi đang chạy

Khi binding liên quan đổi: đánh dấu affected dependency closure stale/needs-replan, chặn dispatch mới của phần đó, báo Human điều gì đổi và next action; giữ facts/effects đã xảy ra cùng source cũ. Nhánh độc lập với binding còn hiện hành tiếp tục. Attempt đã admit effect không được đổi profile/source tại chỗ hoặc coi tự động rollback: retire/cancel theo #20, chờ terminal custody và reconcile #10 trước khi giao generation thay thế.

Replan kỹ thuật trong intent/scope/quyền đã nhận không cần một approval mới cho từng graph revision. Thay đổi mục tiêu/scope/OUTPUT, quyền công bố, provider/account/billing hoặc trade-off sản phẩm chưa có câu trả lời cần actual Human decision. Native mandatory policy chỉ có authority tương ứng mới thay được. Không hỏi lại lựa chọn đã chốt.

Evidence giữ nguyên producer/attempt/command/result và version tuple của lúc chạy. Một check cũ không bị xóa vì source mới; applicability tới head/base/criteria/config/profile mới phải được đánh giá lại bằng dependency rule. Reuse chỉ khi rule verifier xác nhận tất cả dependency liên quan vẫn đồng nhất. Thay code/head sau approval đã có quy tắc kiểm/duyệt lại. Criteria/spec đổi nhưng head không đổi: giữ nguyên GitHub review fact, dừng merge cho tới applicability contract #12; #11 không tự chọn Human đã chấp thuận phạm vi mới.

## 5. Tình huống đối chiếu

Các dòng dưới là **phân tích contract**, không phải lượt runtime mới. Runtime source ở cột cuối đã thực thi trong ticket nguồn với giới hạn riêng.

| Tình huống | Kết quả contract | Bằng chứng hoặc giới hạn |
| --- | --- | --- |
| Load conversation role R1 nhưng skill canonical đã R2 | Không credit binding R1/R1; record mismatch, chặn affected dispatch, materialize revision được chọn rồi replan/resume | #17 quan sát actual R1/R2; cơ chế pin production chưa có |
| Human sửa acceptance input trước step | Input revision đổi; affected steps/evidence stale; báo thay đổi và replan | #9 source-drift logic; #10 actual drift trước effect |
| Canonical input có edit chưa phân loại được, kể cả format | Stale theo raw source identity trừ khi schema có normalized selector equivalence xác định | Không dùng model đoán “chỉ whitespace” làm authority |
| Chỉ thêm comment báo tiến độ, không đổi input selector | Giữ spec revision và bằng chứng còn applicable | Selector phải explicit; không dùng issue timestamp làm version |
| Project config yêu cầu tắt mandatory review/egress policy | Validation reject override; không dispatch bằng cấu hình bị nới | #9 authority validation; native enforcement giới hạn #18 |
| Source đổi sau effect nhưng trước result | Giữ effect và source cũ; needs-replan, reconcile; không credit plan mới | #10 actual Git drift-after-effect và #9 late-receipt logic |
| Profile quota hết hoặc muốn đổi model | Chờ đúng nhánh; không silent switch. Actual lựa chọn profile khác phải được admission và binding mới | Giữ lựa chọn Human; không chạy quota stress |
| Schema/version mới chưa hỗ trợ hoặc malformed config | Báo lỗi field/version; không fallback bỏ qua field | Validation contract; chưa có parser Crew production |
| Actual profile không có bằng chứng no-training cho private payload | Chặn affected private-data path; public/synthetic độc lập có policy đã biết vẫn có thể tiếp tục | #7 conditional admission; chưa kiểm account thật |
| Run tạm chờ Human/quota lâu hơn 30 ngày | Không kích hoạt đồng hồ expiry raw theo permanent stop; giữ state cần thiết | Actual Human #7; không xóa dữ liệu trong phiên này |
| Spec/criteria đổi, code head không đổi và GitHub approval còn | Giữ review fact, không tự cho merge; #12 chốt approval applicability | Không ngầm biến technical invalidation thành câu trả lời Human |
| Story cần sửa control source/skills | Qua owner được cấp quyền, giữ active runtime view bất biến và retire generation trước publication | Authoring không bị loại khỏi scope; concrete mapping #14 |
| Restart có history và result trễ của old attempt | Revalidate ledger/binding; lưu fact đúng old identity, không replay hoặc credit new attempt | #9 cross-run control, #10 actual read-back; active native #20 còn mở |

## 6. Dữ liệu, retention và context sạch

Giữ chính xác #7: raw transcript/log/diff do Crew quản lý giữ 30 ngày sau completed hoặc permanent stop, explicit investigation hold theo scope; necessary recovery state/actual decisions/minimal version-outcome evidence giữ tới Human yêu cầu xóa. Derived private payload giữ classification. Target content ở local/private target tracker; ví dụ target-derived public cần redaction và actual permission đúng scope. Main-model consent không cấp mọi auxiliary/MCP/telemetry destination. Credentials không đi vào model/log/export; no-training và provider retention là hai điều kiện khác nhau. Không có dữ liệu nào được xóa hoặc setting account nào được sửa ở đây.

Contract handoff/context sạch phải mang: goal/scope/actual decisions và rationale; canonical spec/glossary/ADR links; project/repo/worktree/branch/PR/head/base và input versions; role/skills/profile/channel/policy/source versions; data permissions; evidence nguồn/trạng thái/giới hạn; run/plan/attempt/operation ledger refs, outstanding uncertainty và terminal custody; unresolved blockers, exact next action và return criterion. Không sao chép secret/live grant; PID trần không là identity. Context mới phải đọc canonical inputs, reconcile outstanding operations và cấp lại current capability qua trusted owner trước dispatch.

Đây là data contract cho handoff, không giả đã chuyển người/tool/session. Giữ clarification → spec → tickets trong context khi còn cần reasoning; eventual implementation dùng context sạch cho từng ready build ticket với canonical sources. Chưa sinh spec hoặc build tickets trong #11.

## 7. Rationale, validation và phần tiếp nhận

Bác live hot reload vì #17 đã đo role/skill mixed revisions; bác last-write-wins cho quyền vì không bảo toàn authority; bác timestamp/hash-only vì không chứng minh provenance hoặc lifetime. Chọn immutable per-attempt snapshot và dependency invalidation để giữ reproducibility mà không khóa mọi nhánh hoặc bỏ facts sau drift. Đây cụ thể hóa #9/#10 và lựa chọn đã có, không phải một thay đổi hard-to-reverse mới cần ADR riêng.

Đã đọc/đối chiếu canonical resolutions và giới hạn runtime nêu trên; scenario table là reasoning mới. Không chạy native adapter, account preflight, parser production, check suite, Human walkthrough hoặc power-loss test ở ticket này. Không claim snapshot freeze, privacy, authority publication, retention scheduler hay whole-tree terminal đã implemented/verified. Không chọn numeric retry/resource budget chưa đo.

- #19 nhận version/precondition manifest để kiểm GitHub gateway và quyền fixture thực.
- #20 nhận immutable attempt/generation và retire/reconcile rules để kiểm active cancel/resume.
- #12 nhận applicability/invalidation, đặc biệt criteria/spec change với code head không đổi.
- #14 phải chọn runtime-view/native loader/provenance/host mapping, authenticated publication/lifetime, concrete storage/transport/migration và qualified budgets; đây còn là các câu hỏi thiết kế thực, không đổi tên thành “tests tương lai” để xóa fog.
- #13 nhận thông tin drift/error/evidence/next action cần Human hiểu thật.
- #15 nhận câu hỏi đã diễn đạt được: baseline recipes S01–S15 theo schema #9/#11, mỗi recipe có typed inputs/OUTPUT predicate/guards/gates/conditional-rework route và evidence requirements; đối chiếu C01–C11 trước closure. Không nhập 33 cycles cũ hoặc coi 15 shape records là 15 recipes đã kiểm.

#11 complete trong phạm vi quyết định cấu hình/version. Map vẫn draft/awaiting decision; chỉ complete khi các quyết định phụ thuộc và fog toàn destination đã xử lý thật.
