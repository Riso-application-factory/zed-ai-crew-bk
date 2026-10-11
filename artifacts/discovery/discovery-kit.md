# Discovery kit · Zed AI Crew

**Draft · awaiting decision: chờ Human duyệt hoặc sửa kit trước khi dùng.** Business Analyst · 2026-10-11. [Bản xem HTML](discovery-kit.html). Đây là tài liệu chuẩn bị nghiên cứu, không phải spec, backlog mới hay quyết định kiến trúc. Không triển khai, chạy prototype hoặc gửi lời mời phỏng vấn trong bước này.

**Điều cần duyệt:** giữ R1–R9/F1, không mở vòng 3; dùng các guide dưới đây như thư viện chọn câu theo khoảng trống bằng chứng, không như một vòng hỏi bắt buộc. Human hiện kiêm các góc nhìn nên chỉ cần một phiên được chọn lọc nếu có nhu cầu bổ sung bằng chứng. Chưa có bằng chứng Human đã duyệt kit này.

## 1. Baseline, nguồn và độ tin cậy

| Mã | Nguồn đã kiểm trong phiên này | Loại bằng chứng và giới hạn |
| --- | --- | --- |
| S1 | [Intake brief](../intake-brief.html), Q1–Q3, F1–F11, stakeholder map, C1–C7, E1 | Bản ghi yêu cầu và quyết định. Tin cậy cao về nội dung file; E1 là Human kể lại, chưa quan sát sự kiện. |
| S2 | [Domain primer](../research/domain-primer.md), §2–10 | Tổng hợp nguồn sơ cấp trước đó, journey tài liệu và glossary giả thuyết. Tin cậy cao về nội dung tài liệu; hiệu quả tại Crew chưa xác nhận. |
| S3 | [Technical landscape](../research/technical-landscape.html), §3–6 | Đọc code và báo cáo probe ngày 10/10. Mức khả thi là đánh giá Architect, không chứng minh Crew hoạt động. Các câu R1/R2/R5/R9 còn mở trong báo cáo này đã có bản ghi mới hơn ở S4. |
| S4 | [Bản ghi discovery R1–R9, F1, M1–M11](questions.html) ngày 11/10 | Nguồn ghi nhận câu trả lời hiện có. Giữ các lựa chọn Human đã được ghi; không tuyên bố đã tham dự interview hay xác minh transcript khách hàng. Mặc định và suy luận trong cột hệ quả không tự thành lựa chọn Human. |
| S5 | [Phương pháp và phạm vi probe TD1](../evidence/technical-discovery/td1/README.md); [Claude prompt](../evidence/technical-discovery/td1/claude-2-prompt.jsonl), [Codex prompt](../evidence/technical-discovery/td1/codex-2-prompt.jsonl), [Antigravity prompt](../evidence/technical-discovery/td1/antigravity-2-prompt.jsonl) | Đã đọc transcript cũ: Claude báo Authentication required; Codex trả BANANA; Antigravity trả OK khi probe yêu cầu role trả BANANA. Chưa chạy lại adapter, cancel đang hoạt động, fallback role hoặc quota. Tin cậy giới hạn theo adapter/version và mẫu probe. |
| S6 | [Quy ước tracker](../../docs/agents/issue-tracker.md), [domain docs](../../docs/agents/domain.md) | GitHub Issues giữ spec/ticket; HTML là bản xem. Kit nghiên cứu này có bản Markdown gốc, không tạo một nguồn spec khác. Truy vấn GitHub Issues mọi trạng thái, tối đa 50, trả `[]` trong phiên này; không có issue để dẫn làm spec hiện hành. |

Snapshot repo khi đọc: `a2a15cc6c3ea5b688354ebeffe16eba3e359f9f5`. Nghiên cứu ngành và footprint công khai đã nằm ở S2/S3; dùng lại thay vì hỏi Human những khả năng có thể đọc từ tài liệu. Phiên này đối chiếu các đầu vào và transcript local, không nghiên cứu lại thị trường, không đo hành vi của khách hàng. Không suy giá trị thực tế, điều khoản tài khoản hoặc tuân thủ từ nguồn công khai.

### Những lựa chọn được giữ nguyên

- Q1–Q3: GitHub Issues là tracker; thiết kế cũ là input nghiên cứu; giao từng lát dọc đạt chất lượng production, thay vì đợi đủ toàn bộ scenario. [S1]
- R1: điều khiển bằng ngôn ngữ tự nhiên; khi có backlog chủ yếu gọi triển khai story và trả lời AI. R2: worker làm trong worktree riêng; việc vượt ranh giới qua Crew/gate; Human vắng thì phần đó chờ, phần độc lập tiếp tục. [S4]
- R3/F1: target-project private; Crew giữ public. R4: tự tích hợp vào integration branch, Human duyệt một PR vào main. R5: Human ra lệnh qua Zed, run store giữ trạng thái chạy, Crew ghi lên Issues. R6: hết quota hỏi Human, không tự đổi provider. [S4]
- R7: production-ready là mục tiêu của Crew; R8: HTML chỉ hiện điều Human bắt buộc biết, không có ngưỡng thời gian đã chốt; R9: chỉ máy này, macOS và project local. [S4]
- Bản ghi S4 nêu Human đã chấp nhận PAT full quyền. Không mở lại scope token; hiệu quả cưỡng chế ranh giới worker vẫn cần bằng chứng riêng. [S4, S3]

### Các mặc định còn là mặc định

M2 về phép kiểm/DoD, M3 về phạm vi tài liệu Human đọc, M4 về chất lượng target-project, M6 về phát hiện issue drift, M7 về Actions, M8 về provider Orchestrator, M10 về dữ liệu/injection và M11 về onboarding là mặc định hoặc đề xuất được ghi ở S4. Kit không nâng chúng thành DoD đã được Scrum Team thông qua, kiến trúc đã chọn hay kiểm soát đã triển khai. Câu trả lời R8 chỉ nói HTML; việc M3 mở rộng sang câu hỏi gate trong chat vẫn là mặc định. M5 về danh mục scenario và M9 về nghĩa story còn để bước phù hợp xử lý. [S4]

