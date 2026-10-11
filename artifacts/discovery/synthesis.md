# Tổng hợp discovery · Zed AI Crew

**Draft · 2026-10-11 · Business Analyst.** [Bản xem HTML](synthesis.html). Tổng hợp riêng vòng 1 và vòng 2 từ bản ghi đã có; không có interview mới trong phiên này. Quyết định R1–R9/F1 được giữ nguyên. Discovery đã dừng theo luật hỏi cũ; tiêu chí bằng chứng high-risk của bước hiện tại **chưa đủ để ghi complete**. Không mở vòng 3 hoặc thêm gate duyệt kit.

**Kết quả chính:** nhu cầu đã ghi là giảm chuyển prompt thủ công, code khó bảo trì và công đọc HTML. Ranh giới điều khiển, tích hợp, quota và môi trường đã có lựa chọn Human. Chưa chứng minh điều phối tự động giảm rework, cưỡng chế được ranh giới worker hoặc phục hồi không lặp hành động. Có thể chuẩn bị playback problem brief với các giới hạn này; không tuyên bố giải pháp đã được chọn hay kiểm chứng.

## 1. Nguồn và giới hạn

| Mã | Nguồn đã đọc trong phiên này | Loại bằng chứng |
| --- | --- | --- |
| S1 | [Intake](../intake-brief.html), Q1–Q3, F1–F11, C1–C7, E1 | Bản ghi trước: lời kể và quyết định được gán cho Human. Không có transcript gốc để xác minh lời nói. |
| S2 | [Bản ghi discovery](questions.html), R1–R9/F1, M1–M11, luật dừng | Hai vòng ngày 11/10. Trích dẫn dưới đây là nguyên văn **trong bản ghi**, không phải lời được BA trực tiếp nghe. Lựa chọn A/B/C được diễn giải theo hệ quả ghi kèm, không tự viết thành quote. |
| S3 | [Discovery kit](discovery-kit.md), P1, H1–H4, A1–A12, G1–G4/E1–E9 | Giả thuyết và guide đề xuất; không phải câu trả lời, owner đã nhận hay nghiên cứu đã chạy. |
| S4 | [Domain primer](../research/domain-primer.md), §2–3/§9–10 | Tổng hợp nghiên cứu trước, journey và glossary nháp. Không kiểm lại claim nguồn web trong phiên này. |
| S5 | [Technical landscape](../research/technical-landscape.html), baseline 10/10 | Báo cáo Architect trước; các lựa chọn đã có S2 thay thế những câu hỏi cũ tương ứng. Không coi báo cáo là bằng chứng Crew chạy end-to-end. |
| S6 | [Phương pháp TD1](../evidence/technical-discovery/td1/README.md), [Claude](../evidence/technical-discovery/td1/claude-2-prompt.jsonl), [Codex](../evidence/technical-discovery/td1/codex-2-prompt.jsonl), [Antigravity](../evidence/technical-discovery/td1/antigravity-2-prompt.jsonl) | Transcript probe cũ đã đối chiếu trực tiếp: Claude lỗi auth, Codex BANANA, Antigravity OK. Không chạy probe mới. |
| S7 | [SDLC](../sdlc.html), khai báo `workflows`; [fork docs ACP](../../docs/src/ai/external-agents.md), bảng permissions; `crates/agent_servers/src/custom.rs` | Đã đọc cấu trúc workflow, tài liệu ranh giới quyền và đoạn cấu hình env. Không quan sát Human sử dụng hoặc kiểm sandbox thực tế. |

Repo baseline phiên này: `826ed60109a2505e3a1d9ba27292c793912c7549`. Đã truy vấn `gh issue list -R Riso-application-factory/zed-ai-crew --state all --limit 50`: trả `[]`; chưa có issue để dẫn làm spec/ticket hiện hành. Synthesis là bản ghi nghiên cứu, không thay thế GitHub Issues làm nguồn spec theo [quy ước tracker](../../docs/agents/issue-tracker.md).

## 2. Tổng hợp vòng 1 · R1–R9

Ngày theo S2: 2026-10-11. Người trả lời được bản ghi gán: **Human**, kiêm khách hàng/người dùng, Sponsor/PO và vận hành theo S1. Chưa có người khác được xác định là đã tham dự; không dựng xung đột giữa các stakeholder giả định.

