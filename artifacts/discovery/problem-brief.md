# Playback · Problem brief & hiện trạng Zed AI Crew

**Complete · Human xác nhận 2026-10-11 · Business Analyst.** [Bản xem cho Human](problem-brief.html). Human trả lời “đồng ý” trong hội thoại sau ba câu xác nhận playback: vấn đề/hiện trạng, người hưởng lợi/outcome và ranh giới. Không có sửa đổi được yêu cầu. Hoàn tất bước playback; baseline, target, giả thuyết và các quyết định còn mở giữ nguyên trạng thái. Các lựa chọn đã ghi ở intake/discovery được giữ nguyên; không mở lại discovery hay thêm bước duyệt kit.

## Readout để trình bày

Human đang tự điều phối công việc phát triển phần mềm qua AI: chọn step, mở thread, dán prompt, đọc đầu ra và chuyển ngữ cảnh. Human báo code khó bảo trì và tài liệu HTML lan man; một dự án frontend cho backend sẵn có đã thất bại vì đầu ra không khớp hệ thống, theo lời kể được ghi lại. Nhu cầu là giảm công điều phối và đọc/duyệt, đồng thời nhận thay đổi dùng được, phù hợp kiến trúc và vẫn giữ quyền quyết định.

Crew được kỳ vọng hỗ trợ Human bằng hội thoại trong Zed: nhận yêu cầu, làm rõ và đưa các quyết định cần Human. Lát đầu được discovery ghi là **Build theo ticket**, chạy trên chính repo Crew, trên máy macOS này. Crew giao theo lát dọc đạt chất lượng production; mục tiêu dài hạn là phủ các kịch bản Human mong muốn. Chưa có baseline, ngưỡng thành công hoặc bằng chứng tự động hóa sẽ giảm tổng công. Playback xác nhận cách hiểu vấn đề và ranh giới; kiến trúc thuộc bước shaping. [S1 Q3/§2; S2 R1/R7–R9, M11; S3 §4–5]

## Người hưởng lợi và tác nhân

| Ai | Vai trò đã biết / giới hạn |
| --- | --- |
| **Human** | Người hưởng lợi trực tiếp; kiêm khách hàng/người dùng duy nhất, Sponsor, PO và vận hành theo stakeholder map. Có quyền quyết định sản phẩm; không đồng nhất mọi người dùng của target-project. |
| Người dùng của **target-project** | Có thể hưởng lợi gián tiếp từ thay đổi dùng được; chưa xác định danh tính hoặc nhu cầu, không coi họ đã tham gia discovery. |
| Orchestrator / worker AI | Tác nhân thực thi dự kiến, không thay quyền quyết định Human hoặc trách nhiệm của Scrum Team. |
| GitHub, ba provider ACP, upstream Zed | Phụ thuộc về tracker, thực thi và bảo trì; chưa có đại diện nào tham gia xác nhận playback. |

Nguồn: [S1 stakeholder map; S4 §2; S6]. Các góc nhìn PM/Sponsor/Tech Lead trong intake do Human nắm; không giả lập thêm người đã đồng thuận.

## Hiện trạng và pains có bằng chứng

**Luồng được kể:** chọn việc/step → mở thread → copy System (role) + Request (step) → AI tạo đầu ra → Human đọc, trả lời, chuyển ngữ cảnh → review/duyệt. Clarification → spec → tickets giữ cùng context khi phù hợp; mỗi ticket triển khai dùng session sạch. Đã đối chiếu hàm tạo prompt trong SDLC, chưa quan sát Human thao tác trực tiếp. [S1 F2/F4; S3 §4; S7]

| Pain | Bằng chứng | Điều chưa biết |
| --- | --- | --- |
| Chuyển prompt/ngữ cảnh bằng tay | Human được ghi nhận trong intake; SDLC có hai trường prompt để copy. | Thời gian và số lần trên một việc; tỷ trọng trong tổng công. |
| Code lệch kiến trúc, khó bảo trì | Đánh giá của Human trong intake. | Mẫu diff, tần suất, công sửa; chưa xác nhận nguyên nhân. |
| Frontend không khớp backend | Một sự kiện TTS do Human kể, intake E1; sản phẩm được báo là thất bại. | Không có API mapping/diff/log để xác minh nguyên nhân hoặc suy tỷ lệ lỗi. |
| HTML lan man | Intake §2 và R8: chỉ hiện những gì Human bắt buộc biết. | Thời gian đọc, thông tin bị bỏ qua và độ đúng của quyết định. |

