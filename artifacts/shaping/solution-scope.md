# Toàn cảnh sản phẩm production-ready · Zed AI Crew

**Draft toàn cảnh · 2026-10-11 · Business Analyst.** [Bản HTML cho Human](solution-scope.html). Human yêu cầu toàn cảnh sản phẩm; chia Epic là cách triển khai toàn cảnh, không giới hạn đầu ra shaping vào một Epic.

**Cập nhật sau review cùng ngày:** Human đồng ý ba khuyến nghị review (D15–D17: danh mục S01–S15, một PR mỗi Story, Crew merge sau approval khớp phiên bản). Bổ sung các quyết định đã chốt bị rơi (R5, F6, M11 discovery, D8) và đổi tiền tố khối năng lực từ `M` sang `C` để không trùng mặc định discovery M1–M11.

Các quyết định đã xác nhận được giữ. Catalog và hợp đồng ngoài phần đã chốt là draft có nguồn, chưa phải thiết kế đã được Human đồng ý hoặc bằng chứng sản phẩm đã chạy. Không tự mở thêm questionnaire.

**Quy ước ID:** C01–C11 là khối năng lực của tài liệu này; M1–M11 là mặc định discovery ([bản ghi discovery](../discovery/questions.html)); S01–S15 là kịch bản; D0–D17 là sổ quyết định §11.

## 1. Đích sản phẩm và ranh giới

Crew hỗ trợ Human xuyên vòng đời phát triển phần mềm trong Zed: từ yêu cầu/ý tưởng và project có sẵn, tới công việc Ready, thay đổi đúng yêu cầu, chuyển giao khi được ủy quyền, vận hành, xử lý sự cố, cải tiến và ngừng sử dụng. Human dùng hội thoại, trả lời clarification và duyệt quyết định; Crew quản lý ngữ cảnh, tiến trình và bằng chứng.

Ranh giới đã ghi: một Human; máy macOS này, project local; Rust/GPUI trong fork Zed, crate Crew riêng và móc mỏng; Claude/Codex/Antigravity qua ACP; GitHub Issues giữ spec/ticket; run store giữ thực thi; Crew public, target-project private. Không mở thêm multi-user/remote/OS khác. Crew chạy local không tự quyết môi trường release của target-project hoặc cấp quyền thao tác lên môi trường đó.

Mục tiêu production-ready gồm toàn phạm vi được xác định và chất lượng sử dụng được; không được dùng số scenario/cycle để tự tuyên bố phủ 100%. Toàn cảnh thiết kế phải nhìn thấy trước khi phân rã Epic. Giao Epic nhỏ đạt chất lượng vẫn theo intake Q3; thứ tự Build đầu tiên đã ghi không loại các năng lực khác khỏi toàn cảnh. Chưa có deadline/trần chi phí; không suy ngân sách vô hạn hoặc hạ chất lượng để đạt thời gian.

## 2. Các khối năng lực toàn sản phẩm