## 2. Giả thuyết vấn đề và giải pháp — đều chưa xác nhận

**P1 · Giả thuyết vấn đề:** trong quy trình SDLC đang chạy bằng tay, Human phải chuyển prompt/ngữ cảnh và duyệt đầu ra; thiếu đối chiếu capability, ràng buộc kiến trúc và bằng chứng tích hợp có thể làm tăng rework, còn HTML dài che mất quyết định cần Human. Cơ sở là nhu cầu intake và sự kiện text-to-speech E1 do Human kể lại. Chưa biết tần suất, thời gian, chi phí hay liệu nguyên nhân chính là thiếu ngữ cảnh, thiếu kiểm chứng, prompt hoặc mô hình. [S1 E1/F2–F5; S2 §2, §6]

**Giả thuyết đối chứng:** vấn đề chủ yếu do chất lượng yêu cầu hoặc thiếu capability của backend; điều phối tự động chỉ di chuyển thao tác mà không giảm rework. Một workflow có hướng dẫn và đối chiếu capability đầy đủ, vẫn chạy thủ công, có thể đạt kết quả tương đương. Cần giữ khả năng bác bỏ giải pháp đề xuất. [Suy luận BA từ S1 E1; chưa kiểm]

| ID | Giả thuyết giải pháp, chưa chọn | Tín hiệu ủng hộ / bác bỏ cần tìm | Giả định liên quan |
| --- | --- | --- | --- |
| H1 | Điều phối theo playbook, giữ ngữ cảnh và trạng thái chạy, có thể giảm thao tác chuyển việc mà vẫn giữ quyền quyết định Human. | Cùng loại công việc: thao tác và rework giảm mà lỗi lọt review không tăng; bác bỏ nếu chi phí quản lý/gate lớn hơn phần tiết kiệm. | A1, A3, A4, A5, A7 |
| H2 | Kiểm kê capability, đối chiếu ràng buộc và kiểm chứng tích hợp trước khi kết luận có thể giảm lệch backend/kiến trúc. | Mẫu lỗi cũ được phát hiện bằng chứng cụ thể; bác bỏ nếu kiểm này không phát hiện lỗi hoặc nguyên nhân nằm ngoài các ràng buộc đó. | A2, A3, A9 |
| H3 | HTML mở đầu bằng thông tin bắt buộc quyết định, chi tiết theo yêu cầu, có thể giảm công đọc mà không mất thông tin trọng yếu. | Human xác định đúng quyết định, rủi ro và bằng chứng trên artifact thật; bác bỏ nếu phần bị ẩn làm chọn sai hoặc vẫn phải đọc toàn bộ. | A1, A8 |
| H4 | Ranh giới thực thi và đối soát trạng thái có thể giữ quyền kiểm soát qua quota, restart và side effect dở dang. | Không ghi/merge lặp, không tái dùng approval cho baseline đã đổi; bác bỏ nếu ngoài-worktree vẫn bị thực hiện trái gate hoặc trạng thái không đối soát được. | A3, A4, A5, A6, A9 |

H1–H4 là suy luận BA từ S1–S5. Không dùng bảng này để giới thiệu hay bán giải pháp trong interview; chỉ hỏi sự kiện, hành vi và bằng chứng. Việc chọn D1/D2, store/schema, seam và provider thuộc bước định hình với Developers/Architect.

## 3. Assumption map xếp theo rủi ro

Impact (I): 5 = sai có thể mất dữ liệu/quyền kiểm soát hoặc làm sai vấn đề sản phẩm; 4 = rework lớn/không dùng được luồng; 3 = tác động giới hạn. Uncertainty (U): 5 = chưa có bằng chứng riêng của khách hàng; 4 = chủ yếu suy luận hoặc tài liệu; 3 = có mẫu hẹp; 2 = có câu trả lời và một phần bằng chứng; 1 = nhiều bằng chứng khớp. **Điểm = I × U**, dùng để chọn nghiên cứu, không phải xác suất hay thứ tự backlog. Xếp hạng là đề xuất BA. **Điểm từ 16 là high-risk**; mọi dòng đều có phép kiểm hoặc yêu cầu bằng chứng.