**Diễn giải BA, chưa phải fact:** công điều phối, đọc/duyệt và rework có thể đang làm giảm giá trị của AI; điều phối tự động là giả thuyết giải pháp, chưa chứng minh xử lý nguyên nhân của E1. [S3 A1/A2/A8; S5 P1/H1–H4]

## Kết quả mong muốn và tín hiệu thành công

**Đã ghi:** Human điều khiển bằng ngôn ngữ tự nhiên, trả lời clarification và duyệt; Crew có code bảo trì được, đầu ra dùng được và HTML chỉ chứa thông tin bắt buộc. “Production-ready” là mục tiêu của Crew theo R7. M2/M4 là mặc định, chưa phải Definition of Done đã được Scrum Team xác nhận. [S1 §2/Q3; S2 R1/R7/R8; S3 §6]

| Tín hiệu đề xuất, chưa chốt KPI | Baseline | Target |
| --- | --- | --- |
| Công Human trên một việc tương đương: điều phối + đọc/duyệt + sửa lại | Chưa đo | Giảm; mức giảm chưa chốt. |
| Đầu ra dùng được, phù hợp hệ thống/kiến trúc; lỗi lọt review và rework | Chỉ có lời kể E1; chưa có tỷ lệ | Cải thiện; tiêu chí và ngưỡng chưa chốt. |
| Human đủ thông tin để quyết định đúng từ HTML | Chưa quan sát | Chỉ hiện điều bắt buộc theo R8; chưa có ngưỡng định lượng. |
| Phục hồi đúng công việc, không lặp thao tác đã xảy ra | Probe cũ chỉ hỗ trợ một phần resume lịch sử; chưa có Crew end-to-end | Kiểm được theo scope; M2 mới là mặc định. |

Đo cùng scope và chất lượng; số ticket đóng hoặc số role không tự chứng minh giá trị. Chưa có deadline hoặc Sprint Goal được cung cấp. Không đặt một ngưỡng thời gian đọc thay cho R8. [S3 §4–5; S4 §2; S5]

## Ràng buộc và phạm vi

**Giữ theo bản ghi:**

- Một Human, máy macOS này, project local; Rust/GPUI trong fork Zed, crate Crew riêng và móc mỏng để sync upstream. Claude, Codex, Antigravity qua ACP; GitHub Issues giữ spec/ticket, run store giữ trạng thái thực thi. [S1 C1–C6; S2 R5/R9; S3 §2]
- Worker tự do trong worktree riêng; thao tác ngoài worktree qua Crew/gate. Human vắng: phần phụ thuộc quyết định chờ, phần độc lập tiếp tục. Crew merge vào integration branch; Human duyệt một PR vào main. Hết quota dừng phần việc liên quan và hỏi Human, không tự đổi provider. [S2 R2/R4/R6]
- Crew public; target-project private theo F1. Human cố ý cấp PAT full quyền và chấp nhận rủi ro đó theo bản ghi; không suy thành quyền gửi mọi dữ liệu hoặc bằng chứng gate đã hiệu lực. [S2 F1/PAT; S3 §3/§7]
- ISO/IEC 25010:2023 là mô hình chất lượng đã chọn. Chưa có xác nhận tuân thủ/chứng nhận; bộ check M2 và chất lượng target-project M4 vẫn là mặc định. Các mục license, điều khoản provider, dữ liệu và luật trong primer §8 là hồ sơ cần xác định applicability, chưa có kết luận pháp lý. [S1 C3; S2 M2/M4; S4 §8]

**Phạm vi đầu đã ghi:** Build theo ticket trên repo Crew, nhận lệnh qua Zed, thực hiện/kiểm chứng/tích hợp công việc và đưa PR để Human duyệt. Đây là phạm vi để shaping làm rõ, chưa phải spec hay cam kết release. [S1 Q3; S2 mục tiêu/M11]