| Khối | Năng lực phải thấy trong toàn cảnh | Trạng thái |
| --- | --- | --- |
| C01 · Trải nghiệm Human trong Zed | Hội thoại nhận yêu cầu, clarification và quyết định; pipeline dưới chat, đồ thị cycle/step, Run log, HTML review chỉ hiện điều Human bắt buộc phải biết. Không có nút Approve/Re-run trong Crew; câu hỏi nằm trong hội thoại; approval PR vào main qua GitHub PR review (D17). | Kênh, hình mẫu UI (intake F6) và nơi duyệt PR đã ghi; chi tiết trạng thái/keyboard/error còn draft. |
| C02 · Orchestrator và session plan | Hiểu intent, xác định session, tạo plan theo thiết kế, phân công role và tổng hợp kết quả; dùng lại quyết định còn hiệu lực. | Ranh giới LLM/code confirmed; hợp đồng plan và cách chọn route còn thiết kế. |
| C03 · Engine thực thi chung | Kiểm đầu vào và dependency; dispatch/join; chờ Human; nhận evidence/verdict; quản lý lỗi, retry/rework, hủy, restart và đối soát trước phát lại. | Code giữ state/quyền/OUTPUT đã chọn; state machine và chính sách ngoại lệ draft. |
| C04 · Playbook toàn vòng đời | Danh mục session với INPUT/OUTPUT; cycle/step tái dùng; nhánh điều kiện, tiêu chí kiểm chứng, version cấu hình và phạm vi áp dụng. | Danh mục S01–S15 confirmed (D15); recipe từng session chưa duyệt. |
| C05 · Worker qua ba provider ACP | Claude, Codex, Antigravity; role/context/skills, capability/mode/model, cwd, tiến độ, quyền, cancel/resume và quota theo profile. | Ba provider bắt buộc; role/auth/cancel/compaction chưa đủ bằng chứng tương đương. |
| C06 · Workspace và tích hợp thay đổi | Worktree riêng; mỗi Story một integration branch; tích hợp worktree vào integration branch; xử lý conflict, check trên bản tích hợp, một PR mỗi Story, feedback/approval và merge main. | Integration, PR mỗi Story, approval theo phiên bản và người merge confirmed (D4, D10, D16, D17); cơ chế conflict và rework draft. |
| C07 · Ngữ cảnh, backlog và bằng chứng | GitHub Issues giữ spec/ticket, Crew là bên duy nhất ghi Issues; glossary giữ nghĩa, ADR giữ rationale; run store giữ tiến trình; transcript/log/diff có truy vết và ranh giới dữ liệu. | Nguồn sự thật và bên ghi đã chọn (R5); hierarchy/mapping target-project và data lifecycle chưa chốt đầy đủ. |
| C08 · Kiểm chứng chất lượng độc lập | Tiêu chí từ yêu cầu; người/worker khác tác giả kiểm cả code/test; chạy E2E với tích hợp thật; chất lượng phi chức năng; evidence đúng version cho Human. | Khung G6 confirmed cho Crew; seams, ngưỡng và phép kiểm còn thiết kế/validation. |
| C09 · Quyền, dữ liệu và thao tác có tác động | Phân biệt quyền tool, quyết định sản phẩm và chất lượng; kiểm boundary ngoài worktree, input không đáng tin, secret/log và thẩm quyền release/migration. | Giữ PAT rộng đã chấp nhận; main chưa có bảo vệ kỹ thuật (§10); hiệu lực boundary và phạm vi gửi/lưu dữ liệu chưa chứng minh. |
| C10 · Cấu hình theo project | Cấu hình chuẩn và override project cho playbook/role/profile/criteria/policy; kiểm tính hợp lệ và xác định phiên bản dùng trong session. | Nhu cầu override đã ghi; phạm vi override và hiệu lực hot reload là draft. |
| C11 · Vận hành và bảo trì chính Crew | Theo dõi session lỗi/chờ, quota/cost và outcome; recovery trạng thái; tương thích adapter; cập nhật fork, sync upstream và giữ trace lịch sử. | Crate riêng/móc mỏng đã chọn; thước đo, retention, nâng cấp state và regression cần thiết kế. |

## 3. Mô hình vận hành chung

Human → Orchestrator LLM nhận diện intent/session và tạo session plan → code kiểm và thực thi cycle/step → worker/evaluator tạo kết quả và evidence → Human quyết định phần cần thẩm quyền → xác nhận OUTPUT thực tế.

Session là đơn vị công việc đã ghi; `refine`/`implement` là ví dụ được xác nhận. Cycle/step definitions tái dùng và run gắn với input/version là mô hình tham khảo từ catalog cũ; taxonomy mới chưa chốt. Session plan không đồng nhất với plan một provider tự báo trong chat. Kết thúc turn hoặc test xanh không chứng minh OUTPUT đã đạt. Phân công song song theo dependency, kiểm chứng khác tác giả/context, trạng thái chờ/quota và tích hợp dùng cơ chế chung, không xây mỗi scenario một bộ engine riêng.

Nguồn ngữ cảnh: Issues cho spec/ticket, glossary cho nghĩa, ADR cho trade-off, run store cho trạng thái, log/transcript/diff cho evidence theo chính sách dữ liệu. Các khối nhận input đã có và trả kết quả/evidence; chỉ bên có quyền mới thực hiện mutation. Một danh tính/PAT rộng đã chấp nhận không tự chứng minh ranh giới quyền có hiệu lực.

## 4. Toàn cảnh kịch bản · INPUT / OUTPUT / ngoại lệ