| Hạng / ID | Loại | Giả định chưa kiểm chứng về kết quả | Cơ sở / giới hạn | I × U | Phép kiểm đề xuất; câu/bằng chứng |
| --- | --- | --- | --- | --- | --- |
| 1 · A1 | Value | Giảm thao tác và công đọc là phần tiết kiệm đủ lớn để Human tiếp tục dùng Crew. | S1 pain, S4 R1/R8; chưa có baseline hành vi. | 5 × 5 = 25 | Observation/interview trên một việc gần nhất và một việc tốt; G1.2–G1.5, E1/E2. Bác bỏ nếu tiết kiệm bị chi phí duyệt/rework lấn át. |
| 2 · A2 | Value | Lệch capability/kiến trúc là nguyên nhân lặp lại đủ đáng kể, không chỉ một sự kiện E1. | S1 E1 là kể lại, chưa có diff/API mapping. | 5 × 5 = 25 | Interview + document request; G1.4, G2.4, G3.4, E2. Phân loại nguyên nhân, tìm cả phản ví dụ. |
| 3 · A3 | Viability | Quyền thực thi thực tế cho phép giữ ranh giới R2/R4 và hạn chế lộ dữ liệu theo M10 dù credential rộng. | S3 thấy khả năng bypass/env; S4 ghi accepted risk, không có enforcement evidence. | 5 × 4 = 20 | Đọc bằng chứng kiểm soát, observation bởi người vận hành; G4.3–G4.5, E3. Chấp nhận rủi ro token không chứng minh gate có tác dụng. |
| 4 · A4 | Feasibility | Resume lịch sử ACP đủ làm nền cho phục hồi luồng mà không lặp side effect. | S5 chỉ probe session load; chưa có Crew run đầu cuối. | 5 × 4 = 20 | Data request về lần gián đoạn và đối soát; G3.3–G3.4, G4.4, E4. Architect đề xuất phép thử restart sau side effect trước khi ghi result; chưa chạy. |
| 5 · A5 | Feasibility | Ba provider giữ được role và semantics cần thiết trong suốt một phần việc. | S3/S5: Claude prompt bị chặn, Antigravity role không đạt mẫu; cancel đang chạy và fallback còn mở. | 5 × 4 = 20 | E5: Architect bổ sung probe version-pinned; G3.5 chỉ hỏi lịch sử vận hành, không yêu cầu Human đo capability thay Developers. |
| 6 · A6 | Viability | Loại dữ liệu thật, điều khoản tài khoản và cách lưu/gửi cho phép thực hiện workflow. | S2 U5/U6; private repo không xác định retention hay quyền gửi provider. | 5 × 4 = 20 | Document request/questionnaire; G4.2/G4.5, N4–N5, E6. Không suy jurisdiction từ tiếng Việt/timezone. |
| 7 · A7 | Viability | Các gói provider hiện dùng chịu được tải và tổng chi phí vận hành hợp lý. | S1 C7 là gói khai báo; S3 chưa đo quota; S4 R6 đã chốt cách xử lý. | 4 × 4 = 16 | Data request lịch sử usage/chi phí, không hỏi lại provider failover; G2.5, G4.4, N6, E7. |
| 8 · A8 | Usability | Chỉ hiện thông tin bắt buộc trong HTML vẫn đủ để Human duyệt chính xác. | S4 R8 là ý muốn, chưa là outcome dùng thực tế. | 4 × 4 = 16 | Observation trên artifact có sẵn; G1.3/G1.5/G1.6, E1. Prototype giấy chỉ đề xuất sau khi có baseline; chưa tạo/chạy. |
| 9 · A9 | Feasibility | Verify/approval còn đúng scope sau khi đổi issue, config hoặc baseline. | S2 U11, S4 M6 là mặc định; chưa có invalidation evidence. | 5 × 3 = 15 | G3.4/G3.6, E4/E8: đối chiếu lịch sử version và bằng chứng; không mặc định approval cũ còn dùng được. |
| 10 · A10 | Usability | Từ session/thread/cycle/step/run/story có thể hiểu nhất quán khi diễn tả việc thật. | Glossary nháp S2 §9; S4 M9 chưa giải nghĩa story. | 3 × 4 = 12 | Walkthrough + annotation; G1.3, G2.3, G3.3, N2, E1/E8. Không áp nghĩa nháp trước khi nghe cách dùng thật. |
| 11 · A11 | Viability | Crate riêng/móc mỏng đủ giảm gánh bảo trì upstream trong khả năng Human. | S1 C6, S3 churn và ước lượng điểm móc; chưa đo công sync của Crew. | 4 × 3 = 12 | G3.2/G3.5, E9: lịch sử sync, diff, công sửa và check. Không coi ít dòng móc là bằng chứng bảo trì rẻ. |
| 12 · A12 | Value | Tập scenario mong muốn có ranh giới có thể kiểm chứng thay vì “mọi kịch bản”. | S2 DR1, S4 M5/M11; ngoài phạm vi quyết định hiện tại. | 3 × 4 = 12 | G2.2/G2.3, N3, E8: đối chiếu việc đã làm với catalog; để shaping/onboarding sau, không xếp backlog ở đây. |

Value ở đây là giá trị sử dụng và tiếp tục dùng của Human, không mặc định bán Crew. Khả năng phân phối/thương mại chỉ làm rõ qua tài liệu khi thực sự có kế hoạch; không mở một nghiên cứu willingness-to-pay ngoài scope. [S1 stakeholder map; S2 U1; S4 luật dừng]

## 4. Cây phụ thuộc và cách dùng guide

| Nhánh quyết định | Prerequisite | Trạng thái / frontier |
| --- | --- | --- |
| D0 · Duyệt kit để dùng | Có draft reviewable và mapping rủi ro | **Frontier hiện tại duy nhất:** Human duyệt/sửa kit. Chưa hỏi lại R1–R9/F1. |
| D1 · Chốt problem brief | Baseline S1/S4 và kết quả nghiên cứu; dùng thêm bằng chứng nếu thật sự đổi kết luận | Để bước playback; P1 là giả thuyết. Bộ câu hỏi cũ đã dừng, việc không có số đo không tự chặn route đã ghi. |
| D2 · Chọn hướng giải pháp/kiến trúc | Problem brief được xác nhận, R1/R2/R5, capability evidence cho phương án | Architect/Developers cùng Human xử lý ở shaping; không chọn ở kit. |
| D3 · Chốt kiểm chứng/quy tắc chất lượng cụ thể | Scope lát dọc và quyết định giải pháp, M2/M4 được phân biệt rõ | Để refinement/spec; kit không tự đặt ngưỡng hay DoD. |
| D4 · Mở rộng scenario/onboarding target-project | Quyết định mở rộng scope, biết repo/data/người liên quan | Deferred theo M5/M11. Không chặn việc hoàn thiện kit. |

Sau khi D0 được duyệt, Human chọn bằng chứng cần bổ sung; không chạy cả bốn guide mặc định. Trong một phiên, ưu tiên 3–5 câu của cùng góc nhìn và đi đủ mở đầu → bối cảnh → walkthrough → pain → ràng buộc → thành công → bước tiếp. Không đọc khuyến nghị giải pháp cho người trả lời. Probe hỏi “lúc đó xảy ra gì/cho xem gì”, không hỏi “anh/chị có thích feature X”.

