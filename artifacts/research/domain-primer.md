# Domain primer · Zed AI Crew

**Draft · Nghiên cứu đã tổng hợp, chờ discovery xác nhận giả thuyết.** Business Analyst · 2026-10-10. [Intake đã chốt](../intake-brief.html) là baseline. Đây là tài liệu nghiên cứu gốc; [HTML](domain-primer.html) là bản xem. Không phải spec, quyết định kiến trúc, DoD đã thông qua hay bằng chứng sản phẩm đã chạy.

## 1. Điều cần mang vào buổi discovery

- **Đã chốt trong intake:** một Human dùng và vận hành fork Zed; GitHub Issues là tracker; Claude, Codex và Antigravity qua ACP; giao theo lát dọc đạt chất lượng production; thiết kế cũ chỉ là input nghiên cứu. Không mở lại Q1–Q3. [Intake §1–3](../intake-brief.html)
- **Quan sát:** catalog cũ đã bao phủ 12 nhóm công việc và 33 cycle. “Phủ 100%” cần một phạm vi có ranh giới và các tình huống ngoại lệ; số recipe không chứng minh độ phủ. Đây là suy luận BA từ [catalog scenario](../../../zed-scrum-team/old-specs/sdlc/scenarios.html) và [cycle](../../../zed-scrum-team/old-specs/sdlc/cycles.html).
- **Kết luận nghiên cứu:** nhiều sản phẩm đã có delegation, spec hoặc PR review; chưa có bằng chứng trong tập nguồn đã đọc rằng ghép các vai AI thành Scrum team sẽ bảo đảm kiến trúc đúng hoặc giảm thời gian duyệt tài liệu. Xem bảng DR2 và giới hạn nghiên cứu ở §4.
- **Đề xuất:** tách kiểm chứng tự động, đánh giá chuyên môn và quyết định Human. Một test pass không đủ để chốt giá trị sản phẩm; một LLM reviewer đồng ý không phải Human approval. Cơ sở tham khảo: [Scrum Guide](https://scrumguides.org/scrum-guide.html), [NIST SSDF](https://csrc.nist.gov/pubs/sp/800/218/final); vị trí gate cụ thể còn mở ở §7.
- **Đề xuất:** biến những ràng buộc kiến trúc kiểm được thành check; phần phán đoán giữ trong review. Có ví dụ kỹ thuật ở [ArchUnit](https://www.archunit.org/userguide/html/000_Index.html), nhưng chưa chứng minh hiệu quả trên Rust/GPUI của Crew.
- **Unknown quan trọng:** sản phẩm đích tạo doanh thu thế nào, loại dữ liệu nào được gửi ra ngoài, jurisdiction, mức tự động được ủy quyền và ngưỡng “artifact ngắn”. Không suy các câu trả lời này từ public research. [Intake](../intake-brief.html)

**Cách đọc bằng chứng:** “Quan sát” = nội dung file đã đọc; “Nguồn ngành” = quy định/phương pháp hoặc khả năng do tác giả công bố; “Thực nghiệm” = kết quả trong mẫu nghiên cứu cụ thể; “Giả thuyết/đề xuất” = suy luận BA, chưa được khách hàng xác nhận. Tin cậy cao cho nội dung nguồn, không tự chuyển thành tin cậy cao về hiệu quả tại Crew. Mọi nguồn web được đọc trong phiên 2026-10-10; tài liệu live không được pin version.

## 2. Domain, mô hình giá trị và hành trình hiện tại

**Domain làm việc: công cụ phát triển phần mềm có AI, điều phối công việc từ yêu cầu tới thay đổi đã kiểm chứng.** Đây là phân loại BA từ mục tiêu intake, không phải ngành kinh doanh của mọi target-project. Crew nằm giữa Human, playbook, agent provider và ngữ cảnh dự án; người nhận giá trị cuối của phần mềm đích có thể khác Human đang dùng Crew. [Intake §2, stakeholder map](../intake-brief.html)

**Mô hình kinh tế khách hàng chưa đủ dữ kiện.** Intake nêu nhu cầu giảm copy prompt, code khó bảo trì và artifact lan man; chưa nêu doanh thu của Human hay mô hình thu phí Crew. Giả thuyết giá trị là giảm thời gian thao tác, rework và chi phí đọc/duyệt; cần đo cùng chất lượng thay vì chỉ đếm ticket đóng. Subscription trong C7 là bối cảnh chi phí do Human khai báo, không chứng minh quota của Codex thấp nhất. [Intake C7, E1](../intake-brief.html)

**Tham chiếu kinh doanh upstream:** Zed công bố các gói trả phí trên [trang pricing](https://zed.dev/pricing); external agents có quan hệ billing và dữ liệu riêng với provider theo [tài liệu Zed](https://zed.dev/docs/ai/external-agents). Điều này mô tả upstream và ranh giới nhà cung cấp, không xác nhận Human kinh doanh theo mô hình subscription.

| Tác nhân | Quan hệ với công việc | Điều chưa suy được |
| --- | --- | --- |
| Human | Sponsor, PO, khách hàng, người dùng và người vận hành theo intake; quyết định và duyệt gate. | Không mặc định đã ủy quyền publish/deploy/migration cho AI. [Intake stakeholder map](../intake-brief.html) |
| Orchestrator, Worker | Tác nhân AI được kỳ vọng điều phối/thực thi theo playbook. | Role prompt không tạo Scrum accountability hay quyền quyết định tổ chức. [Intake](../intake-brief.html), [Scrum Guide](https://scrumguides.org/scrum-guide.html) |
| Người dùng của target-product | Người chịu tác động của thay đổi sản phẩm đích; đây là phân biệt thuật ngữ đề xuất. | Danh tính, nhu cầu và dữ liệu thực tế cần discovery. |
| GitHub, ACP providers | Tracker và nền tảng thực thi bên ngoài trong phạm vi đã chốt. | Khả năng adapter, điều khoản gói và xử lý dữ liệu cần kiểm tra theo phiên bản/tài khoản. [Intake F1, TD1/TD8](../intake-brief.html) |
| Upstream Zed | Nguồn thay đổi phải sync; C6 yêu cầu crate riêng và móc mỏng. | Không chọn seam hoặc cơ chế tích hợp tại bước BA. [Intake C6](../intake-brief.html) |

**Walkthrough tài liệu, không phải phiên dùng sản phẩm đã chạy:** đã đọc HTML intake, cấu hình workflow trong SDLC và UI-design cũ. Hành trình tài liệu là chọn luồng → chọn node/role → lấy System và Request prompt → mở thread/dán prompt theo mô tả intake → đọc kết quả → Human chuyển ngữ cảnh và duyệt. Có bằng chứng về cấu trúc prompt trong file; không quan sát trực tiếp thao tác của Human hay đo thời gian. [SDLC](../sdlc.html), [Intake F2–F4](../intake-brief.html)

**Hành trình mong muốn, chưa phải hành vi quan sát:** Human gửi yêu cầu, trả lời clarification và duyệt trong hội thoại; pipeline dưới chat mở thành graph, run log ở tab giữa. Mẫu UI cũ vẫn là hình mẫu được intake giữ lại; contract và implementation cũ không trở thành canonical. [Intake Q2, F6](../intake-brief.html), [UI-design](../../../zed-scrum-team/old-specs/orchestrator/ui-design.html)

**Lệch nguồn:** intake F3 ghi brainstorm feature 4 bước; `workflows` trong SDLC hiện có 5 mục: context, options, validation, decision, specification. Kickoff có 9, build có 5. Cần phân biệt “4 bước brainstorm” với “5 bước gồm spec”; không tự sửa intake. [SDLC, khai báo workflows](../sdlc.html)

## 3. DR1 · Công việc đầu cuối và ngoại lệ

Scrum quy định accountabilities, events, artifacts và commitments, không cung cấp một danh mục đầy đủ các playbook kỹ thuật. Sprint Review phục vụ inspect/adapt, không phải release gate; Increment phải đạt DoD. Không ép nghiên cứu, incident hoặc một phiên AI thành Sprint. [Scrum Guide](https://scrumguides.org/scrum-guide.html)

Bảng dưới là **mô hình tổng hợp BA đề xuất**, đối chiếu [12 scenario cũ](../../../zed-scrum-team/old-specs/sdlc/scenarios.html), [33 cycle](../../../zed-scrum-team/old-specs/sdlc/cycles.html) và [3 luồng hiện tại](../sdlc.html). Dấu “có” chỉ nghĩa có mô tả, không nghĩa đã triển khai hay xác minh.

| Nhu cầu / trigger | Hành trình kết quả đề xuất | Ngoại lệ cần nhận diện | Độ phủ tài liệu |
| --- | --- | --- | --- |
| Khởi tạo sản phẩm | Intake → nghiên cứu/discovery → xác nhận vấn đề → yêu cầu → định hình → spec. | Sai vấn đề; chưa có chủ thể quyết định; nhiều ý tưởng mâu thuẫn. | Hiện có kickoff; cũ kickoff. |
| Brownfield onboarding | Kiểm kê hành vi/capability đang có → tìm ràng buộc → xác nhận baseline. | Doc khác code; hệ thống không chạy được; thiếu quyền/data. | Cũ có exploration/kickoff, chưa có route onboarding tường minh; đề xuất specialization, chưa cần scenario mới. |
| Feature / refactor / nợ kỹ thuật | Làm rõ → phân rã → thay đổi → verify → tích hợp → review. | Regression, xung đột, integration fail, scope đổi. | Hiện feature/build; cũ delivery. |
| Đổi định hướng / hủy yêu cầu | Phân tích tác động → quyết định scope → đồng bộ baseline và công việc liên quan. | Việc đã chạy/đã publish; thay đổi hết hiệu lực. | Cũ change-request; hiện chưa có luồng riêng. |
| Bug | Tái hiện → chẩn đoán → sửa → kiểm chứng hồi quy. | Không tái hiện được; nhiều nguyên nhân; test flaky. | Cũ bug-fix; hiện có step debug nhưng chưa có recipe đầu cuối tương đương. |
| Spike / đánh giá khả thi | Câu hỏi và giới hạn → thử/đọc → đối chiếu bằng chứng → kết luận. | Không đủ bằng chứng; prototype bị nhầm với code production. | Cũ exploration; hiện research/prototype là nhánh điều kiện. |
| Dependency upgrade / security fix | Kiểm kê ảnh hưởng → phân loại rủi ro → thay đổi và regression → release nếu cần. | Breaking API, license mới, advisory không khớp phiên bản, credential lộ. | Cũ delivery/bug/release chứa một phần; thiếu route security disclosure và dependency-specific rõ ràng. [NIST SSDF](https://csrc.nist.gov/pubs/sp/800/218/final) |
| Incident / hotfix | Triage ảnh hưởng → mitigation/restore → verify phục hồi → ghi timeline; nguyên nhân và phòng ngừa sau ổn định. | Không có quyền production; rollback thất bại; cần hotfix; chưa phục hồi. | Cũ incident + postmortem + bug/release. [Google SRE](https://sre.google/workbook/incident-response/) |
| Release | Package → kiểm tra điều kiện → rollout → kiểm tra từng đợt → ghi trạng thái. | Deploy thành công nhưng hệ thống lỗi; rollback; upstream sync phá build. | Cũ release; PR của build hiện tại chưa đồng nghĩa release. |
| Migration | Impact → cutover/recovery plan → chuyển từng scope → đối soát → kết luận. | Đổi dở dang, mất/trùng dữ liệu, nguồn còn được ghi. | Cũ migration; cần làm rõ tiêu chí dữ liệu theo target-project. |
| Review vận hành / cải tiến | Đo → tổng hợp → so mục tiêu → chọn cải tiến và theo dõi. | Không có telemetry; dữ liệu thiên lệch; đề xuất chưa có owner. | Cũ operate/retrospective; hiện retro sau build. |
| Retirement | Xác nhận ngừng → chuyển người dùng/dữ liệu → ngừng tài nguyên/quyền → verify. | Nghĩa vụ lưu giữ; người dùng chưa chuyển; phụ thuộc còn dùng nguồn. | Cũ retirement; tham chiếu [GOV.UK retirement](https://www.gov.uk/service-manual/agile-delivery/retiring-your-service). |

**Lỗ hổng xuyên luồng cần stress-test, không phải kết luận code đang lỗi:** Human vắng mặt tại gate; provider hết quota/timeout; cancel rồi resume; restart giữa hai side effect; worker trả kết quả sai phiên bản; song song trên baseline cũ; issue không đáng tin; phần việc thành công còn phần khác thất bại. Catalog cũ có Clarify, Verify, Readiness, Recover và Close, nhưng sự hiện diện tên cycle chưa chứng minh xử lý được các tình huống này. [Cycle cũ](../../../zed-scrum-team/old-specs/sdlc/cycles.html), [Intake TD1/TD3/TD5/TD8](../intake-brief.html)

**Kết luận DR1:** chưa có cơ sở gọi catalog 12/33 là “100% mọi kịch bản ngoài đời”. Đề xuất dùng các nhóm trên làm phạm vi trao đổi; security/dependency và onboarding có thể là biến thể. PO xác nhận phạm vi target-project; Developers xác minh trạng thái kỹ thuật. Không tạo backlog hoặc xếp thứ tự ở đây.

## 4. DR2 · Sản phẩm cạnh tranh và tham chiếu

**Desk research · Tin cậy cao về nội dung nguồn, chưa kiểm hành vi runtime.** Các capability bên dưới là công bố trong docs live; không chứng minh adapter ACP có cùng năng lực. Phần Codex đối chiếu official OpenAI documentation. Bảng chỉ gồm mẫu tham chiếu được intake yêu cầu, không phải market sizing hoặc khảo sát toàn thị trường.

| Tham chiếu | Điều phối và artifacts — quan sát tài liệu chính thức | Gate được mô tả và giới hạn | Điều đáng học cho discovery — suy luận |
|---|---|---|---|
| Claude Code subagents / agent teams | Subagent có context, prompt và tools riêng, trả kết quả về caller. Teams có shared tasks và agent messaging, đang experimental, mặc định tắt. [Subagents](https://code.claude.com/docs/en/sub-agents), [Teams](https://code.claude.com/docs/en/agent-teams). | Docs Teams hiện ghi **plan approval tự động trong lead session**, không phải human sign-off; permission prompts vẫn đến người dùng. Teams tăng token và coordination overhead. [Teams](https://code.claude.com/docs/en/agent-teams). | Role labels và nhiều agent chưa đủ tạo accountability. Cần phân biệt review nội dung với quyền thực thi. |
| Codex | Subagents xử lý các phần độc lập, parent gom summaries; custom agents cấu hình instructions/model. Docs cảnh báo parallel edits gây conflicts. [Subagents](https://learn.chatgpt.com/docs/agent-configuration/subagents). Cookbook minh họa handoff dựa trên files tồn tại và yêu cầu brief ngắn. [Cookbook](https://developers.openai.com/cookbook/examples/codex/codex_mcp_agents_sdk/building_consistent_workflows_codex_cli_agents_sdk). | Sandbox giới hạn phạm vi; approval policy quyết định lúc xin phép. Đây là quyền hành động, không phải phê duyệt giá trị sản phẩm. Cookbook là ví dụ với approval-policy never, gate chủ yếu kiểm tra artifacts tồn tại. [Security](https://learn.chatgpt.com/docs/agent-approvals-security), [Cookbook](https://developers.openai.com/cookbook/examples/codex/codex_mcp_agents_sdk/building_consistent_workflows_codex_cli_agents_sdk). | “Có file” chỉ là prerequisite; cần kiểm tra nội dung và tiêu chí bằng chứng riêng. |
| GitHub Copilot coding/cloud agent | Issue/chat/PR comment → branch → sửa code, tests/linters → PR → phản hồi và sửa tiếp; môi trường ephemeral. [GitHub surface](https://docs.github.com/en/copilot/concepts/copilot-surfaces/copilot-on-github). | PR-triggered Actions cần người có write access approve; cloud agent không push trực tiếp default branch. GitHub yêu cầu review/test trước merge; báo cáo signed commit/session logs là traceability, không chứng minh business acceptance. [Application card](https://docs.github.com/en/copilot/responsible-use/agents). | Handoff bám issue/PR và logs là tham chiếu tốt cho bằng chứng kiểm chứng. Không suy ra tổ chức đã chấp thuận release. |
| GitHub Spec Kit | Spec → plan → tasks → implement/converge; clarify giải quyết ambiguity; analyze tìm mâu thuẫn các artifacts. [Quickstart](https://github.com/github/spec-kit/blob/main/docs/quickstart.md). | Custom checklist thuộc reviewer, kiểm tra **requirements**, không kiểm tra code hoàn thành. Implement hỏi trước khi tiếp tục nếu checklist còn unchecked. Analyze read-only, cần explicit approval cho remediation editing. [Checklist](https://github.com/github/spec-kit/blob/main/templates/commands/checklist.md), [Analyze](https://github.com/github/spec-kit/blob/main/templates/commands/analyze.md), [Quickstart](https://github.com/github/spec-kit/blob/main/docs/quickstart.md). | Tránh lấy dấu tick requirement-quality làm bằng chứng Increment usable. Artifacts liên kết làm giảm drift nhưng thêm chi phí duy trì. |
| BMad Method | Docs hiện chọn planning theo intent/risk; spec có Why, Capabilities, Constraints, Non-goals, Success signal; companions giữ phần sâu, spec dẫn link. [Spec](https://docs.bmad-method.org/plan/define-requirements-and-a-specification/). | Hướng dẫn tổ chức đặt human sign-off ở PRFAQ, PRD validation, architecture, breakdown và epic retrospective; áp dụng approvals hiện có. Đây là phương pháp đề xuất của BMad, không phải gate Scrum hoặc gate của khách hàng đã xác nhận. [Organization](https://docs.bmad-method.org/plan/plan-inside-an-organization/). | “Short contract + references” đáng đối chiếu nhu cầu chống artifacts dài. Không bê nguyên gate tổ chức vào nhóm nhỏ. |
| Kiro | Requirements/design/tasks là ba artifacts. Standard specs review từng phase; Quick Spec hỏi upfront rồi tạo cả ba **không gate giữa phases**. [Specs](https://kiro.dev/docs/specs/), [Quick Spec](https://kiro.dev/docs/specs/quick-spec/). Design-First có lựa chọn detail level. [Design-First](https://kiro.dev/docs/specs/feature-specs/tech-design-first/). | Quick Spec giữ artifacts và cho review sau; do vậy ít interruptions không đồng nghĩa bỏ traceability. Standard review là nội dung spec, không thay thế permission policy. [Quick Spec](https://kiro.dev/docs/specs/quick-spec/). | Một workflow có thể điều chỉnh review cadence theo uncertainty; cần hỏi khách hàng decision nào thật sự cần dừng. |
| MetaGPT | SOP chia vai product manager, architect, project manager, engineer; đầu vào requirement, đầu ra stories, analysis, APIs/docs/code. [Repo](https://github.com/FoundationAgents/MetaGPT). | Paper nêu khó interrupt/checkpoint từng agent và chưa đáp ứng đủ UI/frontend/nhu cầu ứng dụng thực phức tạp. Đây là giới hạn **paper 2024**, không kết luận current repo vẫn vậy. [Paper](https://arxiv.org/html/2308.00352v7). | Tham chiếu cho role/SOP, không phải bằng chứng một “AI software company” hoạt động tương đương nhóm người. |
| ChatDev | Repo **2.0 DevAll** cấu hình workflow YAML graph, có agent/python/human nodes và loops. Human node dừng chờ input; ví dụ reviewer ACCEPT điều khiển vòng sửa. [Workflow guide](https://github.com/OpenBMB/ChatDev/blob/main/docs/user_guide/en/workflow_authoring.md), [Repo](https://github.com/OpenBMB/ChatDev). | Human node phải được thiết kế vào graph; không có bằng chứng mọi workflow mặc định có business gate. Loop counters/timers và payload filtering là cơ chế điều phối, không chứng minh chất lượng. [Workflow guide](https://github.com/OpenBMB/ChatDev/blob/main/docs/user_guide/en/workflow_authoring.md). | Tách “human-in-loop có thể cấu hình” khỏi “người đã review”. Không dùng paper ChatDev 2024 xác nhận năng lực 2.0. |
| OpenHands | SDK cho agent/code/tools/workspace; TaskToolSet gọi subagent **synchronously**, parent chờ kết quả; có persist/resume. [SDK](https://docs.openhands.dev/sdk), [TaskToolSet](https://docs.openhands.dev/sdk/guides/task-tool-set). | AlwaysConfirm/NeverConfirm/ConfirmRisky cấu hình approval hành động; security analyzer chấm rủi ro. Đây là gate kỹ thuật. [Security](https://docs.openhands.dev/sdk/guides/security). Context condenser tóm tắt history cũ; không giới hạn chiều dài artifact dành cho reviewer. [Condenser](https://docs.openhands.dev/sdk/guides/context-condenser). | Khi nói “multi-agent” cần hỏi sequential/parallel/shared files và bằng chứng actual execution. |

### Bằng chứng hiệu quả và giới hạn

- **METR RCT early-2025:** 16 experienced developers, 246 tasks trên các repo họ quen; AI allowed chủ yếu Cursor + Claude 3.5/3.7 Sonnet, mất thêm 19% thời gian. Đây là đo năng suất trong bối cảnh cụ thể, không đo Zed AI Crew hoặc chứng minh mọi dev chậm hơn. Tự cảm nhận speedup trái kết quả đo. [METR, 10-07-2025](https://metr.org/blog/2025-07-10-early-2025-ai-experienced-os-dev-study/).
- **Không đóng băng kết luận ở 2025:** METR update 24-02-2026 ghi experiment sau bị selection effects và khó đo thời gian khi chạy agents song song; estimates mới là weak evidence, chưa đáng dùng làm con số ROI hiện tại. [METR update](https://metr.org/blog/2026-02-24-uplift-update/).
- **MetaGPT paper:** dùng HumanEval, MBPP và SoftwareDev; “Productivity” ở SoftwareDev là tokens/code lines, **khác** năng suất người hoàn tất công việc. Benchmark cải thiện không xác nhận ít thời gian review hoặc ít escaped defects trên stack này. [MetaGPT paper, v7](https://arxiv.org/html/2308.00352v7).
- **ChatDev paper:** dùng SRDD 1.200 prompts, ChatGPT-3.5/Python; quality tổng hợp completeness/executability/semantic consistency. Executability 0,88 và completeness 0,56 là metrics paper, không phải tỷ lệ business acceptance. Paper ghi agents có thể tự thêm functionality ngoài request. [ChatDev paper, v5](https://arxiv.org/html/2307.07924v5).
- **Giới hạn nghiên cứu này:** các trang Claude/Codex/Spec Kit/BMad/Kiro/GitHub/OpenHands đọc được mô tả cơ chế hoặc claims vendor; chưa tìm được controlled comparison xác nhận role workflow tốt hơn single agent trong Zed/Rust/GPUI. Không lấy repository stars, model benchmark hoặc demo thành bằng chứng đó.

### Năng lực nền tảng và điểm khác biệt — suy luận trong mẫu

**Table stakes trong các công cụ thực thi đã đọc:** tạo/sửa code và artifacts, nhận context/instructions, cho review/feedback, theo dõi tiến trình/bằng chứng, cấu hình ranh giới quyền hành động. Đây là tổng hợp mẫu trên, không phải định nghĩa toàn thị trường.

**Các trục khác biệt cần khảo sát:** (1) structured spec và checklist như Spec Kit/Kiro/BMad; (2) coordination/shared tasks/messages như Claude Teams; (3) issue/PR-native traceability như Copilot; (4) runtime/control mở như OpenHands/ChatDev; (5) độ dài và tần suất artifacts. Role count tự nó chưa là differentiator có bằng chứng.

**Giả thuyết khác biệt của Crew:** điều phối theo lifecycle trong Zed, ba ACP provider và artifact vừa đủ đọc. Đây là nhu cầu từ intake và suy luận giá trị; chưa có comparative validation. Số role hoặc số agent không tự tạo lợi thế thị trường. [Intake](../intake-brief.html)

## 5. DR3 · ISO/IEC 25010:2023 thành tiêu chí kiểm chứng

**Nguồn ngành, tin cậy cao:** ISO mô tả 9 đặc tính chất lượng sản phẩm. Bản 2023 đổi tên usability/portability thành interaction capability/flexibility và thêm safety; không dùng bảng 8 đặc tính bản 2011. [ISO catalog](https://www.iso.org/standard/78176.html), [ISO preview, foreword](https://cdn.standards.iteh.ai/samples/78176/13ff8ea97048443f99318920757df124/ISO-IEC-25010-2023.pdf), [bản GSO tiếp nhận ISO 2023, mục 3](https://gso-sims-preview-doc-aws.s3-eu-west-1.amazonaws.com/gso-iso-iec-25010-2025-en.html)

**Toàn bộ phép kiểm bên dưới là đề xuất BA cho Crew**, không phải checklist hay ngưỡng do ISO quy định. Chưa có bài test nào được chạy cho Crew trong nghiên cứu này. AI có thể chạy tool và tổng hợp kết quả; tool không tự quyết định các ngưỡng có đủ cho người dùng không.

| Đặc tính | Bằng chứng tool có thể thu thập | Phần cần Human/chuyên môn |
| --- | --- | --- |
| Functional suitability | Kịch bản E2E đối chiếu output/exception với yêu cầu, đúng target-project. | Nhu cầu, scope và các ngoại lệ quan trọng. |
| Performance efficiency | Độ trễ, token/cost và tài nguyên theo workload cố định. | Mức chấp nhận và trade-off; không lấy số lượng agent làm thước đo giá trị. |
| Compatibility | Kiểm tra trao đổi với tracker/provider trên phiên bản đã pin. | Nền tảng hỗ trợ và ý nghĩa khi một provider thiếu capability. |
| Interaction capability | Check keyboard, trạng thái lỗi/chờ, cấu trúc câu hỏi và link bằng chứng. | Human thử journey thật: hiểu câu hỏi, biết tác động và quyết định được. |
| Reliability | Thử timeout, restart, resume, kết quả trễ; đối soát side effect đã xảy ra. | Chính sách phục hồi và mức mất việc chấp nhận được. |
| Security | Scan secret/dependency, kiểm tra boundary và payload injection mẫu. | Threat model, dữ liệu cho phép gửi, review rủi ro còn lại. |
| Maintainability | Check dependency/boundary, build/test, review diff theo ADR và ví dụ ngoại lệ. | Kiến trúc hợp lý và công sức sync upstream; chưa chốt công cụ Rust. |
| Flexibility | Chạy cùng intent với cấu hình project/provider khác; kiểm tra override không làm mất ràng buộc. | Override nào hợp lệ, fallback nào giữ được mục tiêu. |
| Safety | Kiểm tra chặn/hủy thao tác có tác động vượt phạm vi được phép; chứng cứ recovery. | Đánh giá tác hại tới tài sản/con người theo context; không đồng nhất safety với security. |

**Không dùng “DoD của tài liệu” để thay DoD của Increment.** Artifact có tiêu chí hoàn thành riêng; Scrum DoD mô tả trạng thái chất lượng của Increment. [Scrum Guide](https://scrumguides.org/scrum-guide.html)

| Loại artifact | Tiêu chí hoàn thành đề xuất | Giới hạn |
| --- | --- | --- |
| Primer / discovery | Claim có nguồn hoặc nhãn giả thuyết; unknown có câu hỏi và người trả lời dự kiến. | Public research không thay xác nhận khách hàng. |
| Spec / quyết định | Truy vết nhu cầu, ví dụ thường/ngoại lệ, rủi ro và quyết định còn mở rõ ràng. | Không suy approve từ im lặng hay từ reviewer AI. |
| Code / Increment | Đúng acceptance criteria và DoD thực tế; kiểm tra tích hợp, boundary, lỗi/phục hồi theo scope. | Passing test không chứng minh nhu cầu đúng hoặc release được ủy quyền. |
| Release / migration record | Phiên bản/môi trường, thao tác thực tế, đối soát và trạng thái phục hồi có bằng chứng. | “Lệnh xong” và “run closed” không đồng nghĩa hệ thống tốt. |

## 6. DR4 · Kiến trúc, tài liệu ngắn và failure patterns

| Cơ chế tham chiếu | Bằng chứng và giới hạn | Giả thuyết áp dụng cho Crew |
| --- | --- | --- |
| ADR conformance + review độc lập | [NIST SSDF PW.1/PW.2](https://nvlpubs.nist.gov/nistpubs/specialpublications/nist.sp.800-218.pdf) mô tả design/security review; đây là hướng dẫn, không phải thử nghiệm chứng minh mọi ADR sẽ được tuân thủ. | Reviewer chỉ ra thay đổi liên quan tới quyết định nào, exception nào cần Human; không chỉ trả “đúng kiến trúc”. |
| Dependency rules / fitness functions | [ArchUnit](https://www.archunit.org/userguide/html/000_Index.html) có check layering/cycles; [tác giả Thoughtworks](https://martinfowler.com/articles/fitness-functions-data-products.html) mô tả governance bằng fitness functions. ArchUnit là ví dụ Java, không phải lựa chọn Rust. | Ràng buộc “crate riêng, móc mỏng” cần biểu diễn bằng điều kiểm được; Developers chọn cách sau TD. |
| Kiểm capability trước UI + integration evidence | Human kể E1 trong [intake](../intake-brief.html); chưa kiểm chứng độc lập nguyên nhân thất bại đó. | Lập ánh xạ “hành vi mong muốn → capability thật → bằng chứng”; cảnh báo mismatch trước design. Không nghiên cứu OmniVoice, ElevenLabs hay Claude Design clone. |
| BLUF, cấu trúc ngắn, rubric | [Digital.gov](https://digital.gov/guides/plain-language/principles) khuyên tổ chức thông tin theo nhu cầu người đọc và dùng ngôn ngữ rõ. Chưa tìm được thử nghiệm xác định ngân sách độ dài tối ưu cho artifact AI ở context này. | Đưa kết quả/quyết định cần Human lên đầu; chi tiết và nguồn trong phần mở rộng. Thử một màn hình tóm tắt, đo thời gian duyệt và số lần phải hỏi lại; chưa chốt ngưỡng chữ. |

**Pain point có bằng chứng khách hàng:** copy prompt thủ công, code lệch kiến trúc và tài liệu lan man là nội dung intake; không phải số liệu đo. **Pain point ngành:** đầu vào không đáng tin có thể gây prompt injection theo [OWASP](https://genai.owasp.org/llmrisk/llm01-prompt-injection/). **Failure patterns cần thử, là suy luận:** reviewer cùng giả định sai với worker; kiểm trên nhánh cũ; summary bỏ constraint; nhiều agent tạo công việc trùng; checklist pass nhưng không gọi backend thật; retry lặp side effect. Không tuyên bố các lỗi này đã xảy ra trong Crew. [Intake TD3/TD5/TD8, E1](../intake-brief.html)

## 7. DR5 · Gate cho một Human

**Đề xuất để discovery thảo luận:** đặt gate ở một quyết định cần trách nhiệm Human hoặc khi sắp vượt phạm vi đã ủy quyền. Không mặc định gate ở mọi step; không gọi kế hoạch agent được agent khác duyệt là Human approval. Đây là mô hình BA, dựa trên quyền Human trong [intake F5/F6](../intake-brief.html) và sự phân biệt permission/review ở §4.

| Điểm quyết định đề xuất | Human cần thấy | Khi chưa có câu trả lời |
| --- | --- | --- |
| Chốt vấn đề / scope thay đổi | Dữ kiện, giả thuyết, lựa chọn, ảnh hưởng tới mục tiêu. | Giữ decision mở; tiếp tục nghiên cứu độc lập nếu có. |
| Chọn trade-off khó đảo ngược | Phương án, rationale, rủi ro và ví dụ cụ thể. | Không tự chọn bằng giả định; chỉ ghi proposal. |
| Publish dữ liệu / deploy / migration vượt ủy quyền | Tác động thật, môi trường, dữ liệu, quyền, recovery và bằng chứng readiness. | Dừng phần phụ thuộc; thẩm quyền và phạm vi ủy quyền cần khách hàng xác nhận. |
| Kết quả thiếu bằng chứng / exception mới | Đã thử gì, trạng thái hiện tại, điều chưa biết, lựa chọn tiếp. | Không tự nâng “chưa xác minh” thành pass. |

**Ví dụ câu hỏi discovery, chưa phải lựa chọn đã chốt:** “Provider hết quota sau khi đã tạo PR nhưng trước khi ghi run result. Có PR thực tế; chưa có bằng chứng verify. Khuyến nghị đối soát PR và tiếp tục từ trạng thái đã biết. Bạn muốn chờ quota hay chuyển provider? Chuyển có thể cần auth/context mới; không tạo PR thứ hai.” Đây là tình huống giả định để kiểm nghĩa gate/retry, không phải sự cố đã quan sát.

**Trade-off cần đo:** ít gate giảm số lần ngắt nhưng có thể tăng chi phí sửa sai; nhiều gate có thể tăng chờ đợi và giảm chú ý. Chưa có nghiên cứu trong bộ nguồn này cho điểm cân bằng của một Human dùng ba provider. Đề xuất đo thời gian chờ, thời gian Human đọc, quyết định bị đảo và rework; UX/Human xác nhận phương pháp, không nhận feedback mô phỏng là nghiên cứu người dùng.

## 8. Checklist quy định, tiêu chuẩn và điều khoản

**Jurisdiction và phạm vi dữ liệu chưa biết.** Ngôn ngữ tiếng Việt và timezone không xác định pháp nhân, nơi kinh doanh hay luật áp dụng. Checklist dưới đây là sàng lọc applicability, chưa phải ý kiến pháp lý hay phê duyệt tuân thủ.

| Hạng mục / nguồn | Applicability hiện biết | Bằng chứng hoặc câu hỏi cần bổ sung |
| --- | --- | --- |
| [ISO/IEC 25010:2023](https://www.iso.org/standard/78176.html) | Đã chọn làm mô hình chất lượng trong intake; không tự tạo nghĩa vụ chứng nhận. | Các thước đo/ngưỡng theo scope, DoD thực tế, nguồn chuẩn đầy đủ nếu cần đối chiếu formal. |
| [NIST SSDF](https://csrc.nist.gov/pubs/sp/800/218/final), [OWASP prompt injection](https://genai.owasp.org/llmrisk/llm01-prompt-injection/) | Hướng dẫn tham chiếu cho phát triển an toàn; chưa có hợp đồng yêu cầu áp dụng formal. | Threat model cho issue/tool, quyền tối thiểu, xử lý vulnerability và secrets. |
| GPL/Apache của fork | [README](../../README.md) nêu GPL-3.0-or-later và các component Apache; [LICENSE-GPL](../../LICENSE-GPL) §5/§6 đặt điều kiện khi chuyển giao modified source/object code. | Có phân phối binary/fork cho người khác không? Ai rà license và notice của dependency/asset? Crate riêng không tự miễn nghĩa vụ. |
| [Luật BV dữ liệu cá nhân Việt Nam 91/2025/QH15](https://vanban.chinhphu.vn/?classid=1&docid=214590&pageid=27160&typegroup=) | Danh mục chính phủ ghi hiệu lực 01-01-2026. Chỉ là candidate nếu hoạt động/dữ liệu nằm trong phạm vi luật; chưa xác định applicability. | Pháp nhân/hoạt động, data subjects, phân loại dữ liệu và chuyển dữ liệu ra provider; Legal rà toàn văn và văn bản hướng dẫn hiện hành trước kết luận. |
| [GDPR](https://eur-lex.europa.eu/eli/reg/2016/679/oj/eng), Điều 3 | Candidate nếu có establishment EU hoặc offer/monitoring thuộc phạm vi; không áp dụng chỉ vì dùng GitHub/provider quốc tế. | Nơi hoạt động, người có dữ liệu, mục đích xử lý, retention và cơ chế transfer; Legal/Privacy xác nhận. |
| Provider terms, retention, subscription entitlement | [Zed external agents](https://zed.dev/docs/ai/external-agents) nêu quan hệ billing/data với provider. Gói khai báo trong C7 chưa xác minh được quyền automation qua adapter. | Loại account và điều khoản cụ thể, dữ liệu có được gửi không, log lưu ở đâu; TD và Legal/Privacy kiểm tra. |
| Public issue và thông tin khách hàng | Repo public là bối cảnh được ghi trong [intake](../intake-brief.html) và [quy ước tracker](../../docs/agents/issue-tracker.md). | Những gì được publish; redaction, credentials và nội dung nhạy cảm phải ở đâu. Chưa viết nội dung ra GitHub trong bước này. |

## 9. DR6 · Draft glossary — mọi mục là hypothesis

Các nghĩa bên dưới **đều là hypothesis chờ khách hàng xác nhận**, kể cả khi có định nghĩa Scrum để đối chiếu. Chưa chuyển vào `GLOSSARY.md`, vì chưa có thuật ngữ mới nào được xác nhận; không tạo ADR cho một đề xuất nghiên cứu. Nguồn đối chiếu: [intake](../intake-brief.html), [registry cũ](../../../zed-scrum-team/old-specs/registry.md), [catalog cycle](../../../zed-scrum-team/old-specs/sdlc/cycles.html), [Scrum Guide](https://scrumguides.org/scrum-guide.html), [Zed external agents](https://zed.dev/docs/ai/external-agents).

| Thuật ngữ | Nghĩa đề xuất — hypothesis | Điểm dễ lẫn / ví dụ phân biệt | Trạng thái |
| --- | --- | --- | --- |
| Human | Người quyết định và dùng Crew trong engagement này. | Không đồng nghĩa mọi end user của target-product. | Hypothesis |
| Crew / target-project | Crew là công cụ đang xây; target-project là sản phẩm/repo được Crew hỗ trợ. | Tracker target-project ở repo nào vẫn là A2 chưa chốt. | Hypothesis |
| Session | Một ngữ cảnh làm việc phục vụ một intent. | Có đồng nhất một thread/provider session hay gồm nhiều thread? Còn mở. | Hypothesis |
| Thread | Chuỗi hội thoại trong giao diện agent. | Không mặc định bằng session nghiệp vụ. | Hypothesis |
| Scenario | Nhóm mục tiêu đầu cuối, như migration hoặc incident. | Không phải mỗi chuyên môn hay mỗi step. | Hypothesis |
| Playbook | Định nghĩa cách xử lý các nhóm công việc. | Không phải một lần thực thi; “recipe” có thể chỉ một phần playbook. | Hypothesis |
| Cycle | Đơn vị công việc tái dùng gồm các step để đạt một kết quả. | Research là cycle trong catalog cũ; không phải Scrum Sprint/event. | Hypothesis |
| Step | Một hành động/góc nhìn có input và output trong cycle. | Đổi role không tự tạo session mới. | Hypothesis |
| Run | Một lần thực hiện có scope và kết quả cụ thể. | Scenario definition khác scenario run; cycle có thể chạy nhiều lần. | Hypothesis |
| Gate | Điểm cần quyết định hoặc kiểm tra điều kiện chuyển tiếp. | Cần gọi rõ Human gate, permission gate hay quality check. | Hypothesis |
| Permission | Cho phép một thao tác/tool theo phạm vi quyền. | Không xác nhận chất lượng hay giá trị của sản phẩm. | Hypothesis |
| Readiness | Đủ điều kiện để chuyển bước trong scope cụ thể. | Không phải Verify, approval hay bằng chứng đã hoàn thành. | Hypothesis |
| Orchestrator / Worker | Tác nhân điều phối / tác nhân thực hiện phần việc. | Là vai thực thi AI; không tương đương PO hoặc Developers accountability. | Hypothesis |
| Artifact / deliverable | Sản phẩm công việc / kết quả dự kiến bàn giao. | File được tạo chưa chứng minh kết quả đạt; HTML là review view. | Hypothesis |
| Acceptance criteria | Điều kiện chấp nhận một item với ví dụ và ngoại lệ. | Không thay DoD dùng chung của Increment. | Hypothesis |
| Definition of Done | Mô tả trạng thái chất lượng của Increment theo Scrum. | Không dùng tên này cho mọi checklist tài liệu. | Hypothesis |
| Increment | Kết quả sử dụng được, tích hợp và đạt DoD. | Không đồng nghĩa PR mở hay worker báo done. | Hypothesis |
| Release / deploy | Giao phiên bản cho người dùng / đưa phiên bản vào môi trường. | PR merge và deploy command xong chưa chứng minh release khỏe. | Hypothesis |
| Incident / bug / hotfix | Sự kiện gây tác động vận hành / sai hành vi / bản sửa khẩn. | Incident có thể được giảm tác động trước khi sửa bug xong. | Hypothesis |
| Review / verification / approval | Đánh giá / đối chiếu tiêu chí bằng chứng / quyết định chấp thuận. | Reviewer AI pass không phải Human đồng ý. | Hypothesis |
| ACP / MCP | Giao thức editor–agent / giao thức cung cấp tool và context cho agent. | Hỗ trợ giao thức không bảo đảm mọi adapter có cùng capability. | Hypothesis |
| ADR | Bản ghi một quyết định kiến trúc, phương án và lý do lựa chọn. | Một đề xuất đang nghiên cứu chưa phải quyết định đã chốt. | Hypothesis |
| Canonical source | Nguồn được công nhận để xác định nội dung hiện hành. | Bản xem và summary không tự thay thế nguồn này. | Hypothesis |
| Capability | Hành vi một hệ thống hỗ trợ trong điều kiện xác định. | Có tool hoặc tên feature chưa chứng minh hành vi mong muốn khả thi. | Hypothesis |
| Quota / rate limit | Hạn mức sử dụng / giới hạn tần suất thực thi. | Giá subscription chưa cho biết mức song song thực tế. | Hypothesis |
| Worktree | Một vùng làm việc tách biệt của cùng repository. | Tách vùng sửa file không tự giải quyết xung đột nghiệp vụ khi tích hợp. | Hypothesis |

**Đề xuất từ vựng chưa chốt:** giữ scenario để gọi mục tiêu đầu cuối; giữ definition/run để phân biệt mô tả với lần thực thi. Tạm tránh thêm recipe/flow/workflow như ba khái niệm riêng nếu khách hàng chỉ dùng chúng làm đồng nghĩa. Chưa gộp session và thread; chưa đổi thuật ngữ Scrum. Rationale nằm trong primer này, không trộn vào glossary chính thức.

## 10. Unknown của khách hàng và route tiếp theo

Đây là danh sách để đưa vào discovery kit; chưa tổ chức interview. Mọi người trả lời/owner dưới đây là **đề xuất**, chưa ai nhận trách nhiệm.

| ID | Câu hỏi riêng của khách hàng | Người trả lời dự kiến / vì sao cần |
| --- | --- | --- |
| U1 | Human và các target-product tạo giá trị/doanh thu thế nào? Crew dùng nội bộ hay sẽ phân phối? | Human/PM: hiểu economic outcome và license context. |
| U2 | “Phủ 100%” giới hạn ở loại project, nền tảng, môi trường và các scenario nào? | Human/PO: xác định phạm vi; không phải quyết định ordering. |
| U3 | Một ví dụ mới nhất code lệch kiến trúc, artifact quá dài và một kết quả tốt là gì? Human tốn công ở đoạn nào? | Human + UX: baseline thực tế, tránh suy từ E1. |
| U4 | Thao tác nào đã được ủy quyền tự chạy; thao tác nào cần Human; Human vắng thì chờ/hủy/tiếp tục phần độc lập thế nào? | Human/người vận hành: semantics gate và quyền thật. |
| U5 | Project/repo/data nào được gửi tới provider, được publish lên issue hoặc lưu trong run log? Retention mong muốn? | Human + Legal/Privacy: ranh giới dữ liệu. |
| U6 | Pháp nhân, nơi hoạt động, người có dữ liệu và các hợp đồng/quy định ngành của target-project là gì? | Human + Legal/Compliance: applicability; không đoán jurisdiction. |
| U7 | Có DoD hiện hữu không? “Production-ready” đo bằng tín hiệu gì, mức recovery/cost/latency nào chấp nhận được? | Human + Developers/QA: ngưỡng chất lượng. |
| U8 | Muốn đọc gì trên màn hình đầu tiên, khi nào mở chi tiết; tối đa bao nhiêu thời gian duyệt một artifact? | Human + UX: tiêu chí concision; chưa tự chốt word budget. |
| U9 | Session khác thread thế nào; scenario có là cấp trên session không; “cycle” giữ nghĩa cũ nào? | Human + BA: glossary hypothesis; kiểm bằng ví dụ một intent có nhiều lần research. |
| U10 | A2 tracker của target-project có đúng không? Ai quyết định chuyển một item giữa nghiên cứu, delivery và release? | Human/PO: nguồn nghiệp vụ và quyền, không chọn API ở đây. |
| U11 | Khi nào một phép kiểm/approval cũ hết hiệu lực sau đổi scope, phiên bản hoặc config? | Human + Developers/QA: tránh tái dùng bằng chứng sai ngữ cảnh. |

**Next route trong cùng mạch kickoff:** đưa primer và U1–U11 vào discovery kit sau khi có technical landscape bản nháp. PM/BA nhận ma trận scenario và giả thuyết giá trị; UX nhận journey tài liệu và nhu cầu đo thời gian duyệt; Legal/Privacy nhận checklist applicability. Developers nhận các câu hỏi TD1/TD3/TD5/TD8 về permission, retry, quota, data boundary và integration evidence. Đây là recipient/next action đề xuất; không có cuộc trao đổi bên ngoài nào đã được gửi, không có owner đã nhận.

## 11. Trạng thái hoàn thành và giới hạn kiểm chứng

| Tiêu chí bước nghiên cứu | Bằng chứng | Trạng thái |
| --- | --- | --- |
| Người mới theo được trao đổi domain | Mô hình giá trị, actors, workflow/exception, glossary đối chiếu ở §2–3/§9. | Draft đủ dùng để chuẩn bị discovery; chưa kiểm với người mới/domain expert. |
| Claim có nguồn hoặc nhãn suy luận | Link tại từng claim/bảng, khả năng sản phẩm tách khỏi outcome thực nghiệm. | Đã kiểm nội dung tài liệu; không chạy sản phẩm tham chiếu. |
| Unknown khách hàng không bị đoán | U1–U11, mô hình doanh thu và jurisdiction còn mở. | Đủ cho bước này; chặn kết luận riêng về khách hàng, không chặn primer. |
| DR1–DR6 có kết luận/giới hạn | §3–7/§9; checklist ở §8. | Nghiên cứu hoàn tất ở mức draft, chưa có product decision mới. |

**Những gì thực sự đã kiểm:** đọc intake, SDLC, README/domain conventions và catalog/UI cũ; đối chiếu danh sách workflow và cycle; đọc nguồn web sơ cấp; agent nền nghiên cứu nhóm sản phẩm tham chiếu. Repo snapshot `28f2e9cc0277b42df4492aacf1737e7e883978e6`. Kiểm tra tĩnh bản Markdown/HTML được ghi sau cùng. Không probe ACP, không benchmark, không chạy customer UI, không user interview, không xác minh quota/subscription hay compliance. Fetch web public fork thất bại nên dùng tài liệu local và intake; không tuyên bố đã walkthrough website riêng của khách hàng. Tài liệu permission của fork mô tả session/request_permission và bypass, trong khi docs Zed live nêu forwarding permissions có thể áp dụng; TD8 cần đối chiếu đúng adapter/version, chưa coi hai phạm vi quyền là tương đương. [Fork docs](../../docs/src/ai/external-agents.md), [Zed live](https://zed.dev/docs/ai/external-agents). GNU web fetch không thành công; đối chiếu license local. Toàn văn ISO trả phí không được mua/đọc trọn; dùng catalog, preview và bản tiếp nhận của GSO cho đối chiếu cấp đặc tính. PDF luật Việt Nam truy xuất được nhưng không có text extract; chỉ dùng catalog hiệu lực, chưa suy nghĩa vụ chi tiết.