| Câu | Lời/ lựa chọn của Human trong S2 | Điều được giữ và giới hạn |
| --- | --- | --- |
| R1 | “Chỉ dùng ngôn ngữ tự nhiên.” Bản ghi mô tả lệnh “triển khai story #1111” khi đã có backlog. | Điều khiển qua hội thoại; nhiều trao đổi ở kickoff/grooming. D1/D2 hay phương án lai vẫn là suy luận Architect, chưa phải quyết định kiến trúc. |
| R2 | Chọn **A**. | Theo diễn giải ghi kèm: worker tự do trong worktree riêng; thao tác ngoài worktree qua Crew/gate. Human vắng thì phần phụ thuộc chờ, phần độc lập tiếp tục. Chưa có bằng chứng cưỡng chế. |
| R3 | Chọn **C**, lúc đó được ghi là đặt cả repo private. | Đã được vòng 2 làm rõ; không dùng cách hiểu này để đổi repo Crew sang private. |
| R4 | Chọn **A**. | Theo bản ghi: Crew tự merge vào integration branch; Human duyệt một PR vào main. Không suy thành quyền release/deploy/migration. |
| R5 | Chọn **B**. | Theo bản ghi: Human ra lệnh qua Zed, Crew giữ trạng thái chạy ở run store và là bên ghi lên Issues. Spec/ticket vẫn ở Issues theo Q1; không gộp hai loại dữ liệu. Store/schema/API chưa chốt. |
| R6 | Chọn **A**. | Theo bản ghi: hết quota dừng phần việc liên quan và hỏi Human, không tự đổi provider. Chưa biết tần suất hoặc thời gian chờ. |
| R7 | “Production-ready là mục tiêu của chính Crew: Crew phải hoàn thiện, phủ được mọi kịch bản Human mong muốn, code đạt chuẩn ISO. Target-project không có khái niệm này.” | Giữ mục tiêu Crew và Q3 giao lát dọc đạt chất lượng production. Chưa có ngưỡng chất lượng đã thông qua; M2/M4 là mặc định, không phải DoD được Scrum Team xác nhận. |
| R8 | ““Ngắn” là chỉ hiện những gì Human bắt buộc phải biết. Chỉ áp dụng cho tài liệu HTML Human đọc; tài liệu AI chia sẻ cho nhau thì không quan tâm.” | Phạm vi đã nêu là HTML cho Human. Không đặt ngưỡng chữ/thời gian; M3 mở rộng sang câu hỏi gate trong chat vẫn là mặc định. Chưa kiểm việc Human quyết định đúng khi đọc bản rút gọn. |
| R9 | “Crew chỉ chạy trên máy này.” | Phạm vi ghi kèm: macOS, project local; không cần remote hoặc OS khác. Không cần hỏi lại OS. |

**Kết quả vòng 1:** có đầu vào cho K1–K6; chưa xác nhận giá trị thực nghiệm của H1–H4 hoặc khả năng vận hành. R3 là điểm chưa rõ đã dẫn tới F1, không phải quyết định chưa giải quyết hiện tại. [S2]

## 3. Tổng hợp vòng 2 · F1 và điểm dừng

Ngày theo S2: 2026-10-11; người trả lời được gán: **Human**. Quote trong bản ghi: “Bỏ qua việc chuyển private cho dự án này. Yêu cầu private chỉ áp cho target-project.” [S2 F1]

- **Đã làm rõ:** target-project private; repo Crew giữ public. R3 không còn có nghĩa đổi repo Crew sang private. M10 là mặc định xử lý dữ liệu/injection cho repo public, chưa chứng minh kiểm soát đã chạy.
- **Đề xuất được ghi:** project-onboarding, M11; deferred khỏi Increment 1. Không tạo ticket hoặc chọn thứ tự backlog trong bước này.
- **Rủi ro đã được chấp nhận theo bản ghi:** Human cố ý cấp PAT full quyền. Giữ quyết định này, không đề nghị thu hẹp token. Việc Human chấp nhận credential rộng không tự xác nhận gate có tác dụng, quyền gửi mọi dữ liệu hoặc chấp nhận tất cả A3/A6.
- **Điểm dừng được giữ:** không mở vòng 3. Các câu bỏ qua trở thành mặc định đã ghi hoặc deferred. Không dùng kit bổ sung để buộc trả lời lại.