Danh mục 15 kịch bản là "mọi kịch bản Human mong muốn" theo R7 (D15). Đối chiếu đủ 12 scenario cũ: kickoff → S02, exploration → S03, delivery → S04 + S05, change-request → S06, bug-fix → S07, release → S09, migration → S10, incident → S11, postmortem → S12, operate → S13, retrospective → S14, retirement → S15; thêm S01 onboarding (M11 discovery) và S08 dependency/security (primer). Refactor và nợ kỹ thuật đi qua S04/S05 như một feature, không thành kịch bản riêng (D15). Không tự quyết mọi dòng là một session type mới.

Mỗi OUTPUT phải là INPUT dùng được cho kịch bản kế tiếp trong hành trình §5. Chỉ OUTPUT của S04/S05 đã confirmed; OUTPUT các dòng khác là đề xuất BA để spec làm rõ.

| Nhóm | INPUT | OUTPUT | Ngoại lệ cần phủ | Trạng thái |
| --- | --- | --- | --- | --- |
| S01 · Onboard target-project | Repo/project có sẵn và quyền được cấp. | Baseline project: nguồn code/docs/tracker, capability, constraints và cấu hình đủ để Crew làm việc. | Doc lệch code, thiếu quyền. Target-project là repo public: Crew dừng ở gate và hỏi Human (M11 discovery, F1). | Draft từ M11 discovery/primer; nằm trong toàn cảnh. |
| S02 · Kickoff sản phẩm | Ý tưởng, nhu cầu, constraints và bằng chứng ban đầu. | Problem brief được xác nhận, mục tiêu/phạm vi toàn sản phẩm, hướng giải pháp, tiêu chí và backlog feature/Epic ban đầu trên Issues làm INPUT cho S04. | Sai vấn đề, nhiều ý tưởng mâu thuẫn, chưa đủ bằng chứng. | Draft tổng hợp workflow kickoff hiện có. |
| S03 · Nghiên cứu / exploration / spike | Câu hỏi hoặc giả thuyết có phạm vi. | Kết luận có nguồn hoặc thực nghiệm, phương án và giới hạn; prototype giữ đúng vai trò thử nghiệm. | Kết quả inconclusive, thiếu access, nhầm prototype là code production. | Draft dựa catalog và primer. |
| S04 · Định hình feature / refine | Feature/Epic từ S02 hoặc S06, hoặc nhu cầu Human nêu trực tiếp, kể cả refactor. | Spec trên Issues và các Story đạt Story Ready để giao implement; clarification → spec → tickets trong cùng context (F4). | Lựa chọn chưa giải quyết thì Story chưa Ready. | Story Ready confirmed (D8); recipe/hierarchy draft. |
| S05 · Implement Story Ready | Một Story đạt Story Ready và ngữ cảnh đã xác nhận. | PR của Story đó merged vào main; có kiểm chứng đúng phiên bản và Human approval tương ứng (D7, D10, D16, D17). | Story chưa Ready: không bắt đầu, báo phần thiếu và đề xuất chạy refine (D8). CI failed, conflict hoặc findings thì còn việc. Code đổi sau approval phải kiểm và duyệt bản mới. | INPUT/OUTPUT/approval/merge confirmed; cycle draft. |
| S06 · Đổi scope / change request | Baseline và thay đổi Human yêu cầu. | Quyết định thay đổi cùng impact; spec/backlog và phần việc chịu ảnh hưởng được đồng bộ có truy vết. | Công việc đang chạy, approval/check cũ, thay đổi đã publish. | Draft; cách pause/replan hoặc tách việc mới chưa chọn. |
| S07 · Bug / hotfix | Triệu chứng, báo cáo hoặc test thất bại. | Bằng chứng tái hiện/chẩn đoán; bản sửa và regression vào main theo hợp đồng OUTPUT của S05 (PR merged, approval đúng phiên bản). | Không tái hiện được, nhiều nguyên nhân, test flaky; hotfix không tự bỏ quyền Human. | Draft; dùng lại hợp đồng S05 là đề xuất BA. |
| S08 · Dependency upgrade / security fix | Version/advisory hoặc nhu cầu thay dependency. | Impact được đối chiếu; thay đổi vào main theo hợp đồng OUTPUT của S05, kèm regression/security evidence; phần cần release được xác định. | Breaking API, advisory không áp dụng, secrets, license hoặc disclosure cần specialist. | Draft biến thể delivery/bug từ primer; dùng lại hợp đồng S05 là đề xuất BA. |
| S09 · Release / deploy | Thay đổi đã đạt điều kiện, package, môi trường và quyền. | Bản phát hành được triển khai và xác nhận hoạt động theo tiêu chí; trạng thái rollout/rollback có evidence. | Lệnh deploy xong nhưng hệ thống lỗi; thiếu quyền hoặc rollback chưa thành công. | Draft; Crew chỉ thao tác môi trường khi Human cấp quyền theo project (D15). |
| S10 · Migration | Nguồn/đích, scope, quy tắc chuyển và quyền. | Trạng thái đích được đối soát toàn vẹn; kết quả chuyển/rollback và phần chưa đạt được ghi đúng. | Thiếu/trùng dữ liệu, cutover dở dang, nguồn tiếp tục được ghi. | Draft; hợp đồng dữ liệu/cutover theo project còn mở. |
| S11 · Incident / phục hồi | Sự cố có tác động và quyền xử lý được xác định. | Mức hoạt động mong muốn được phục hồi, có evidence và timeline; việc còn lại được chỉ rõ. | Thiếu access/owner, mitigation chưa đủ, cần hotfix hoặc rollback thất bại. | Draft; Crew chỉ thao tác môi trường khi Human cấp quyền theo project (D15). |
| S12 · Postmortem | Incident đã ổn định, timeline/logs và quyết định. | Nguyên nhân/yếu tố góp phần có bằng chứng; hành động phòng ngừa với owner đề xuất và cách theo dõi. | Không đủ evidence; chưa có người nhận trách nhiệm. | Draft; không giả lập incident hoặc đồng thuận. |
| S13 · Review vận hành / outcome | Một kỳ sử dụng, mục tiêu và dữ liệu/feedback thực tế. | Đánh giá hoạt động và giá trị; vấn đề/cơ hội, độ lệch mục tiêu và route tiếp theo. | Không có baseline/telemetry; đếm ticket không chứng minh giá trị. | Draft; đo Crew và target-project theo mục tiêu riêng. |
| S14 · Retrospective / cải tiến | Run logs, defects, checks và feedback đã có. | Cải tiến được đề xuất/chọn cùng cách đo; thay đổi playbook/rules có review và truy vết. | Suy luận từ summary, tự sửa rules hoặc gán owner chưa nhận. | Draft; retro không tự chặn OUTPUT implement đã xác nhận. |
| S15 · Retirement / decommission | Quyết định ngừng và phạm vi người dùng/dữ liệu/quyền. | Chuyển/lưu giữ dữ liệu theo quyết định, ngừng tài nguyên/quyền, xác nhận kết quả thực tế. | Dependency còn dùng, người dùng chưa chuyển, nghĩa vụ retention chưa rõ. | Draft; Crew chỉ thao tác môi trường khi Human cấp quyền theo project (D15). |