**Loại trừ:** đa người dùng, remote/OS khác; chuyển repo Crew sang private; tự đổi provider; big-bang mới bắt đầu sử dụng. Project-onboarding và danh sách toàn bộ scenario deferred khỏi lát đầu. Không nghiên cứu lại sản phẩm TTS/Claude Design clone đã được loại trừ. Merge/PR không tự cấp quyền release, deploy hoặc migration. [S1 Q3/C4/E1; S2 R6/R9/F1/M5/M11; S3 §2–3]

**Giới hạn bước này:** không chọn engine/LLM, database/schema, UI chi tiết, DoD, backlog ordering hoặc triển khai.

## Rủi ro và quyết định còn mở

Tất cả người xử lý bên dưới là **đề xuất, chưa nhận trách nhiệm**. A1–A12 dùng ID của discovery kit và trạng thái cập nhật ở synthesis §5.

| Nhóm / unknown | Quyết định bị chặn | Người đề xuất xử lý / hành động tiếp |
| --- | --- | --- |
| **Value · A1/A2:** chưa có tổng công/rework hay nguyên nhân E1 được xác minh | Chưa thể kết luận tự động hóa tạo lợi ích hoặc xử lý đúng nguyên nhân. Không chặn xác nhận vấn đề dưới dạng nhu cầu được kể. | Human với PM/BA: dùng việc thật để đối chiếu lợi ích khi đánh giá scope. |
| **Usability · A8/A10:** chưa biết HTML rút gọn có đủ cho quyết định; session/thread/run/story còn mơ hồ | Chặn chốt rubric đọc/duyệt và hành vi nếu các thuật ngữ ảnh hưởng spec. | Human với BA/Designer: đối chiếu một artifact/tình huống cụ thể tại shaping/spec. |
| **Feasibility · A3/A4/A5/A9:** boundary, restart sau thao tác ghi và tương đương capability ba adapter chưa kiểm đầy đủ | Chặn khẳng định gate/recovery đáng tin và chọn phương án dựa trên capability chưa có bằng chứng. | Architect/Developers: xác định phép kiểm cho phương án shaping; không yêu cầu Human đo thay. |
| **Viability · A6:** quyền gửi/lưu dữ liệu, retention, điều khoản tài khoản và luật áp dụng chưa rõ | Chặn kết luận dữ liệu nhạy cảm được gửi/publish và kết luận tuân thủ; không chặn draft playback. | Human/người giữ dữ liệu với chuyên gia phù hợp nếu cần: xác định hồ sơ cho đúng scope. |
| **Viability · A7/A11:** chưa đo quota, chi phí và công sync | Chặn kết luận mức song song/chi phí vận hành và bảo trì bền vững. | Human/vận hành với Architect: đối chiếu usage và công sync khi cân nhắc phương án. |
| **Scope · A12/M5/M11:** “mọi kịch bản” và tracker target-project chưa chốt đầy đủ | Chặn tuyên bố phủ 100% hoặc đặc tả onboarding; không chặn lát đầu trên repo Crew. | Human/PO với BA: xác định khi mở rộng scope; PO giữ quyền ordering. |
| **Chất lượng · M2/M4:** chưa có ngưỡng và DoD được xác nhận | Chặn tuyên bố một Increment đã đạt chất lượng production; không chặn nêu mục tiêu Crew. | Human với Developers/QA: cụ thể hóa theo scope tại refinement/spec. |

Trade-off cần giữ: ít thao tác/gate nhưng vẫn đủ thông tin và quyền kiểm soát; ba provider nhưng capability khác nhau; ít điểm móc upstream nhưng vẫn phải đo công bảo trì. Chưa chọn phương án xử lý. [S3 §5/§7; S4 §7; S8 cập nhật 11/10]

## Từ vựng và xác nhận playback

