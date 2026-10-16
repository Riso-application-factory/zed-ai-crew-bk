# #14 — Nạp và khôi phục bộ hướng dẫn Orchestrator trên thread Zed

**Kết luận kỹ thuật cho planning, dùng góc nhìn Software Engineer/Architect trong công việc Coordinator.** Định nghĩa thread/vai trò là actual Human decision; cơ chế dưới đây được tổng hợp từ source và #9/#11, chưa implemented hoặc runtime-qualified. Không có specialist bên ngoài nhận việc/phê duyệt. Bản nội dung bộ hướng dẫn mặc định vẫn là draft riêng, không gán Human đã chọn hoặc review.

## 1. Điểm tích hợp đã đối chiếu

| Source surface                              | Hành vi hiện có                                                                                                               | Phần Crew cần bổ sung                                                                                                                  |
| ------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| `agent::Thread::new` / `from_db`            | Tách model, profile, templates và history; saved model resolve Ready/Unresolved, một số nhánh chưa có selection dùng defaults | Gắn thread với instruction binding rõ ràng; thiếu hoặc không resolve được binding thì báo lỗi thay vì tự gọi đó là Orchestrator        |
| `AgentProfileSettings`                      | Name, tools, context servers, default model; không có trường instruction bundle trong struct đã đọc                           | Definition/binding Crew bổ sung instruction refs; không giả chỉ cần đặt tên profile là đã nạp SOUL                                     |
| `agent::Templates` / `SystemPromptTemplate` | Templates bọc Handlebars nội bộ, system template cố định; native request dựng Role::System rồi thêm history                   | Hook context/instructions theo từng thread; không sửa global `system_prompt.hbs` hoặc AGENTS.md của Human để đổi mọi thread            |
| Native turn loop                            | Đọc lại model/tools giữa các round, có refusal fallback path                                                                  | Crew-bound turn kiểm actual model/profile/tool set khớp binding trước mỗi dispatch; không dùng fallback như consent tự đổi backend     |
| `Thread::to_db` / `DbThread`                | Lưu history/model/profile và metadata khác, chưa có Crew instruction binding                                                  | Lưu binding/manifest của Crew trong domain AppDatabase, liên kết bằng typed thread/conversation refs; native history không thay ledger |
| Compaction, summary, title                  | Có các call tới provider ngoài main completion; compaction model có thể lấy từ registry                                       | Scope theo purpose và data destination; không chỉ guard main request rồi để summary/title/compaction dùng global model khác            |

Phạm vi source audit có pin/hash/excerpts. Các dòng trên là quan sát code, không test hoặc chứng minh mọi provider có cùng instruction priority.

## 2. Nguồn và assembly được chọn

Definition của Orchestrator giữ một danh sách thành phần có thứ tự: system prompt, SOUL, các guidance/skills được chọn. Mỗi entry có identity/kind, source ref, revision/content ref và delivery channel áp dụng. Tên SOUL không buộc một đường dẫn hoặc một format mới; source phải được khai báo, không tự quét mọi file cùng tên rồi ghép. Nội dung mặc định đóng gói theo mẫu asset/definition của Zed/Crew; project overrides theo allowlist và precedence đã chốt #11, không dùng file role để cấp quyền.

Module resolver của Crew có Interface nhỏ: resolve một definition/source set thành một immutable instruction bundle hoặc lỗi chỉ rõ thành phần/nguồn. Nó đọc đúng bytes của nguồn đã resolve, kiểm schema/kind/source/classification/size/capability, giữ ordered refs và snapshot nội dung cần phục hồi. Hash nhận diện nội dung, không thay provenance hoặc current-authority admission. Missing SOUL/required component, unknown schema/channel hoặc input thiếu quyền không được bỏ qua rồi nạp một persona mặc định khác.

Role bundle khác với request context: yêu cầu Human, tài liệu dự án, summary, pending questions và references từ ledger được gắn riêng, không biến tài liệu hoặc lịch sử chat thành system authority. Các giới hạn platform/native/project vẫn do code thi hành. Không đưa credential/private grants hoặc raw secret environment vào bundle/LLM context.