## 5. Hành trình liên kết toàn sản phẩm

- Project mới: kickoff (S02) → nghiên cứu/discovery (S03) → backlog feature/Epic → refine (S04) → Story Ready → implement (S05) → PR merged → release khi có mục tiêu/quyền (S09) → review vận hành (S13) → cải tiến (S14).
- Project đang có: onboarding/kiểm capability (S01) → yêu cầu feature, refactor hoặc lỗi → refine (S04) hoặc reproduce/diagnose (S07) → thay đổi được kiểm chứng/tích hợp theo hợp đồng S05 → chuyển giao theo mục tiêu.
- Sự cố: triage → mitigation/recover (S11) → verify phục hồi; bản sửa/hotfix qua S07 và release theo quyền; sau ổn định postmortem (S12) → hành động phòng ngừa.
- Đổi scope/migration/retirement (S06/S10/S15): impact và quyết định → plan theo phạm vi → thao tác được ủy quyền → verify/đối soát → ghi trạng thái thực tế; chưa đạt thì không ghi completed.

Các tuyến trên là draft ở mức hành trình, không ép mọi yêu cầu bắt đầu kickoff, không trộn sự kiện Scrum với session/cycle. Ngoại lệ của một session không được bù bằng giả định đã đạt input contract.

## 6. Business rules đã chốt và ngoại lệ còn thiết kế