**Mặc định còn là mặc định:** M2 check/DoD; M3 phạm vi tài liệu đọc; M4 chất lượng target-project; M6 phát hiện issue drift; M7 Actions; M8 provider Orchestrator; M10 kiểm soát dữ liệu. M5 scenario và M9 story để bước phù hợp giải nghĩa; M11 là đề xuất onboarding. Không có bằng chứng chúng đã được triển khai hoặc kiểm chứng. [S2]

## 4. Hiện trạng, pains, ràng buộc và tín hiệu thành công

**Luồng hiện tại được Human kể qua S1:** chọn công việc/step → mở thread → copy System (role) và Request (step) → AI tạo đầu ra → Human đọc, trả lời và chuyển ngữ cảnh → review/duyệt. Luật tài liệu giữ clarification → spec → tickets trong một session và một session sạch cho mỗi ticket. Đã thấy hàm tạo prompt/cấu trúc workflow trong S7; chưa quan sát các thao tác này hoặc đo thời gian.

**Trường hợp lỗi được kể:** S1 E1 gán cho Human sự kiện frontend text-to-speech sao chép giao diện tham chiếu nhưng không khớp backend có sẵn, kết quả sản phẩm thất bại. Đây là lời kể một sự kiện, không có diff, API mapping hay log để xác nhận nguyên nhân. Không nghiên cứu lại các sản phẩm TTS, theo ràng buộc S1.

| Pain | Nguồn / loại | Tần suất | Tác động biết được / còn thiếu |
| --- | --- | --- | --- |
| Mở thread và chuyển prompt bằng tay | Human qua S1 F2; cấu trúc prompt khớp S7 | Quy trình mô tả lặp theo node; chưa có số lần trong kỳ đo | Có thao tác tay; chưa đo số phút hoặc tỷ trọng tổng công. |
| Code lệch kiến trúc, khó bảo trì | Human qua S1 §2 | Chưa biết | Đánh giá của Human; chưa có mẫu code/rework để xác định mức tác động. |
| Frontend không khớp backend | Human qua S1 E1 | Một sự kiện được kể; không suy tỷ lệ lỗi | Human báo sản phẩm thất bại; chưa xác minh công sửa, chi phí hoặc nguyên nhân. |
| HTML lan man | Human qua S1 §2, S2 R8 | Chưa biết | Nhu cầu chỉ thấy thông tin bắt buộc; chưa quan sát mất thời gian hoặc quyết định sai. |

**Ràng buộc được ghi:** ba provider qua ACP; GitHub Issues; Rust/GPUI trong fork Zed; crate Crew riêng và móc mỏng để sync upstream; chất lượng theo ISO/IEC 25010:2023; một Human; macOS/máy này/project local; target-project private và Crew public; không tự đổi provider khi quota cạn. Gói provider/chi phí trong S1 C7 là khai báo trước, không phải giá/quota đã kiểm lại. Chưa có deadline hoặc Sprint Goal được cung cấp. [S1 C1–C7; S2]

**Tín hiệu thành công:** R1/R7/R8 nêu trạng thái Human mong muốn, chưa nêu outcome đã đạt. Đề xuất đo công chuyển việc + đọc/duyệt + rework, cùng lỗi lọt review và khả năng sử dụng đầu ra. M2 đề xuất test/clippy, resume đúng step và lỗi hiển thị; ngưỡng, phương pháp và DoD còn cần xử lý theo scope ở refinement, không chốt tại discovery. [S2 M2; S3 H1–H4]

## 5. Cập nhật giả định theo bằng chứng

ID **A1–A12 thuộc discovery kit S3**, không đồng nhất A1/A2 trong intake. `Confirmed`/`refuted` áp cho đúng mệnh đề và phạm vi kiểm; câu trả lời về mong muốn không chứng minh hiệu quả. Không có bằng chứng mới từ một buổi khách hàng trong phiên này.

