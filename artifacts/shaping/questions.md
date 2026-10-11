# Bộ câu hỏi shaping · Zed AI Crew

**Bộ gốc cố định từ lần cập nhật này · 2026-10-11 · Business Analyst.** Đây là kế hoạch hỏi và sổ ghi quyết định trong cùng context, chưa phải spec. Các câu đã có câu trả lời được tái sử dụng, không hỏi lại. Không tuyên bố bộ này đã được lập trước bảy câu trong hội thoại.

**Human chỉ đọc [bản HTML](questions.html).** Markdown này là sổ đầy đủ cho AI. G6 bản sửa đã confirmed; bộ gốc đã xử lý và không có câu đang chờ trả lời trong interview này. Mọi câu hỏi thực sự mới về sau phải có mục tiêu/phạm vi riêng, không nối vào interview đã dừng này.

**Sửa phạm vi theo phản hồi Human:** tài liệu trước đã thu hẹp shaping vào lát đầu. Kết thúc interview này không chứng minh đã định hình đủ toàn sản phẩm. [Toàn cảnh production-ready](solution-scope.html) thay thế bản tổng hợp hẹp; các câu trả lời đã có vẫn giữ nguyên.

## 1. Mục tiêu và giới hạn

Định hình **toàn cảnh sản phẩm Crew production-ready**: giá trị và ranh giới, toàn bộ hành trình vòng đời, năng lực chung, mô hình công việc, trách nhiệm điều phối, tương tác Human và chất lượng. Mỗi Epic là một lát nhỏ đủ để triển khai và sử dụng trong toàn cảnh đó; lựa chọn Build đầu tiên theo playback là thứ tự giao việc, không giới hạn phạm vi shaping.

Đi từ **toàn sản phẩm → mô hình vận hành chung → ranh giới Epic → chi tiết cần thiết**. Không dùng `implement` làm đại diện để thiết kế hết sản phẩm. Luồng cycle `implement` giữ draft theo câu trả lời 5; đi sâu khi triển khai.

Không hỏi Human về dữ kiện tra được từ repo, GitHub hoặc probe; không hỏi lại quyết định đã xác nhận. Database/schema, API, LLD, số worker, retry budget, từng cycle/step và cách viết test thuộc thiết kế triển khai. Một câu kỹ thuật chỉ trở thành câu hỏi Human khi kết quả kiểm chứng buộc chọn một trade-off sản phẩm thực sự.

## 2. Quy trình hỏi có điểm dừng

1. **Đóng băng sáu câu gốc G1–G6 bên dưới.** Không nối thêm G7/G8 trong khi đang trả lời. Mỗi câu phải gỡ một quyết định tổng thể trong mục tiêu.
2. Dùng câu trả lời và nguồn đã có trước. Chỉ hỏi câu gốc còn thiếu câu trả lời; hỏi theo thứ tự lớn → nhỏ, tối đa ba câu mỗi lượt. Câu có prerequisite chưa giải quyết phải chờ.
3. Có thể ghi ứng viên câu phụ từ câu trả lời, nhưng **không hỏi bất kỳ câu phụ nào trước khi tất cả câu gốc đã có câu trả lời hoặc quyết định hoãn rõ của Human**. Silence không phải câu trả lời.
4. Sau bộ gốc, xét lại **từng ứng viên phụ với toàn bộ câu trả lời**, kể cả những câu đã nêu nhưng chưa được trả lời. Mỗi ứng viên chỉ giữ nếu đồng thời: chặn một quyết định nằm trong mục tiêu; chưa được câu trả lời/nguồn khác giải quyết; không thể tra hoặc kiểm chứng độc lập; và thật sự cần lựa chọn của Human ở mức shaping.
5. Ghi kết quả lọc: **hỏi / loại / hoãn triển khai / route validation**, kèm lý do và quyết định bị chặn. Không dùng nhãn “có thể hữu ích” để giữ câu phụ.
6. **Một vòng phụ duy nhất, tối đa ba câu sau khi lọc**, theo thứ tự lớn → nhỏ. Đây là giới hạn của kế hoạch hỏi này, không phải quyết định sản phẩm được suy từ Human. Không phát sinh chuỗi phụ của phụ. Nội dung còn lại đưa vào draft/thiết kế triển khai/validation; nếu còn blocker thật thì ghi blocked cùng quyết định cụ thể, không tuyên bố ready.
7. **Dừng interview** khi bộ gốc và vòng phụ đã xử lý; tổng hợp quyết định, draft và phần hoãn để Human kiểm tra cách hiểu trong bản shaping. Không thêm một vòng duyệt questionnaire riêng. Nếu có câu trả lời mâu thuẫn, chỉ hỏi câu xác nhận đúng mâu thuẫn đó; không mở rộng sang nhánh mới.