Nếu một sự kiện mới mâu thuẫn lựa chọn đã ghi, ghi nguyên sự kiện và lựa chọn cạnh nhau; chỉ đưa một câu xác nhận mâu thuẫn theo ngoại lệ ở S4. Câu quyết định đó phải nêu vì sao cần, khuyến nghị và trade-off, viết để “có” chấp nhận khuyến nghị. Không suy trả lời từ im lặng; không dùng giả định để chọn thay Human. Các guide sau là thu thập bằng chứng, không phải một vòng quyết định số 3.

## 5. Guide theo loại stakeholder

Người tham dự đã biết: **Human kiêm người dùng, Sponsor/PO, Tech Lead và vận hành**. Phân các guide theo góc nhìn để không lẫn loại bằng chứng, không giả lập bốn người. Người dùng target-product, chuyên gia dữ liệu hoặc vendor chỉ dùng guide tương ứng nếu Human thực sự xác định họ và chọn mời; chưa ai được liên hệ hoặc nhận trách nhiệm. AI Orchestrator/Workers không phải stakeholder interview; kiểm bằng transcript và hành vi. [S1 stakeholder map; S4]

### G1 · Người dùng trực tiếp / Human — journey và khả năng duyệt

1. **G1.1 · Mở đầu:** “Trong buổi này, phần công việc nào gần đây anh/chị có thể kể và cho xem an toàn?” Probe: việc nào cần che dữ liệu; phần nào không muốn ghi lại? Vì sao: chọn sự kiện thật và giới hạn ghi chép. [A6; E1]
2. **G1.2 · Bối cảnh:** “Lần gần nhất anh/chị dùng SDLC để thực hiện một yêu cầu là việc nào, khi nào, đầu ra cuối cùng ở đâu?” Probe: ai dùng kết quả; việc đã hoàn tất hay còn dở? Vì sao: baseline giá trị, tránh coi ticket đóng là thành công. [A1; E1]
3. **G1.3 · Walkthrough:** “Từ yêu cầu đó đến lúc dừng, anh/chị đã làm những thao tác gì? Xin đi theo thứ tự trên màn hình/tài liệu.” Probe: mở thread, copy System/Request, chuyển ngữ cảnh, đọc, duyệt; đoạn nào khác chuỗi journey tài liệu; lúc đó gọi đâu là session, thread, cycle, step, run; một story chỉ tới đơn vị nào? Vì sao: kiểm journey và glossary bằng ví dụ, không bằng đồng ý định nghĩa. [A1, A8, A10; E1/E8]
4. **G1.4 · Pain:** “Ở lần đó, chỗ nào anh/chị phải làm lại, bỏ kết quả hoặc tự sửa? Xin chỉ một đoạn cụ thể.” Probe: đã biết sai bằng gì; hậu quả; một lần tương tự làm tốt có gì khác? Vì sao: kiểm nguyên nhân và phản ví dụ của P1/H2. [A1, A2; E2]
5. **G1.5 · Ràng buộc:** “Trong HTML gần nhất đã duyệt, anh/chị đọc phần nào, mở chi tiết nào và dựa vào đâu để quyết định?” Probe: thông tin còn thiếu; chỗ đã bỏ qua; lần nào bỏ qua rồi phải sửa quyết định; thời gian thực tế nếu có log, không yêu cầu đặt giới hạn mới. Vì sao: kiểm R8 qua hành vi, không hỏi lại ‘ngắn’ nghĩa gì. [A1, A8; E1]
6. **G1.6 · Thành công:** “Một đầu ra gần đây anh/chị chấp nhận dùng tiếp đã có bằng chứng gì? Một đầu ra đã từ chối khác ở điểm nào?” Probe: review/verification/approval khác nhau ra sao trong chính lần đó; ai quyết định; bằng chứng gì còn thiếu? Vì sao: hiểu tiêu chí đang dùng và glossary, không tự chốt DoD. [A8, A10; E1/E2]
7. **G1.7 · Kết:** “Tôi đã ghi đúng các sự kiện này chưa? Tài liệu hoặc ví dụ cụ thể nào anh/chị có thể mang lại tiếp, bằng cách nào và lúc nào?” Probe: đánh dấu chỗ sửa; chọn một artifact đã che dữ liệu. Vì sao: next step thực tế, không coi im lặng là xác nhận. [E1/E2]

### G2 · Sponsor / Product Owner — giá trị, phạm vi và trade-off

1. **G2.1 · Mở đầu:** “Trong các việc gần đây, quyết định đầu tư công sức nào anh/chị đang cần hiểu rõ hơn?” Probe: tài liệu hoặc lịch sử nào phản ánh quyết định đó? Vì sao: giữ tập trung vào nhu cầu đã có. [A1; E8]
2. **G2.2 · Bối cảnh:** “Tháng gần nhất, những loại việc nào đã chiếm thời gian của anh/chị? Kết quả nào được dùng thực tế?” Probe: scope/project/platform của việc đó; chi phí bỏ dở; có lần giao cho người khác hay phát hành binary không? Vì sao: giá trị và ranh giới thực tế, không mặc định mô hình doanh thu. [A1, A6, A12; E7/E8]
3. **G2.3 · Walkthrough:** “Xin đi lại một việc đã chọn làm và một việc đã hoãn: đầu vào, người quyết định, đầu ra và nơi giữ bản hiện hành là gì?” Probe: ticket/story/scenario/run được dùng thế nào; nhóm việc nào trong catalog phản ánh sai việc đã làm? Vì sao: glossary, phạm vi ‘100%’, nguồn nghiệp vụ; không hỏi lại GitHub hoặc run store. [A10, A12; E8]
4. **G2.4 · Pain:** “Trong một lần đầu ra không dùng được, phần công sức nào mất nhiều nhất và nguyên nhân được xác định bằng gì?” Probe: E1 có tài liệu nào còn giữ; các lần khác có cùng nguyên nhân không; lỗi nào dù đủ prompt vẫn xảy ra? Vì sao: phân biệt nhu cầu điều phối với vấn đề capability/chất lượng. [A1, A2; E2]
5. **G2.5 · Ràng buộc:** “Lần gần nhất phạm vi, thời gian, chi phí và chất lượng xung đột, anh/chị đã giảm phần nào trước và giữ phần nào?” Probe: quyết định thật, chi phí/quota/bảo trì đã phát sinh; điều gì làm thay đổi lựa chọn? Vì sao: chuẩn bị workshop dựa trên hành vi; intake hiện chưa có deadline. [A7, A11; E7/E9]
6. **G2.6 · Thành công:** “Trong một đợt công việc đã kết thúc, anh/chị đã dùng dấu hiệu nào để quyết định tiếp tục cách làm đó?” Probe: kết quả cho ai; số đo nào có thật; chuyện gì khiến ngừng; phân biệt mục tiêu Crew và chất lượng target-project theo R7. Vì sao: kiểm H1 bằng outcome, không tự xếp Product Backlog. [A1, A12; E8]
7. **G2.7 · Kết:** “Bản ghi nào nên được sửa sau buổi này, và ví dụ nào anh/chị có thể cung cấp để làm rõ phần chưa biết?” Probe: một next step cụ thể và người thực sự đồng ý làm. Vì sao: decision evidence và follow-up. [E2/E8]