Nội dung của bản mặc định trong `orchestrator-brain-draft.md` là đề xuất để review, không input đã được load. Nếu Human có bộ chuẩn, resolver dùng source đã được chỉ định; nội dung draft không tự override bộ đó.

## 3. Thread binding và lưu trữ

Crew domain trong AppDatabase lưu association giữa logical thread ref, native/ACP conversation refs, definition/bundle revision, ordered component/content refs, source/capability/profile/model revisions và context/run refs. Session/run không bị đồng nhất với thread. Nơi cần giữ exact instruction bytes cho resume thì snapshot có classification/retention riêng trong store hiện có; không dựa vào file nguồn live hoặc hash mà không còn bytes để phục hồi.

Controller là writer theo ADR-0003. Chỉ chuyển thread sang trạng thái sẵn sàng sau khi binding cần thiết đã ghi thành công trên persistent connection. Native thread history vẫn đi qua nền tảng history Zed; association Crew trỏ tới identity đó, không ghi authoritative run state vào chat blob. Nếu native thread tạo xong nhưng ghi association lỗi, nó là initialization chưa hoàn tất, không được gửi công việc hoặc tự tạo một operation mới để né lỗi.

Giữ #7: raw context/transcript không mặc nhiên giữ vô hạn vì được đưa vào snapshot. Minimal identity/decision/version/recovery refs được giữ theo policy; raw expiry hoặc explicit deletion có thể làm exact old context không còn. Khi đó báo rõ thiếu/đã hết hạn, chọn nguồn/context mới bằng một binding mới có kiểm tra; không giả exact replay. Chưa xóa hoặc mở DB thật trong quyết định này.

## 4. Cơ chế request theo từng thread

Thêm một seam trung lập, tùy chọn theo thread, tại phần dựng/chuẩn bị request của `agent::Thread`: nhận instruction context và request policy đã được resolve. `agent` không phụ thuộc crate Crew; implementation resolve/admit/bind nằm trong Crew. Native threads thông thường giữ behavior hiện tại khi không có policy này. Không thêm một controller hoặc storage engine khác vào Agent UI.

Với Crew-bound thread, hook tạo effective instruction messages từ immutable bundle và snapshot context; giữ source/delivery refs cho evidence. Trước provider dispatch, kiểm actual purpose/backend/model/profile/tools và binding vẫn được phép. Mismatch/error phải tới thread/Crew UI bằng Result/state, không panic hoặc log rồi tiếp tục bằng prompt cũ. Hook không chỉ là gắn thêm text vào user message, và không cấp toàn bộ tool registry của Zed cho Orchestrator.

Main completion chỉ expose các tool/ports cần cho đọc nguồn phù hợp và proposal/question/result theo #9. Nếu cần gọi thao tác, request phải vào controller để resolve scope/Ready/authority rồi mới dispatch effect owner; role text hoặc completion không tự đổi trạng thái. Tool set và effective policy là dữ liệu kiểm bởi code, không content do model tự tuyên bố.

Auxiliary requests được kiểm riêng:

- Compaction dùng context/purpose/profile được admission, không tự lấy một global compaction/fallback model khác. Sau compaction, bundle vẫn được nạp khi dựng request tiếp theo; summary không thay system prompt/SOUL hoặc ledger.
- Title/summary là optional presentation work: không chạy bằng model/destination chưa có binding và data permission. Có thể dùng nhãn/tóm tắt trạng thái xác định từ metadata khi chưa admit LLM auxiliary route, thay vì tự mở một đích gửi mới.
- Các native refusal/default/profile model changes không âm thầm đổi binding Crew. Human chọn model/profile mới thì apply #11/#20 đối với affected work; không đổi vai trò Orchestrator thành một danh tính khác chỉ vì backend đổi.