Không biến “đã ghi default”, “giữ draft” hoặc “planned check” thành confirmed/verified. Kết thúc bộ câu hỏi không tự chứng minh sản phẩm đã được kiểm chứng hoặc đủ điều kiện triển khai.

## 3. Bộ câu hỏi gốc cố định — lớn tới nhỏ

### G1 · Crew giải quyết vấn đề gì, cho ai, trong ranh giới nào?

**Vì sao cần:** xác định sản phẩm và kết quả cần tạo trước khi chọn workflow.

**Đã có câu trả lời — không hỏi lại:** một Human dùng Crew để giảm điều phối prompt/context, công đọc/duyệt và rework; nhận thay đổi dùng được, bảo trì được và giữ quyền quyết định. Rust/GPUI trong fork Zed, crate Crew riêng; ba provider ACP; GitHub Issues; máy macOS này, project local; Crew public, target-project private. Không có deadline được ghi. Nguồn: [playback đã xác nhận](../discovery/problem-brief.md), intake C1–C6 và discovery R1–R9/F1.

### G2 · Mô hình vận hành chung từ yêu cầu Human tới kết quả là gì?

**Prerequisite:** G1. **Vì sao cần:** có hợp đồng công việc chung, thay vì thiết kế riêng từng tình huống nhỏ.

**Đã có câu trả lời — không hỏi lại:** Human nói với Orchestrator; Orchestrator xác định session, tạo session plan và chạy các cycle theo thiết kế. Session kết thúc khi đạt OUTPUT. Ví dụ xác nhận: `refine` → Story Ready; `implement` nhận Story Ready → PR merged vào main. Cấu trúc cycle chi tiết vẫn draft. Không suy số thread/worker hoặc hierarchy Story/Task từ những câu trả lời này. Nguồn: câu 2–5 hiện tại; [glossary](../../GLOSSARY.md).

### G3 · LLM và code điều phối chịu trách nhiệm gì?

**Prerequisite:** G2. **Vì sao cần:** chọn ranh giới kiến trúc có ảnh hưởng đến mọi session.

**Confirmed — câu 7, không hỏi lại:** hướng lai. Orchestrator LLM hiểu yêu cầu và tạo session plan; code kiểm tính hợp lệ, giữ trạng thái và điều khiển thực thi theo dependency, quyền và OUTPUT. Trade-off Human đã đồng ý: hội thoại linh hoạt và tiến trình được quản lý rõ, đổi lại plan cần có cấu trúc. Không suy database, MCP hoặc API đã được chọn. Nguồn: câu 7; [landscape §4](../research/technical-landscape.html).

### G4 · Chia phạm vi giao được theo đơn vị nào; lát đầu và phần hoãn là gì?

**Prerequisite:** G1/G2. **Vì sao cần:** phân biệt phạm vi toàn sản phẩm với cách chia công việc triển khai; không dùng Epic đầu để thu hẹp đầu ra shaping.

**Đã có câu trả lời — không hỏi lại:** mỗi Epic là một lát nhỏ đủ để triển khai và sử dụng, áp dụng cho lát đầu và lát N. Build theo ticket trên repo Crew là thứ tự đầu đã ghi. Onboarding và các scenario khác vẫn phải có trong toàn cảnh dù triển khai sau; remote/OS khác và đa người dùng ngoài ranh giới sản phẩm đã ghi. Không thay ordering của PO. Chuỗi cycle implement giữ draft, đi sâu khi triển khai. Chưa có trần chi phí được ghi; không suy ngân sách vô hạn và không yêu cầu một con số khi nó chưa chặn phạm vi. Nguồn: câu 1/5; playback, intake Q3, discovery M11 và phản hồi hiện tại về toàn cảnh.

### G5 · Human tương tác và giữ quyền quyết định thế nào?

**Prerequisite:** G1–G3. **Vì sao cần:** xác định trải nghiệm và quyền chung trước khi chọn màn hình hoặc các nút cho từng ngoại lệ.

**Đã có câu trả lời — không hỏi lại:** Human dùng hội thoại Zed với Orchestrator. Pipeline dưới ô chat, đồ thị mở rộng và tab Run log là hình mẫu đã ghi; câu hỏi nằm trong hội thoại. Run store giữ trạng thái, Issues giữ spec/ticket. Worker trong worktree riêng; ngoài worktree qua Crew/gate; phần phụ thuộc Human chờ, phần độc lập tiếp tục. Crew tích hợp vào integration branch, Human duyệt PR vào main. Approval theo phiên bản; code đổi sau approval thì check lại và Human duyệt phiên bản mới. Hết quota hỏi Human, không tự đổi provider. Nguồn: intake F6; discovery R1/R2/R4–R6; câu 6.