### G3 · Tech Lead / Developer / người bảo trì — khả thi và kiến trúc

1. **G3.1 · Mở đầu:** “Anh/chị đã trực tiếp thực hiện hoặc review phần nào của công việc được chọn?” Probe: đâu là trải nghiệm trực tiếp, đâu là đọc báo cáo? Vì sao: giới hạn độ tin cậy lời kể. [E2/E9]
2. **G3.2 · Bối cảnh:** “Lần gần nhất đồng bộ upstream hoặc sửa một tích hợp ACP đã chạm các phần nào và tốn công ở đâu?” Probe: commit/diff/check còn giữ; có lần thay đổi tưởng nhỏ nhưng gây regression không? Vì sao: kiểm A11, không lấy ước lượng số dòng làm chi phí thật. [A11; E9]
3. **G3.3 · Walkthrough:** “Xin đi lại một lần chạy có nhiều thread/worker hoặc bị gián đoạn: trạng thái được ghi ở đâu và anh/chị xác định đang ở bước nào bằng gì?” Probe: session/thread/run được nối ra sao; hành động nào đã xảy ra trước khi mất kết nối; có mẫu tương tự của công cụ hiện tại không? Nếu chưa từng xảy ra, ghi ‘chưa có mẫu’, không dựng trải nghiệm giả. Vì sao: hiện trạng recovery và glossary. [A4, A10; E4]
4. **G3.4 · Pain:** “Trong một lần tích hợp sai hoặc bằng chứng review bị lỗi thời, anh/chị phát hiện bằng gì và phải làm lại phần nào?” Probe: API/backend mapping, baseline/config khác nhau, side effect trùng, test pass nhưng đầu ra sai; tìm phản ví dụ có kiểm chứng tốt. Vì sao: H2/H4 và hiệu lực evidence. [A2, A4, A9; E2/E4/E8]
5. **G3.5 · Ràng buộc:** “Ở các lần dùng provider gần đây, lỗi role, quyền, quota hoặc cancel nào thực sự đã gặp, với phiên bản nào?” Probe: transcript đã che dữ liệu; phần nào chỉ là báo cáo TD1; chưa biết thì chuyển yêu cầu probe cho Architect. Vì sao: thêm lịch sử customer-specific, không hỏi lại capability có thể đo. [A5, A7, A11; E5/E7/E9]
6. **G3.6 · Thành công:** “Một thay đổi đã được chấp nhận gần đây có những check và review nào, trên commit/config nào?” Probe: khi code/issue đổi, bằng chứng nào đã chạy lại; có quality standard hiện hành nào; ai thực sự thông qua? Vì sao: kiểm evidence và phân biệt M2 với DoD được chấp nhận. [A9; E8]
7. **G3.7 · Kết:** “Anh/chị có thể cung cấp transcript/diff nào để đối chiếu phần còn mở? Ai có thể kiểm mẫu đó và khi nào?” Probe: xác nhận người nhận đã đồng ý hay chỉ đề xuất. Vì sao: chuyển câu kỹ thuật sang người có bằng chứng. [E2/E4/E5/E9]

### G4 · Người vận hành / người giữ dữ liệu, tài khoản và điều khoản