Đường native Zed dùng seam trên. Đường ACP nếu được cấu hình phải materialize cùng logical definition qua channel thực sự được adapter hỗ trợ; reuse measured role/skill rules #17 và guard #18/#20, không hứa system-tier parity từ tên SOUL. Không thêm yêu cầu mới rằng Orchestrator phải chạy mọi kiểu backend; ba worker ACP vẫn giữ requirement cũ. Backend delivery là mapping kỹ thuật, không mở lại câu A/B về danh tính.

## 5. New / resume / compaction / drift

| Trigger                                 | Trình tự thiết kế                                                                                                                                                      | Không được suy ra                                                               |
| --------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------- |
| New Orchestrator thread                 | Resolve sources/profile → assemble immutable bundle → tạo thread và persist association → prepare/admit request                                                        | Ordinary thread cùng model không tự là Orchestrator                             |
| Resume                                  | Load association/history → kiểm codec/source/bundle/profile/data policy và ledger refs → đối soát outstanding operations → dựng effective instructions trước turn tiếp | `load_thread` hoặc history còn tồn tại không chứng minh bộ não đã được nạp đúng |
| History có nhưng binding thiếu/hỏng     | Hiện initialization/recovery error; giữ dữ liệu, không dispatch cho tới khi khôi phục/bind có căn cứ                                                                   | Không tự suy role từ title/model hoặc reset thành run mới                       |
| Model không resolve                     | Giữ unresolved/blocked theo đúng selection; hiện phần cần cấu hình                                                                                                     | Không chọn một authenticated provider bất kỳ để thay                            |
| SOUL/guidance thay revision             | Giữ lịch sử cũ; affected work revalidate/rebind theo #11 trước request/effect mới                                                                                      | Không sửa live instruction snapshot giữa attempt hoặc đổi quyền bằng text       |
| Compaction/history được rút gọn         | Dùng purpose/profile đã admit; lưu lineage/summary refs; effective bundle được dựng lại độc lập                                                                        | Summary không giữ giùm identity, instructions hoặc quyền                        |
| Restore sau Zed crash                   | Reuse persistent Crew binding/intent và history Zed; reconcile #10/#20 trước new effects                                                                               | End-turn/receipt mất không là not-applied hoặc achieved                         |
| US đã hoàn thành, xuất hiện ý tưởng mới | Giữ OUTPUT/binding cũ; ý tưởng đi vào US mới                                                                                                                           | Thread còn tiếp tục không kéo dài PR/US đã hoàn thành                           |

## 6. Validation, giới hạn và return

Đã đối chiếu source và canonical #9/#11/#13/#14, xác định các hook/field chưa có và request paths phải kiểm. Không viết/sửa code production, không tạo thread thật, không gọi model, không nạp SOUL thật, không sửa global prompt/settings hoặc DB. Bảng lifecycle là design walkthrough, không executed model test. Stock Thread không được ghi là đã đáp ứng contract này chỉ nhờ source inspection.

Kế hoạch qualification cụ thể: new→save→restart→restore với same bundle; canonical SOUL đổi giữa hai turn; thiếu snapshot/binding/model; native model/profile đổi giữa round; refusal fallback; compaction/title/summary có destination khác; lỗi persist; late proposal sai attempt. Kiểm request thực nhận đúng instruction bytes/delivery refs và code không dispatch trên mismatch; sau đó kiểm hành vi LLM/rubric với Human, không dùng string match làm bằng chứng model luôn tuân thủ. Các phép kiểm chưa thực hiện, không lấy probe SQLite/UI trước đó làm pass.

Phần cơ chế instruction loading đã có phương án cụ thể để đưa vào packet #14. Nội dung bản mặc định vẫn draft, chờ biết Human dùng bản này hay có bộ chuẩn. Controller/effect-owner channel/custody/reconnect, native/host publication và upgrade/resource/packaging mappings khác vẫn cần giải quyết trong #14; không đóng parent hoặc map từ riêng phần này.
