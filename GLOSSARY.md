# Zed AI Crew

Từ vựng của công cụ hỗ trợ Human thực hiện quy trình phát triển phần mềm.

## Language

**Human**:
Người dùng và người có quyền quyết định đối với Crew trong engagement này. Không đồng nghĩa với mọi người dùng của sản phẩm đích.

**Crew**:
Công cụ hỗ trợ Human điều phối công việc phát triển phần mềm đang được xây dựng trong dự án này.

**target-project**:
Dự án phần mềm đích mà Crew hỗ trợ, phân biệt với dự án xây dựng chính Crew.

**Epic**:
Một lát phạm vi của sản phẩm đủ nhỏ để triển khai và sử dụng được.

**Story**:
Một phần phạm vi của Epic có hành vi và tiêu chí chấp nhận riêng, dùng làm đơn vị giao cho session implement.

**Task triển khai**:
Một phần công việc cần thực hiện để hoàn thành Story.

**Thread**:
Một mạch hội thoại với LLM, gồm ngữ cảnh và lịch sử tương tác; phân biệt với Session là đơn vị công việc.

**Orchestrator**:
Thread đảm nhiệm vai trò điều phối sau khi được nạp bộ hướng dẫn Orchestrator; không đồng nhất với model, provider hoặc controller thực thi.

**Bộ hướng dẫn Orchestrator**:
Tập hướng dẫn tạo nên vai trò điều phối của thread, gồm system prompt, SOUL và các thành phần hướng dẫn liên quan được chọn; còn được gọi là “bộ não Orchestrator”.

**Worker**:
Agent qua ACP (Claude, Codex hoặc Antigravity) được Crew giao một phần việc trong phạm vi riêng; đề xuất kết quả nhưng không tự cấp quyền hay tự xác nhận OUTPUT.

**Session**:
Đơn vị công việc Crew xác định từ yêu cầu của Human và thực hiện theo session plan.

**Session plan**:
Kế hoạch các bước công việc Crew chạy để một session đạt OUTPUT.

**OUTPUT**:
Kết quả đã thỏa thuận mà một session phải thực sự đạt, kèm bằng chứng; phân biệt với trạng thái thực thi như turn kết thúc, exit code hay PR đã mở, và với báo cáo về công việc.

**Recipe**:
Định nghĩa có version cho một kịch bản vòng đời, gồm đầu vào, các bước, nhánh lỗi và cách đánh giá OUTPUT; phân biệt với session plan cụ thể và run thực tế.

**Cycle**:
Một nhóm bước công việc có mục đích chung, có thể dùng lại trong các session plan.

**Step**:
Một đơn vị công việc trong cycle.

**Run**:
Một instance thực thi cụ thể của đơn vị công việc Crew. Session run, Cycle run và Step run chỉ rõ cấp công việc được thực thi.

**Attempt**:
Một lần thực hiện cụ thể của một step trong session plan, với binding riêng; retry hoặc rework tạo attempt mới, và kết quả trễ của attempt cũ không được tính cho attempt mới.

**Binding**:
Tập tham chiếu bất biến mà một attempt dùng, gồm yêu cầu và spec gốc, project, cấu hình, role, skills, profile và quyền. Khi nguồn đổi, binding cũ không bị sửa; phần việc bị ảnh hưởng được lập lại với binding mới.

**Story Ready**:
OUTPUT của session refine: Story có đủ đầu vào để session implement nhận mà không cần làm rõ thêm.

**Integration branch**:
Nhánh riêng của một Story, nơi Crew tích hợp các worktree của Story trước khi mở PR vào nhánh chính.

**Nhánh chính**:
Nhánh mà PR của Story được merge vào, được bind trong Story Ready của project. Repo Crew dùng `risotech`; `main` của fork giữ lịch sử Zed upstream.

**Execution scope**:
Phạm vi thực thi cụ thể cùng các tài nguyên và quyền tác động của nó; phân biệt với Session, Story và kết quả được đề xuất.

**Reservation**:
Giữ chỗ bền vững trên một đích khi có thao tác chưa chắc chắn kết quả; chỉ được nhả sau khi đối soát, không vì timeout hay việc dùng ID hoặc thư mục mới.

**Candidate artifact**:
Một bản kết quả cụ thể được đưa vào kiểm chứng hoặc xem xét để công bố; chưa đồng nghĩa với kết quả đã được chấp nhận hay OUTPUT đã đạt.

**Publication target**:
Đối tượng nhận thay đổi được công bố; phân biệt với nơi một worker đang làm việc và candidate artifact dự kiến dùng cho thay đổi đó.