| ID / mức trong kit | Trạng thái sau vòng 2 | Bằng chứng và giới hạn | Bổ sung cần thiết / người đề xuất |
| --- | --- | --- | --- |
| A1 · high-risk | **Open** | S1 xác nhận nhu cầu qua bản ghi; R1/R8 nêu ưu tiên. Chưa có baseline hay dùng Crew để chứng minh tiết kiệm đủ lớn. | E1: một việc gần nhất + tổng công thực tế; Human với góc nhìn người dùng, BA/UX. |
| A2 · high-risk | **Partially supported** | S1 E1 phù hợp giả thuyết lệch capability; chỉ một lời kể, chưa xác nhận nguyên nhân hoặc tính lặp lại. | E2: yêu cầu/diff/API mapping và phản ví dụ làm tốt; Human/Developer → BA/Architect. |
| A3 · high-risk | **Open** | R2/R4 là ranh giới mong muốn; S5/S7 nêu bypass và quyền rộng. PAT accepted không phải enforcement evidence. | E3: kiểm hành động ngoài worktree/gate trên quyền thật; Architect/Developer, phối hợp người vận hành. |
| A4 · high-risk | **Partially supported** | S5/S6 báo load/resume lịch sử sau restart; không kiểm restart sau ghi issue/PR nhưng trước khi ghi result. | E4: timeline trước/sau side effect và kết quả đối soát; Architect/Developer. |
| A5 · high-risk | **Partially supported; một cách chèn role bị refuted** | S6: Codex 2.2.2 trả BANANA; Antigravity 1.3.0 trả OK dù role yêu cầu BANANA; Claude 0.89.0 lỗi auth. Cách chèn `_meta` không đạt ở mẫu Antigravity; chưa bác bỏ mọi fallback hay mọi capability của provider. | E5: role/fallback qua compaction, prompt Claude, cancel đang chạy, permission thật; Architect/Developer. |
| A6 · high-risk | **Open** | F1 chỉ chốt visibility repo. Chưa có danh mục dữ liệu, quyền gửi/lưu, retention hoặc điều khoản đúng account. Không suy applicability pháp lý từ ngôn ngữ/timezone. | E6: danh mục và hồ sơ đã che; Human/người giữ dữ liệu, specialist nếu phù hợp. |
| A7 · high-risk | **Open** | S1 C7 có gói khai báo, R6 chốt xử lý quota; chưa có usage/chi phí/tải thực. | E7: usage và thời gian chờ của các lần thật; Human/vận hành → BA/Architect. |
| A8 · high-risk | **Open** | R8 xác nhận sở thích HTML, không xác nhận duyệt đúng với thông tin rút gọn. | E1: Human chỉ phần thực sự đọc và căn cứ quyết định trên artifact thật; BA/UX. |
| A9 · khác | **Open** | M6 là mặc định báo issue drift; chưa có bằng chứng approval/check mất hiệu lực đúng lúc. | E4/E8: lịch sử đổi issue/code/config và check chạy lại; Developer/QA/Architect. |
| A10 · khác | **Partially supported** | S1/S2 phân biệt Human, Crew, target-project. Session/thread/run/cycle/step/story chưa có ví dụ đủ để xác nhận quan hệ. | Dùng cách gọi trong việc thật; BA + Human tại spec nếu ảnh hưởng hành vi. |
| A11 · khác | **Open** | C6 chốt hướng tổ chức code; S5 ước lượng điểm móc và churn. Không có chi phí sync thực tế của Crew. | E9: một lần sync và công sửa/check; người bảo trì → Architect. |
| A12 · khác | **Partially supported** | Q3 cho phép lát dọc, R9 giới hạn môi trường và M11 deferred; phạm vi mọi scenario chưa kiểm chứng. | E8: đối chiếu catalog với việc thật tại shaping/onboarding; Human/PO. |

**P1/H1–H4:** P1 partially supported bởi pains được kể, quan hệ nguyên nhân còn open; H1 tiết kiệm nhờ điều phối open; H2 kiểm capability giảm lỗi partially supported bởi một sự kiện kể, hiệu quả kiểm vẫn open; H3 HTML rút gọn duyệt đúng open; H4 ranh giới/đối soát giữ quyền kiểm soát open. Không có outcome được confirmed hoặc toàn bộ giả thuyết bị refuted. [S1–S6]

**Risk ownership:** các recipient trong bảng chỉ là đề xuất, chưa ai nhận kiểm chứng hoặc chấp nhận rủi ro A1–A12. Riêng rủi ro PAT rộng có Human là người đã chấp nhận **theo S2**; không suy mở rộng acceptance này. Vì vậy không thể đóng high-risk assumptions bằng cách tự gán owner.