1. **G4.1 · Mở đầu:** “Anh/chị có thể nói về phần vận hành nào mà không đưa credential hay dữ liệu nhạy cảm vào buổi này?” Probe: tài liệu nào chỉ được xem local, phần nào phải che? Vì sao: giới hạn thu thập. [A3, A6; E3/E6]
2. **G4.2 · Bối cảnh:** “Ở project gần nhất đã dùng AI, loại dữ liệu nào đã đi qua repo, provider và log; ai thực sự sở hữu dữ liệu/tài khoản?” Probe: phân phối nội bộ hay ra ngoài; pháp nhân, hợp đồng và phạm vi hoạt động nếu có hồ sơ; không suy từ địa chỉ máy. Vì sao: applicability của ràng buộc vận hành/điều khoản. [A6; E6]
3. **G4.3 · Walkthrough:** “Xin đi theo một thao tác ghi issue, push, merge hoặc publish gần nhất: từ yêu cầu tới hành động thực, quyền và quyết định được ghi ở đâu?” Probe: người/tiến trình thực hiện; trạng thái bypass; cách phân biệt người và AI; bước nào chỉ có lời hứa mà chưa cưỡng chế? Vì sao: kiểm ranh giới, không xin lại scope PAT đã chấp nhận. [A3; E3]
4. **G4.4 · Pain:** “Lần gần nhất hết quota, mất kết nối hoặc restart giữa công việc, điều gì đã xảy ra và được phục hồi bằng cách nào?” Probe: có ghi/PR dở dang không; lịch sử usage; công việc đã chờ bao lâu; nếu chưa có, ghi rõ. Vì sao: evidence cho R6/recovery và chi phí; không hỏi chọn provider mới. [A4, A7; E4/E7]
5. **G4.5 · Ràng buộc:** “Những hạn chế nào đã được áp dụng khi gửi code/dữ liệu, lưu transcript hoặc công khai issue gần đây?” Probe: điều khoản đúng loại account; retention/xoá; mẫu redaction; nội dung ngoài Human/Crew đã được xử lý thế nào; kiểm soát nào là quy định, kiểm soát nào đã chạy? Vì sao: kiểm M10 và A6 bằng evidence, không coi private repo đủ bảo vệ. [A3, A6; E3/E6]
6. **G4.6 · Thành công:** “Trong một lần vận hành được coi là ổn, anh/chị đối soát kết quả và quyền thực thi bằng những bản ghi nào?” Probe: lỗi không bị nuốt, số liệu chi phí, ai xác nhận hồi phục; còn hạn chế gì? Vì sao: outcome thực và khả năng truy vết. [A3, A4, A7; E3/E4/E7]
7. **G4.7 · Kết:** “Có thể cung cấp bản tóm tắt hoặc tài liệu đã che dữ liệu nào để xác nhận phần chưa biết, và ai thực sự giữ tài liệu đó?” Probe: cách trả lời an toàn, thời điểm, một next step cụ thể. Vì sao: người giữ kiến thức không cần gửi bí mật. [E3/E6/E7]

## 6. Questionnaire cho người giữ kiến thức không tham dự

**Chưa gửi.** Coordinator/Human đề xuất chọn người và các câu liên quan sau khi kit được duyệt. Không gửi toàn bộ nếu chỉ cần một loại bằng chứng. Trả lời “không biết/chưa từng có/không thể chia sẻ” là hợp lệ; tách thông tin trực tiếp biết với suy đoán. Không yêu cầu API key, PAT, auth file, dữ liệu nhận dạng hoặc transcript chưa che.

| ID | Câu hỏi gửi bằng văn bản | Probe / định dạng trả lời | Mapping |
| --- | --- | --- | --- |
| N1 | Một yêu cầu gần đây được thực hiện ra sao, kết quả dùng được hay phải làm lại? | Ngày, vai trò của người trả lời, các bước thực tế, ví dụ tốt và lỗi, link artifact được phép chia sẻ. | A1/A2/A8; E1/E2 |
| N2 | Trong chính việc đó, các từ session, thread, cycle, step, run, story chỉ tới những phần nào? | Dùng từ của người trả lời; đánh dấu phần journey tài liệu không khớp, không chọn sẵn định nghĩa primer. | A10; E1/E8 |
| N3 | Trong đợt công việc gần nhất, loại việc nào có và loại nào không xuất hiện; phạm vi project/nền tảng là gì? | Ví dụ thực, catalog chỉ dùng để đối chiếu sau câu trả lời; không đòi danh sách “100%”. | A12; E8 |
| N4 | Dữ liệu và thao tác ghi nào đã đi qua provider, repo và log trong một lần chạy gần đây? | Danh mục đã che, nơi lưu, ai/tiến trình ghi, bằng chứng quyền/gate; không gửi giá trị secret. | A3/A6; E3/E6 |
| N5 | Hồ sơ hiện hành nào giới hạn gửi/lưu/phân phối dữ liệu hoặc phần mềm trong phạm vi đó? | Loại account, tên/phiên bản điều khoản, ngày, người giữ hồ sơ; phần không thể xác định ghi unknown. | A6; E6 |
| N6 | Lần gián đoạn/quota/restart gần nhất được xử lý thế nào, có hành động dở dang và chi phí nào? | Adapter/version, timeline, usage đã che, trạng thái trước/sau, action đã đối soát; chưa có thì ghi chưa có. | A4/A5/A7; E4/E5/E7 |
| N7 | Sau một lần đổi code/config/issue hoặc sync upstream, các check/review nào đã chạy lại và kết quả ở đâu? | Commit/config, diff và check; công sửa; phân biệt plan với executed. Kết: một tài liệu có thể bổ sung tiếp, cách gửi và thời điểm. | A9/A11; E8/E9 |

## 7. Yêu cầu dữ liệu và quyền xem — đề xuất, chưa cấp/chưa lấy

Mẫu đề xuất nhỏ: một việc gần nhất, một việc bị sửa/bỏ và một việc được chấp nhận. Không đòi số lượng nếu chỉ có một trường hợp; đánh dấu giới hạn mẫu và không suy tỷ lệ lỗi từ đó. Chủ sở hữu tài liệu xác định bản có thể chia sẻ. Metadata/ảnh chụp đã che hoặc walkthrough local thường đủ cho discovery; quyền đọc repo riêng chỉ xin khi ví dụ đó không thể trả lời bằng cách này. Không xin quyền ghi, chạy production hoặc đổi credential.