### G6 · Khung chất lượng tối thiểu nào dùng để đánh giá một Epic Crew giao được?

**Prerequisite:** G1–G5. **Vì sao cần:** phân biệt hoàn thành OUTPUT của workflow với chất lượng của lát sản phẩm sử dụng được.

**Phạm vi:** chất lượng chính sản phẩm Crew; không áp khung này tự động lên target-project.

**Phản hồi Human đã nhận:** đề xuất cũ chưa đủ; Human nêu rủi ro AI tự viết test cho code của mình, test pass nhưng phần mềm không thực hiện đúng điều Human mong muốn. Đây là phản hồi/rủi ro được báo, chưa có mẫu lỗi, tỷ lệ false pass hoặc test Crew thực tế được kiểm. Không diễn giải thành mọi test do AI viết đều vô giá trị hoặc luôn pass.

**Khung sửa confirmed — Human trả lời “đồng ý”:** tiêu chí/expected behavior từ spec đã xác nhận; kiểm chứng bởi người/worker khác tác giả với context riêng; đánh giá code và test theo yêu cầu, kể cả hành vi thiếu và khả năng phép kiểm phát hiện sai hành vi; chạy Crew end-to-end với giao diện và tích hợp thật trong phạm vi Epic; tiêu chí phi chức năng theo rủi ro/ISO; HTML ghi bằng chứng đúng version và phần chưa kiểm, Human trải nghiệm phần cần phán đoán trong review hiện có. AI viết code có thể viết test, nhưng tự báo pass không đủ để chấp nhận. Một worker/model khác không tự bảo đảm tránh giả định sai; cần tiêu chí độc lập và bằng chứng hành vi thực tế.

Test/clippy, báo lỗi, recovery và đối soát thao tác ghi vẫn là baseline bên trong khung mở rộng. Developers/QA làm sâu kiểm chứng từng Epic; chưa chạy phép kiểm mới. Trade-off: thêm công kiểm chứng/chạy tích hợp, giảm phụ thuộc lời tự xác nhận AI. Giữ một lựa chọn ở G6 về khung tổng thể; không sinh câu hỏi Human cho từng testcase.

**Trạng thái:** khung cũ bị đánh giá chưa đủ; khung sửa đã confirmed trong hội thoại. Production cho từng lát và ISO đã chọn. G6 chưa tự trở thành Definition of Done của Scrum Team hoặc chứng nhận ISO. Nguồn: feedback và lời đồng ý hiện tại; intake Q3, discovery R7/M2, primer §5–6; D-SDLC-05 cũ chỉ là tham khảo về kiểm chứng độc lập.

## 4. Sổ trả lời hiện có

| Câu trong hội thoại | Câu trả lời / trạng thái | Dùng ở |
| --- | --- | --- |
| 1 | Mỗi Epic là lát nhỏ đủ để triển khai và sử dụng. | G4 |
| 2 | Human → Orchestrator → nhận diện session → session plan → cycle → output. | G2 |
| 3 | Session kết thúc khi đạt OUTPUT; implement là PR merged vào main. CI failed còn trong công việc của session. | G2/G6 |
| 4 | OUTPUT refine là Story Ready; không implement với dữ liệu thiếu. | G2 |
| 5 | Chuỗi cycle implement giữ draft, làm sâu khi triển khai. | G2/G4 |
| 6 | Đồng ý approval gắn phiên bản; sửa code thì check lại và Human duyệt phiên bản mới. | G5 |
| 7 | Đồng ý hướng lai: LLM hiểu yêu cầu/tạo plan, code quản lý thực thi. | G3 |
| 8 | Chưa trả lời. Đã hỏi quá sâu trước khi có bộ gốc; chuyển thành ứng viên P1, không hỏi tiếp lúc này. | Sổ ứng viên phụ |
| G6 | Khung cũ chưa đủ; khung sửa cho Crew đã được Human đồng ý. Rủi ro AI tự kiểm chứng được đưa vào khung chất lượng. | G6 |

Các câu trả lời G1–G5 được giữ; G6 đã confirmed. Không hỏi lại. Bộ gốc đã xử lý; phạm vi mà bộ này bao phủ chưa đủ để kết luận toàn cảnh hoàn tất.

## 5. Kết quả lọc cuối sau tất cả câu trả lời