## 6. Thuật ngữ

Đã ghi [GLOSSARY.md](../../GLOSSARY.md) cho **Human**, **Crew**, **target-project**, dựa trên S1 stakeholder map và S2 R7/F1. Đây là những nghĩa đã thể hiện rõ trong lựa chọn được ghi, không phải định nghĩa mới được Human phê duyệt trong phiên này. Glossary chỉ có từ vựng; nguồn/rationale nằm ở synthesis.

| Thuật ngữ nháp trong S4 | Kết quả đối chiếu |
| --- | --- |
| Human / Crew / target-project | Giữ, nhưng sửa điểm mơ hồ: Human không đồng nhất mọi end user; Crew khác dự án đích. Quyền/visibility là quyết định, không đưa vào định nghĩa. |
| Session / thread / run / cycle / step / story | **Open.** Không gộp session với thread, không suy story = issue hay một Increment. M9 vẫn deferred tới spec. |
| Gate / permission / review / verification / approval | **Partially supported.** R2/R4 yêu cầu quyết định Human khác quyền tool; chưa đủ ví dụ để xác nhận mọi định nghĩa nháp. Không coi reviewer AI pass là Human duyệt. |
| Production-ready / DoD | Sửa cách hiểu: mục tiêu chất lượng Crew theo R7; không gán chất lượng Crew cho mọi target-project. M2/M4 chưa thành DoD đã thông qua. |
| Scenario / playbook và các thuật ngữ nháp khác | Giữ hypothesis ở primer, chưa chuyển vào glossary chính thức. |

Không có quyết định kiến trúc mới đã chọn, nên không tạo ADR.

## 7. Mâu thuẫn, người có quyền quyết định và cập nhật landscape

| ID | Xung đột / lệch bằng chứng | Trạng thái và route |
| --- | --- | --- |
| C1 | R3 private cả repo so với F1 Crew public | **Resolved theo S2 F1**, Human là người quyết định. Không xin xác nhận lại. |
| C2 | Landscape còn hỏi R1/R2/R5/OS và đề nghị PAT hẹp | **Superseded bởi S2.** Đã thêm cập nhật vào landscape; giữ probe cũ theo ngày/version. Kiến trúc vẫn chưa chọn. |
| C3 | R2 giới hạn worker trong worktree so với credential rộng/bypass nêu ở S5/S7 | **Open về hiệu lực kiểm soát**, chưa quan sát hành vi vi phạm. Architect/Developer đề xuất nhận kiểm chứng; nếu không cưỡng chế được, Human quyết định trade-off. Không hỏi lại scope PAT. |
| C4 | R8 chỉ HTML so với M3 thêm câu hỏi gate trong chat | **Default khác phạm vi lời trả lời**, không coi là lựa chọn mới. Human/PO có quyền sửa mặc định khi ảnh hưởng spec; chưa chặn tổng hợp discovery. |
| C5 | Intake mô tả brainstorm 4 bước; `workflows` hiện có 5 mục gồm specification | **Lệch tài liệu đã kiểm trực tiếp S7.** Đề xuất người bảo trì SDLC làm rõ cách đếm; chưa suy là lỗi runtime hoặc sửa workflow ngoài scope. |
| C6 | S2 gọi K5 đủ dữ kiện + mặc định; S3 và bước hiện tại yêu cầu bằng chứng high-risk | **Hai tiêu chí khác nhau.** Đủ lựa chọn để playback không đồng nghĩa hoàn tất kiểm chứng. Không tạo lại gate D0 từ kit, không tự coi high-risk accepted. |

Không có bằng chứng xung đột giữa các người trả lời, hoặc khác biệt giữa lời nói và hành vi Human đã được quan sát trực tiếp.

**Landscape revision:** chưa có hệ thống mới được khách hàng tiết lộ ngoài bộ ba ACP, GitHub, Git/worktree, fork/upstream và trình duyệt đã kiểm kê. Bổ sung lựa chọn R1–R9/F1, phân biệt Issues giữ spec với run store giữ trạng thái; không chọn SQLite/schema; giữ hạn chế probe role/auth/cancel. Đã cập nhật [technical landscape](../research/technical-landscape.html) bằng phần ghi ngày 11/10, không chạy lại probe hoặc kiểm điều khoản.