| ID | Bằng chứng / quyền xem cần đề xuất | Dùng để kiểm gì | Người cung cấp/nhận đề xuất; trạng thái |
| --- | --- | --- | --- |
| E1 | Walkthrough một lần dùng SDLC và HTML đã duyệt: prompt, thao tác, decision, thời điểm nếu có; xin đồng ý riêng nếu ghi màn hình/âm thanh. | Journey, A1/A8/A10; baseline thao tác và công đọc. | Human → BA/UX; chưa cung cấp/quan sát trong phiên này. |
| E2 | E1 text-to-speech hoặc một lỗi khác: yêu cầu, capability/API backend, UI/diff, rework; thêm một kết quả tốt nếu có. | A2, nguyên nhân P1, phản ví dụ H2. Không nghiên cứu lại thị trường TTS. | Human/Developer → BA/Architect; chưa nhận. |
| E3 | Mô tả quyền thực và sơ đồ dữ liệu; bản ghi một hành động ngoài worktree và gate liên quan; môi trường/bypass metadata; test kiểm soát hiện có nếu có. | A3: ranh giới theo R2/R4 và M10 có cưỡng chế được không. Không yêu cầu thu hẹp PAT đã được chấp nhận. | Người vận hành → Architect/chuyên gia security nếu có; chưa nhận/chưa probe. |
| E4 | Một timeline gián đoạn/resume và các side effect trước/sau: ID issue/PR local, commit, run/attempt, thời điểm, result. | A4/A9: đối soát, trùng ghi và evidence hết hiệu lực. | Developer/vận hành → Architect; chưa nhận. Probe session load cũ không thay thế mẫu này. |
| E5 | Kế hoạch và kết quả probe mới đúng adapter/version cho Claude prompt, Antigravity role fallback/compaction, cancel khi chạy và permission thật. | A5; nguồn TD1 cũ giữ làm baseline. | Architect/Developer → BA nhận kết luận/giới hạn; chỉ đề xuất, chưa chạy hoặc phân công. |
| E6 | Danh mục dữ liệu, repo/account, retention, chính sách/hợp đồng/điều khoản được áp dụng; kế hoạch phân phối nếu đã có. Xin quyền đọc hồ sơ đã che ở nơi được phép. | A6: dữ liệu nào được gửi/lưu; tính áp dụng pháp lý/điều khoản. Không tự kết luận tuân thủ. | Human/người giữ hồ sơ → chuyên gia phù hợp nếu có; chưa nhận/chưa xác nhận owner. |
| E7 | Usage/quota/chi phí và thời gian chờ của các lần dùng thật, theo gói và kỳ đo; che định danh/tài khoản. | A7: chi phí thực, bottleneck; giá gói C7 không thay số đo. | Human/vận hành → BA/Architect; chưa nhận/chưa đo. |
| E8 | Ticket/issue, lịch sử sửa, checklist/check output, commit/config liên quan, quyết định review; các loại việc đã thực hiện. | A9/A10/A12; phân biệt thông tin spec trong Issues với trạng thái chạy trong run store theo R5. | Human/Developer → BA/Architect; chưa nhận. Không giả lập story đang có khi query Issues trả rỗng. |
| E9 | Một lần sync upstream: baseline trước/sau, diff/check, phần sửa thủ công và công bỏ ra nếu có. | A11: chi phí vận hành fork. | Người bảo trì → Architect; chưa nhận/chưa refetch. |

Đăng nhập Claude và các probe kỹ thuật còn mở không chặn việc duyệt kit; chúng giới hạn kết luận khả thi sau đó. Không xin Human cung cấp lại facts mà Developers có thể đọc/đo. Nếu không có specialist, ghi nhu cầu phối hợp và giữ kết luận mở, không gán cho AI quyền phê duyệt thay họ.

## 8. Agenda kickoff workshop — đề xuất 60 phút

Workshop là playback/đồng bộ bối cảnh sau D0, không phải một vòng discovery mới. Dùng câu trả lời đã có trước; điều chưa biết ghi rõ. Không mặc định có Sprint hoặc dùng workshop để xếp backlog thay PO.

| Phút | Nội dung | Câu dẫn / kết quả reviewable |
| --- | --- | --- |
| 0–5 | Vì sao ở đây | Nhắc nhu cầu S1, E1 và P1; phân biệt lời kể với quan sát; thống nhất mục đích buổi bằng lời xác nhận thật. |
| 5–15 | Outcome và tín hiệu thành công | Playback một việc thật; đối chiếu R7/R8 và A1/A2/A8. Tín hiệu đề xuất: công thao tác/đọc/rework cùng lỗi lọt review; chưa gán ngưỡng hoặc gọi là DoD. |
| 15–23 | Ngoài phạm vi | Giữ phạm vi máy này/macOS/local, không nghiên cứu lại TTS; scenario rộng và onboarding deferred M5/M11; không mở chọn kiến trúc, lập ticket hoặc implementation trong bước kit. |
| 23–35 | Hàng xóm và dependencies | Human, target-project, GitHub, ba ACP provider, upstream; sơ đồ dữ liệu/quyền nếu có. Ghi phụ thuộc auth/probe/điều khoản, bên giữ bằng chứng và phần chưa kiểm. |
| 35–47 | Điều khiến lo lắng | Chọn các rủi ro điểm cao theo sự kiện: gate không cưỡng chế, side effect dở dang, role mất, dữ liệu ra ngoài, rework không giảm. Tách accepted risk về token khỏi kiểm soát chưa chứng minh. |
| 47–55 | Khi scope/time/budget/quality xung đột, điều gì nhường trước | Bắt đầu từ quyết định thật ở G2.5; hiện chưa có deadline. Khuyến nghị đưa ra để Human cân nhắc: giảm scope trước, giữ chất lượng lát dọc theo Q3; đổi lại độ phủ đạt muộn hơn. Không ghi đã đồng ý nếu chưa có câu trả lời. |
| 55–60 | Wrap-up và next step | Đọc lại facts/quotes/assumptions/decisions riêng; sửa chỗ hiểu sai; chọn một evidence request cần thiết, recipient và thời điểm nếu họ đồng ý; không coi người được đề xuất là owner đã nhận. |

Người tham dự thực tế hiện chỉ xác định Human. PM/UX/Architect/Coordinator là các góc nhìn cộng tác đề xuất; không tuyên bố đã mời hoặc họ đã phản hồi. Workshop có thể rút gọn hoặc bỏ nếu bản ghi đã đủ, theo lựa chọn Human sau khi duyệt kit.

## 9. Mẫu ghi chú phiên