Đã xét P1–P8 theo G1–G6 và dừng bộ hỏi cũ. **Không có câu phụ đang chờ trong bộ này.** Bảng dưới lưu kết quả lọc trước khi Human chỉ ra thiếu toàn cảnh; nó không chứng minh không còn gap ở cấp sản phẩm. Các phần hoãn không phải lựa chọn đã được Human đồng ý. Các hợp đồng chung về replan, cấu hình, hierarchy và recovery phải được xét trong toàn cảnh trước khi giao hành vi liên quan; chi tiết cycle implement vẫn giữ draft.

| ID | Ứng viên / nguồn | Kết quả cuối và lý do |
| --- | --- | --- |
| P1 | Câu 8: Human đổi scope khi implement đang chạy. | **Hoãn triển khai.** Ngoại lệ của một session; M6 có default báo drift, G6 yêu cầu hành vi/ngoại lệ được kiểm trước giao Epic. Không chặn hình dạng tổng thể; pause/replan chưa được chọn. |
| P2 | Từng cycle, ordering, fan-out/join, retry/rework budget, retro. | **Hoãn triển khai.** Human yêu cầu chuỗi cycle giữ draft ở câu 5. |
| P3 | Story/ticket/Task hierarchy trên GitHub. | **Hoãn thiết kế Epic liên quan.** G2 đủ mô hình tổng thể; không nhập taxonomy Rally cũ hoặc giả định Story = một ticket. |
| P4 | UI loading/error/empty, lời báo invalid issue, nút thao tác. | **Hoãn thiết kế UI.** G5 đã có hình mẫu và kênh tương tác; không dùng chi tiết copy/layout để kéo dài shaping. |
| P5 | Schema, database, MCP, API, model/provider cho Orchestrator. | **Route kỹ thuật.** G3 đã chọn hướng lai; không còn lựa chọn kiến trúc tổng thể chờ Human. Developers làm sâu theo Epic; quyết định mới nếu có nằm trong phạm vi thiết kế đó. |
| P6 | Enforcement ngoài worktree, recovery sau ghi, capability ba ACP. | **Route validation.** Là câu hỏi thực nghiệm, BA/Developers tự tìm bằng chứng; câu trả lời mong muốn của Human không thay phép kiểm. |
| P7 | Scope/version áp dụng của checks và approval. | **Loại phần đã trả lời:** câu 6 đã chốt approval theo phiên bản. Cách nhận diện version và check nào chạy lại là chi tiết triển khai. |
| P8 | Có thêm gate duyệt session plan hoặc mở lại lựa chọn provider khi quota cạn không? | **Loại:** workflow câu 2 không có gate plan mới; quota đã chốt R6. Không tự thêm gate hoặc hỏi lại. |

Người xử lý kỹ thuật/QA/UX trong các route trên là **đề xuất**, chưa ai nhận trách nhiệm và chưa có liên hệ bên ngoài. Chưa có validation mới được chạy.

## 6. Điểm dừng interview và giới hạn kết luận

G6 đã confirmed; P1–P8 đã được xét lại. **Interview cũ đã dừng, không tự mở vòng phụ.** Human không cần trả lời câu 8 hoặc xác nhận lại bộ câu hỏi. [Toàn cảnh production-ready cho Human](solution-scope.html) trình bày phạm vi toàn sản phẩm, quyết định đã chốt, đề xuất và các gap. Trạng thái toàn cảnh là draft, chưa complete hoặc ready-to-build.

Bản shaping cần nêu hình dạng giải pháp, phạm vi và phần hoãn, quyết định confirmed, chuỗi cycle draft, lựa chọn còn blocked và validation chưa thực hiện. Câu trả lời mới có thể giải quyết nhiều ứng viên cùng lúc; phải loại các câu đã mất lý do tồn tại. Không claim ready-to-build khi còn blocker của phạm vi triển khai. Không biến mọi chi tiết deferred thành blocker của bước shaping tổng thể.

**Next route:** xét coverage/gap ở mức toàn sản phẩm trước khi tổng hợp spec trong cùng context. Giữ luật bộ gốc cố định và lọc câu phụ nếu thật sự cần một bộ hỏi mới có mục tiêu; không nối thêm câu vào interview này. Khung chất lượng được đồng ý không phải bằng chứng sản phẩm đã pass; chưa chạy validation Crew mới.

Nguồn chung: [problem brief](../discovery/problem-brief.md), [synthesis](../discovery/synthesis.md), [discovery decisions](../discovery/questions.html), [domain primer](../research/domain-primer.md), [technical landscape](../research/technical-landscape.html), [bản shaping](solution-scope.md), [glossary](../../GLOSSARY.md) và câu trả lời Human trong hội thoại hiện tại. File này điều khiển thứ tự hỏi trong bước shaping; không sửa lịch sử discovery hoặc thay GitHub Issues làm nguồn spec/ticket.