- Human chỉ ra lệnh qua Zed, nói với Orchestrator; code giữ trạng thái/quyền/dependency/OUTPUT; run store không lấy trạng thái từ lời tự báo trong chat (R5, D11).
- Crew là bên duy nhất ghi lên Issues. Human vẫn sửa issue trên web: Crew phát hiện lệch trước mỗi step và báo Human, không âm thầm ghi đè (R5, mặc định M6 discovery); pause/replan khi lệch chưa được Human chọn.
- Crew không có nút Approve/Re-run; câu hỏi cho Human nằm trong hội thoại (intake F6).
- OUTPUT refine = Story Ready. Implement chỉ nhận Story Ready đủ dữ liệu; Story chưa Ready thì không bắt đầu, báo phần thiếu và đề xuất refine, không thêm refine trong implement để bù (D8).
- Mỗi Story có một integration branch; worktree của Story được Crew tích hợp vào đó; mỗi Story có một PR vào main (R4, D16).
- OUTPUT implement = PR của Story merged vào main. CI failed, conflict hoặc findings còn mở thì session chưa đạt OUTPUT (D7).
- Human duyệt PR qua GitHub PR review. Crew merge ngay sau khi kiểm phiên bản PR (head commit) trùng phiên bản Human đã duyệt; không trùng thì không merge (D10, D17).
- Approval gắn với phiên bản Human đã xem. Sửa code sau approval phải check lại và Human duyệt phiên bản mới (D10).
- Worker làm trong worktree riêng; ngoài worktree qua Crew/gate. Human vắng: phần phụ thuộc chờ, phần độc lập tiếp tục. Quota cạn: dừng phần liên quan và hỏi Human, không tự đổi provider (R2, R6).
- Target-project là repo public: Crew dừng ở gate và hỏi Human (F1, M11 discovery).
- G6 cho chính Crew: tiêu chí từ yêu cầu, kiểm chứng độc lập cả code/test, chạy luồng/tích hợp thật, chất lượng phi chức năng và HTML evidence đúng version. Không tự áp chất lượng Crew lên target-project, project có tiêu chí riêng.

**"CI" trong D7:** mặc định M7 discovery (tắt Actions) dựa trên tiền đề repo Crew chuyển private; F1 giữ Crew public nên tiền đề không còn. Actions đang bật (§10). Bộ check bắt buộc trên PR là việc Developers xác định, không chờ Human.

Cần thiết kế theo toàn cảnh: invalid input/wrong repo, scope drift, stale outputs, permission bị từ chối, auth/provider thiếu capability, timeout/cancel/result trễ, partial failure, restart sau mutation, conflict/rework và retention. Cách hủy/dừng không được gán thành đã đạt OUTPUT. Không phát lại side effect mù; cần đối soát và validation. PAT rộng giữ quyết định đã có.

## 7. Chất lượng production-ready toàn sản phẩm

Khung G6 đã confirmed; bảng sau ánh xạ đề xuất theo primer, không phải checklist bắt buộc do ISO ban hành. Mỗi đặc tính có scope/tiêu chí/ngưỡng/evidence hoặc lý do không áp dụng; chưa có threshold/checks chi tiết được xác nhận.

| Đặc tính | Bằng chứng cần định nghĩa |
| --- | --- |
| Đúng chức năng | Acceptance scenarios từ yêu cầu; evaluator độc lập; E2E thật gồm luồng chính/ngoại lệ. |
| Hiệu năng và tài nguyên | Đo latency, tài nguyên, token/cost trên workload xác định; ngưỡng theo mục tiêu. |
| Tương thích | Kiểm đúng version Zed/GitHub/ba ACP và công cụ project; capability mismatch được báo. |
| Khả năng tương tác | Human hiểu và quyết định được; keyboard/focus, chờ/lỗi/empty và HTML vừa đủ. |
| Độ tin cậy | Timeout/restart/kết quả trễ/partial failure; đối soát side effect trước retry. |
| Bảo mật | Kiểm input không đáng tin, secret/log/data boundary và hiệu lực quyền thật. |
| Khả năng bảo trì | Review kiến trúc/ADR, boundary crate, regression và một lần sync upstream có evidence. |
| Tính linh hoạt | Kiểm provider/project override còn giữ hợp đồng và bất biến; version cấu hình xác định. |
| An toàn thao tác | Chặn thao tác vượt phạm vi, giữ đúng thẩm quyền; rollback/recovery được xác nhận khi áp dụng. |

Spec/acceptance examples là nguồn expected behavior, không suy ngược từ code. Tác giả có thể viết unit tests cho feedback; evaluator khác context phải đánh giá cả test/code, hành vi thiếu và khả năng bắt lỗi đại diện. Reviewer/model khác vẫn có thể cùng giả định sai, nên cần evidence tích hợp thật và trải nghiệm Human ở review hiện có. Không giả lập feedback, không mở gate duyệt từng testcase. Khung này chưa tự thành Scrum DoD hoặc chứng nhận ISO; chưa chạy Crew E2E mới.