```text
Session / ngày / người tham dự thực tế:
Guide và câu đã dùng; giả định cần kiểm:
Phạm vi ghi âm/ảnh và đồng ý thực tế (hoặc không ghi):
Sự kiện đang kể: ngày, project, vai trò người kể:
Nguồn/phiên bản/commit; nơi lưu được phép; phần che dữ liệu:

QUOTE — nguyên văn, người nói, timestamp; không sửa rồi gọi là quote:
OBSERVED FACT — người ghi đã trực tiếp thấy gì, ở đâu, lúc nào:
REPORTED FACT — người tham dự kể sự kiện gì; chưa đối chiếu bằng gì:
OPINION — đánh giá/ưu tiên của ai, không chuyển thành fact:
INFERENCE / HYPOTHESIS — suy luận của BA, căn cứ, phản ví dụ:
DECISION — nội dung, người có thẩm quyền đã nói gì, scope, ngày,
           rationale/trade-off; phân biệt confirmed/proposed/default:

Journey / glossary: chỗ khớp, chỗ bác bỏ và ví dụ thực:
Assumption ID: supported / contradicted / inconclusive; bằng chứng và giới hạn:
Unresolved: câu nào còn mở; prerequisite nào thiếu:
Next action: artifact, recipient, owner proposed hay accepted,
             ngày cam kết nếu thật có; return evidence:
Playback: phần đã xác nhận, phần sửa, phần chưa được trả lời:
```

Không dùng bản tóm tắt AI như nguyên văn khách hàng. Không ghi “quan sát” nếu chỉ đọc report. Không dùng việc Human duyệt kit làm bằng chứng chấp nhận P1/H1–H4, DoD hoặc phương án kiến trúc.

## 10. Trạng thái, giới hạn và route

| Tiêu chí bước này | Bằng chứng trong kit | Trạng thái |
| --- | --- | --- |
| Giả thuyết vấn đề và giải pháp chưa xác nhận; assumption map đủ bốn loại | §2–3; I × U và phép kiểm/bác bỏ | Draft đã soạn. |
| Mỗi high-risk assumption có câu hoặc evidence request | A1–A8 → G1–G4/N1–N6/E1–E7 | Đã đối chiếu tĩnh; phép kiểm chưa thực hiện. |
| Guide có thứ tự, hỏi sự kiện/hành vi, có probe và wrap-up | Bốn guide §5, questionnaire §6; không pitch giải pháp | Đã review nội dung; chưa dùng với người trả lời. |
| Data/access request, agenda và note template | §7–9 | Proposed; chưa cấp quyền, chạy workshop hoặc thu thập notes. |
| Human duyệt hoặc sửa kit trước khi dùng | D0 | **Awaiting decision.** Chưa đạt tiêu chí hoàn thành bước. |

**Không có prerequisite thiếu chặn việc soạn kit:** intake, stakeholder map, primer và landscape đều đã đọc; S4 có câu trả lời mới hơn được giữ. Những unknown A1–A12 chặn các kết luận tương ứng, không tự tạo thêm approval gate hoặc vòng hỏi. Trạng thái “discovery kết thúc” ở S4 vẫn giữ nguyên; trạng thái của kit bổ sung này là “chờ duyệt”, không sửa lịch sử thành “đã duyệt kit”.

**Những gì thực sự đã làm:** đọc S1–S6, đối chiếu bản ghi quyết định với báo cáo cũ, kiểm transcript TD1 và truy vấn danh sách Issues, soạn kit, kiểm cấu trúc/mapping/link local. Không phỏng vấn khách hàng, chạy probe mới, benchmark, prototype, xác minh account terms, gửi questionnaire hay công khai tài liệu lên tracker.

**Validation artifact:** kiểm tĩnh đạt: 10 section, 9 bảng, 21 link/anchor, 12 assumption, 8 high-risk có mapping, 28 câu guide; đủ bốn nhóm Value/Usability/Feasibility/Viability; CSS có `width: 100%`, `max-width: none` và padding responsive. Đã thử Chrome headless để kiểm layout, nhưng lượt viewport rộng timeout sau 30 giây; lượt hẹp chưa chạy. Vì vậy chưa xác minh layout runtime hoặc usability với Human. Không coi kiểm cấu trúc là kiểm trải nghiệm sử dụng.

| Recipient / owner đề xuất | Artifact và trạng thái | Phần còn mở | Next action |
| --- | --- | --- | --- |
| Human với góc nhìn PM/PO | Toàn bộ kit — draft | D0; giá trị thực của P1 | Duyệt/sửa cách dùng kit; chọn evidence thật sự đổi kết luận, không hỏi lại R1–R9/F1. |
| UX Researcher / Designer, nếu có | G1, A1/A8, E1 — proposed | Journey và công duyệt chưa quan sát | Hỗ trợ Human chọn mẫu/quan sát sau D0; mang lại notes thật, không mô phỏng feedback. |
| Software / Solution Architect / Developers | A3–A5/A9/A11, E3–E5/E8/E9 — proposed | Probe và enforcement/recovery chưa chứng minh | Bổ sung evidence kỹ thuật; chọn hướng kiến trúc ở shaping sau playback. |
| Coordinator / Orchestrator | Guide, questionnaire, agenda, note template — draft | Người tham dự/owner chưa nhận | Giúp Human chọn phiên hoặc request phù hợp; chỉ gửi/mời khi được Human chỉ đạo. |

**Next route:** Human duyệt/sửa kit; nếu chọn chạy phiên bổ sung thì Human thực hiện với người liên quan, mang lại notes/transcript/tài liệu đã che dữ liệu để tổng hợp và kiểm lại A1–A12. Nếu không cần thêm phiên, dùng S4 để đi tiếp problem brief như route đã ghi. Tiếp tục trong planning session này; không tạo portable handoff khi chưa đổi người/công cụ/repository. Chưa viết problem brief, spec hoặc tickets trong bước này.