## 8. Follow-up có mục tiêu và bàn giao trong cùng session

**Không gửi questionnaire, mở interview hoặc yêu cầu Human trả lời vòng mới.** Bảng là danh sách bổ sung bằng chứng khi cần, không phải điều kiện duyệt tài liệu bắt buộc. Các câu product đã chốt không hỏi lại; accessible facts/probes do Developers/Architect xử lý.

| Artifact / khoảng trống | Câu follow-up hoặc kiểm cần trả lại | Recipient / owner đề xuất | Trạng thái / next action |
| --- | --- | --- | --- |
| E1/E2, A1/A2/A8 | Trong một việc gần nhất, đã copy/chuyển/đọc/sửa những gì; lỗi nhận ra bằng gì? Có một việc tương tự làm tốt và artifact nào được phép đối chiếu? | Human người dùng + BA/UX | Chưa quan sát; chỉ chọn nếu có mẫu hoặc cần bác bỏ P1. Trả notes và nguồn, không ước tính giả. |
| E3, A3 | Khi worker có quyền thật, hành động ngoài worktree bị chặn/đi qua gate bằng chứng nào? | Architect/Developer + người vận hành | Chưa kiểm; xác định phép thử theo quyền đã chấp nhận. Trả kết quả và giới hạn, không yêu cầu đổi PAT. |
| E4/E8, A4/A9 | Restart sau side effect trước result có gây ghi/PR lặp? Đổi baseline/config/issue làm check/approval nào hết hiệu lực? | Architect/Developer/QA | Chưa chạy; trả timeline/version và hành động đối soát thực tế. |
| E5, A5 | Role có được giữ ở từng adapter qua fallback/compaction? Cancel đang chạy và permission thật diễn ra ra sao? | Architect/Developer; Human hỗ trợ auth nếu cần | Probe cũ có giới hạn; chưa kiểm auth hiện tại. Trả evidence version-pinned; không chạy probe trong bước này. |
| E6, A6 | Những loại dữ liệu thực nào được phép gửi/lưu/publish, theo hồ sơ nào, retention nào? | Human/người giữ hồ sơ; chuyên gia nếu applicability đòi hỏi | Chưa có hồ sơ; xin bản tóm tắt đã che ở nơi được phép khi công việc đó cần. Không kết luận tuân thủ. |
| E7, A7 | Các lần thật đã dùng bao nhiêu quota/chi phí và chờ bao lâu? | Human/vận hành → BA/Architect | Chưa có usage; không hỏi lại quyết định dừng/hỏi Human của R6. |
| A10/A11/A12 và C5 | Từ vựng trong việc thật, công sync và catalog nào không khớp? | BA/người bảo trì/Human với góc nhìn PO | Deferred theo phạm vi; glossary/refinement/onboarding hoặc bảo trì tài liệu, không xếp backlog ở đây. |

**Next route đề xuất:** playback problem brief từ S1/S2 và synthesis này, nêu riêng bằng chứng, giả thuyết, rủi ro chưa được nhận và mặc định. Không gọi bước discovery này complete chỉ vì bộ câu hỏi đã dừng. Điều kiện đạt completion về bằng chứng: từng A1–A8 có outcome đủ để confirmed/refuted theo scope, hoặc người có thẩm quyền thực sự chấp nhận rủi ro còn lại và owner nhận trách nhiệm. Chưa có các xác nhận đó. Không tự chọn kiến trúc, viết spec, tạo ticket hay triển khai.

## 9. Kiểm chứng phiên này

Đã đọc và đối chiếu S1–S7, truy vấn Issues, kiểm nội dung transcript TD1 và danh sách workflow, ghi thuật ngữ đã rõ, tổng hợp riêng hai vòng và bổ sung landscape. Kiểm tĩnh tài liệu: link local, cấu trúc bảng, đủ A1–A12 và HTML full-width. Không phỏng vấn người dùng, đo outcome, chạy sản phẩm/probe mới, xác minh credential hiện tại, kiểm usability hoặc gửi thông tin ra bên ngoài. Kết quả kiểm tĩnh không xác nhận hiệu quả sản phẩm hay stakeholder agreement.