## 8. Test seams đề xuất · bắt được và bỏ sót

Seams sau là đề xuất của BA để Developers/QA xác nhận hoặc thay; chưa được chọn signature hay viết test. Lựa chọn abstraction (ví dụ có tách adapter tracker khi mới chỉ có GitHub hay không) là quyết định thiết kế của Developers.

| Bề mặt | Bắt được | Không chứng minh |
| --- | --- | --- |
| Hội thoại và UI Crew | Intent → session/plan → câu hỏi/trạng thái/OUTPUT; trải nghiệm Human và luồng đầu cuối. | Không tự chứng minh sự đúng đắn bên trong adapter hoặc mọi hành vi của model. |
| Giao diện công khai điều khiển session | Input/dependency/order/join/wait/verdict/version, rework và đối soát; expected result từ spec. | Fake adapter không chứng minh role/tool/cancel/quota thật. |
| Ba adapter ACP thực tế | Role/skills/capability, cwd, prompt, permission, cancel đang chạy và resume; đúng version/profile. | Probe nhỏ không chứng minh toàn lifecycle hoặc chất lượng code sinh ra. |
| Git/GitHub và công cụ project thực tế | Worktree/integration/CI/PR/merge, kiểm phiên bản trước merge, lỗi/retry/đối soát ghi; release/migration khi có quyền và môi trường thử. | Không chứng minh Human mong muốn đúng hoặc cấp quyền production. |

## 9. Toàn cảnh trước, Epic triển khai sau

Toàn cảnh gồm toàn bộ C01–C11 và S01–S15, cùng chất lượng/operability/boundaries. Các năng lực chưa nằm trong Epic đầu vẫn ở trong thiết kế sản phẩm. Không dùng “ngoài lát đầu” để hoãn việc mô tả chúng.

Epic phải truy về hành trình/OUTPUT và khối năng lực trong bản đồ; có acceptance và bằng chứng chất lượng. Mỗi Epic nhỏ đủ để triển khai/sử dụng; PO quyết định ordering, Developers làm sâu và size công việc. Build đầu tiên là lựa chọn thứ tự đã ghi, không phải toàn bộ spec. Không tạo lịch, estimate hoặc danh sách ticket để coi là đã lập roadmap được đồng ý.

## 10. Trạng thái và blockers của toàn cảnh

**Draft toàn cảnh, chưa complete/ready-to-build.** Không còn câu hỏi sản phẩm chờ Human trong bản này: danh mục kịch bản, PR mỗi Story và người merge đã chốt (D15–D17). Các gap dưới đây chủ yếu cần thiết kế hoặc validation; chỉ trở thành câu hỏi Human khi kết quả buộc chọn một trade-off sản phẩm.

| Nhóm gap | Điều còn thiếu | Người xử lý đề xuất, chưa nhận |
| --- | --- | --- |
| OUTPUT ngoài S04/S05 | OUTPUT S01–S03, S06–S15 là đề xuất BA, gồm việc S07/S08 dùng lại hợp đồng S05; cần làm rõ ở spec từng nhóm. | BA + Human/PO khi spec nhóm đó, owner đề xuất |
| Quyền và dữ liệu | Enforcement ngoài worktree với quyền thật; main chưa có branch protection/ruleset nên "Human duyệt trước merge" hiện chỉ dựa vào Crew kiểm phiên bản; dữ liệu gửi/lưu/publish/retention và release/migration authority theo project chưa rõ. | Developers/Security + Human/người giữ dữ liệu, owner đề xuất |
| Recovery và provider parity | Role/auth/cancel/compaction ba ACP và đối soát restart sau side effect chưa được chứng minh đầy đủ. | Developers/Architect/QA, owner đề xuất |
| Chất lượng đo được | G6 là khung đã chọn; ngưỡng/checks/seams, bộ check CI bắt buộc và bằng chứng áp dụng cho toàn sản phẩm chưa hoàn tất. | Developers/QA + Human cho trade-off trải nghiệm, owner đề xuất |
| Cấu hình và lifecycle | Taxonomy session/run, hierarchy backlog (Epic/Story/Task trên Issues), override/version, hủy, replan khi lệch issue và quản lý lịch sử còn draft. | BA/Developers/UX theo phạm vi, owner đề xuất |