Giữ nghĩa đã ghi trong [GLOSSARY.md](../../GLOSSARY.md): **Human** là người dùng/người quyết định trong engagement; **Crew** là công cụ hỗ trợ điều phối; **target-project** là dự án đích, khác dự án xây chính Crew. Nguồn từ vựng trước được synthesis §6 ghi lại; Human đã xác nhận cách dùng trong brief qua câu trả lời “đồng ý”. Không bổ sung các nghĩa nháp session/thread/run/cycle/step/story vào glossary, không tạo ADR khi chưa có lựa chọn và trade-off thực tế.

Bằng chứng xác nhận: Human trả lời “đồng ý” ngày 2026-10-11 sau bản playback và ba câu dưới đây. Vấn đề, người hưởng lợi, outcome và constraints đã được xác nhận, không có correction cần áp dụng. Các câu hỏi được giữ làm bản ghi; không còn yêu cầu trả lời. Xác nhận brief không chuyển các unknown thành bằng chứng đã kiểm chứng:

1. **Vấn đề và hiện trạng:** ba pain chính — chuyển prompt thủ công, đầu ra khó bảo trì/không khớp hệ thống, HTML lan man — cùng chuỗi hiện tại ở trên đã phản ánh đúng chưa? Có câu nào cần sửa?
2. **Người hưởng lợi và outcome:** Human là người hưởng lợi trực tiếp; kết quả cần là giảm tổng công nhưng vẫn có thay đổi dùng được và giữ quyền quyết định. Cách phát biểu này có đúng nhu cầu không?
3. **Ranh giới:** bản playback có phản ánh đúng các lựa chọn đã ghi về máy local, ba provider, Issues/run store, quyền worker/merge/quota, Crew public/target-project private và lát đầu Build theo ticket không? Chỉ ra điểm diễn giải sai nếu có; baseline/target chưa biết được giữ mở.

## Bằng chứng kiểm và route tiếp

Đã đọc S1–S8, glossary, quy ước domain/tracker; đối chiếu cấu trúc prompt/workflow trong SDLC, cấu hình provider và bảng permission trong tài liệu fork. SDLC hiện có 5 mục brainstorm gồm specification, khác cách đếm 4 trong intake; không suy thành lỗi runtime. Đã truy vấn `gh issue list -R Riso-application-factory/zed-ai-crew --state all --limit 100 --json number,title,url,body`, kết quả `[]`; chưa có issue chuẩn để liên kết. Đây là bản ghi playback, không thay GitHub Issues làm nguồn spec/ticket. Snapshot: `1622a4d08cbd95a57a47b3c8ae3b1304b9187493`.

Không có interview/quan sát sử dụng/probe ACP/benchmark mới trong phiên này. Kết quả probe và nghiên cứu web là bằng chứng kế thừa có ngày/phạm vi; chưa kiểm lại các claim web. Đã có xác nhận playback của Human như ghi ở trên; chưa có owner acceptance hoặc chọn kiến trúc mới.

**Tiếp trong cùng context:** playback đã complete theo xác nhận Human, đủ đầu vào để chuyển sang **solution shaping**, dùng brief đã xác nhận + domain primer + technical landscape. Shaping chưa được thực hiện trong bước playback. PM/Sponsor tiếp nhận unknown giá trị/phạm vi; Architect tiếp nhận giới hạn capability, enforcement/recovery. Đây là recipient/next action đề xuất, chưa có trao đổi bên ngoài; không tạo portable handoff vì chưa đổi người, tool hoặc repo.

### Nguồn

- **S1:** [Intake, gồm stakeholder map, Q1–Q3 và E1](../intake-brief.html).
- **S2:** [Bản ghi discovery R1–R9/F1 và mặc định M1–M11](questions.html).
- **S3:** [Synthesis, gồm assumption status hiện hành §5](synthesis.md).
- **S4:** [Domain primer](../research/domain-primer.md); nghĩa nháp §9 đã được synthesis cập nhật.
- **S5:** [Discovery kit](discovery-kit.md); hypotheses/guide đề xuất, không phải nghiên cứu đã chạy hay gate mới.
- **S6:** [Glossary đã ghi](../../GLOSSARY.md).
- **S7:** [SDLC và prompt/workflow](../sdlc.html).
- **S8:** [Technical landscape](../research/technical-landscape.html); dùng cập nhật 11/10 thay các câu hỏi/lựa chọn baseline đã bị superseded.