**Evidence thực sự đã đọc:** intake Q1–Q3/F1–F11, discovery R1–R9/F1/M1–M11, playback, synthesis, primer §3/§5, landscape; catalog cũ 12 scenario/33 cycle và UI/worker/config; roles trong SDLC hiện có. Probe ACP 10/10 là evidence cũ với giới hạn role/auth/cancel/recovery; không chạy lại. Ngày 2026-10-11 đã truy vấn `gh api repos/Riso-application-factory/zed-ai-crew/actions/permissions` → `enabled: true`, `.../rulesets` → `[]`; lượt trước ghi classic branch protection main trả 404. Không xác nhận sản phẩm đã có toàn bộ các năng lực trên, không benchmark/user test/probe mới, không liên hệ cộng tác viên hoặc tạo spec/ticket.

**Next route:** tổng hợp spec trong cùng context, bắt đầu từ business rules và acceptance criteria của mô hình vận hành chung (§3, §6) và S04/S05. HLD/LLD chỉ cho câu hỏi kiến trúc/interface/state cụ thể; không làm thiết kế kỹ thuật chi tiết thay cho hành vi.

## 11. Sổ quyết định đã có · giữ nguyên câu trả lời

| ID | Quyết định / giới hạn | Nguồn |
| --- | --- | --- |
| D0 | Vấn đề, người hưởng lợi và ranh giới đã xác nhận qua playback. | [Problem brief](../discovery/problem-brief.md) |
| D1 | Mỗi Epic là một lát nhỏ đủ để triển khai và sử dụng, áp dụng cho lát đầu và lát N. | Human, câu trả lời 1 trong hội thoại shaping |
| D2 | Human nói với Orchestrator “triển khai story #1111” → Orchestrator xác định session `implement` → tạo session plan → chạy các cycle theo thiết kế → output. | Human, câu trả lời 2 trong hội thoại shaping |
| D3 | Hội thoại Zed, ba provider ACP, worktree riêng; ngoài worktree qua Crew/gate; phần độc lập tiếp tục khi Human vắng. | [Synthesis §2](../discovery/synthesis.md), R1/R2 |
| D4 | Crew tích hợp vào integration branch; Human duyệt PR vào main. Issues giữ spec/ticket, Crew là bên duy nhất ghi Issues; run store giữ trạng thái thực thi. | Synthesis R4/R5, intake Q1 |
| D5 | Hết quota dừng phần liên quan và hỏi Human; không tự đổi provider. Chỉ máy macOS này và project local. | Synthesis R6/R9 |
| D6 | Crew public, target-project private; onboarding ngoài lát đầu. Chất lượng production cho từng lát, mục tiêu dài hạn phủ các kịch bản. | Synthesis F1/R7; intake Q3 |
| D7 | Session kết thúc khi đạt OUTPUT. OUTPUT của session `implement` là PR merged vào main; mở PR chưa đạt OUTPUT, vì CI vẫn có thể failed. | Human, câu trả lời 3 trong hội thoại shaping |
| D8 | OUTPUT của `refine` là Story Ready. `implement` chỉ nhận Story Ready có đủ đầu vào; không dùng refine trong implement để bù thiếu dữ liệu. | Human, câu trả lời 4 trong hội thoại shaping |
| D9 | Chuỗi cycle đề xuất ở F4 giữ là draft; khi triển khai cần đi sâu hơn để làm đúng. Không ghi là recipe đã chốt. | Human, câu trả lời 5 trong hội thoại shaping |
| D10 | Approval gắn với phiên bản Human đã xem. Code đổi sau approval thì chạy lại check liên quan và đưa phiên bản mới cho Human duyệt trước merge. | Human đồng ý câu 6 trong hội thoại shaping |
| D11 | Đồng ý hướng lai: Orchestrator LLM hiểu yêu cầu và tạo session plan; code kiểm tính hợp lệ, giữ trạng thái và điều khiển thực thi theo dependency, quyền và OUTPUT. | Human đồng ý câu 7 trong hội thoại shaping |
| D12 | Luật hỏi (quy trình làm việc, không phải quyết định sản phẩm): có mục tiêu rõ và một bộ câu hỏi gốc; câu phụ phải chờ hết bộ gốc, được xét lại theo mọi câu trả lời và loại khi không còn cần thiết. Đi lớn → nhỏ, có điểm dừng. | Human yêu cầu trong hội thoại shaping |
| D13 | Đồng ý khung G6 sửa cho Crew: tiêu chí từ yêu cầu, kiểm chứng độc lập cả code/test, chạy luồng và tích hợp thật, chất lượng phi chức năng theo scope, HTML bằng chứng đúng version và Human trải nghiệm phần cần phán đoán trong review hiện có. | Human trả lời “đồng ý” cho bản sửa G6 |
| D14 | Toàn cảnh sản phẩm production-ready phải được định hình trước; Epic là cách chia triển khai, không thu nhỏ toàn cảnh thành lát đầu. | Human sửa cách hiểu trong hội thoại shaping |
| D15 | Danh mục S01–S15 là các kịch bản Human mong muốn (R7). Refactor/nợ kỹ thuật đi qua S04/S05. Với S09–S11/S15, Crew chỉ thao tác lên môi trường của target-project khi Human cấp quyền theo từng project. | Human “đồng ý” khuyến nghị review, 2026-10-11 |
| D16 | Mỗi Story có một integration branch; các worktree của Story tích hợp vào đó; mỗi Story một PR vào main. | Human “đồng ý” khuyến nghị review, 2026-10-11 |
| D17 | Human duyệt PR qua GitHub PR review; Crew merge ngay sau khi kiểm head commit trùng phiên bản đã duyệt. | Human “đồng ý” khuyến nghị review, 2026-10-11 |

## 12. Thư viện cycle và các góc nhìn có sẵn · input nghiên cứu

- **Nhận yêu cầu và định nghĩa:** `intake`, `clarify`, `discovery`, `research`, `spike`, `synthesize`, `vision-scope`, `requirements`, `design`, `contract`.
- **Chuẩn bị và triển khai:** `decompose`, `prioritize`, `refine`, `plan`, `implement`, `verify`, `integrate`, `review`, `impact`, `deliverable-update`.
- **Khắc phục và chuyển giao:** `reproduce`, `diagnose`, `package`, `deploy`, `migrate`, `recover`.
- **Vận hành, học và kết thúc:** `triage`, `measure`, `evaluate`, `improve`, `decommission`, `readiness`, `close`.

33 cycle này không phải recipe mới đã được chốt. `pull-request` trong draft implement là nhóm hành vi đã đề xuất, không tự được tính là cycle thứ 34. Catalog cũ dùng scenario; yêu cầu mới dùng session. Chưa nhập taxonomy cũ vào glossary.

23 góc nhìn hiện có trong SDLC: Coordinator / Orchestrator, Sponsor / Business Owner, Product Manager, Product Owner, Business Analyst, UX Researcher / Designer, UI Designer, Developers · Scrum, Software Engineer, QA / Test Engineer, Tech Lead, Software / Solution Architect, Platform / DevOps Engineer, Site Reliability Engineer, Security Engineer / AppSec, Engineering Manager, Project / Delivery Manager, Scrum Master, Agile Coach, Release Manager, Customer / End User, Support / Customer Success, Legal / Compliance / Privacy. Đây là role prompts, không phải 23 người đã tham gia hoặc 23 worker bắt buộc. Human vẫn giữ quyền đã ghi; AI không thay accountability hay specialist approval.

## Nguồn

[Input intake](../intake-brief.html) · [Bản ghi discovery](../discovery/questions.html) · [Playback](../discovery/problem-brief.md) · [Synthesis](../discovery/synthesis.md) · [Domain primer](../research/domain-primer.md) · [Technical landscape](../research/technical-landscape.html) · [SDLC](../sdlc.html) · [Sổ interview cũ](questions.md) · [Glossary](../../GLOSSARY.md) · [ADR-0001](../../docs/adr/0001-hybrid-orchestration.md).

Thiết kế cũ chỉ là input: [12 scenario](../../../zed-scrum-team/old-specs/sdlc/scenarios.html) · [33 cycle](../../../zed-scrum-team/old-specs/sdlc/cycles.html) · [UI](../../../zed-scrum-team/old-specs/orchestrator/ui-design.html) · [worker](../../../zed-scrum-team/old-specs/worker/spec.html) · [configuration](../../../zed-scrum-team/old-specs/sdlc/settings-design.html). Không giữ backlog.db hoặc native-worker contract cũ khi tracker/worker mới đã khác.
