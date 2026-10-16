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

**Session**:
Đơn vị công việc Crew xác định từ yêu cầu của Human và thực hiện theo session plan.

**Session plan**:
Kế hoạch các bước công việc Crew chạy để một session đạt OUTPUT.

**Cycle**:
Một nhóm bước công việc có mục đích chung, có thể dùng lại trong các session plan.

**Step**:
Một đơn vị công việc trong cycle.

**Run**:
Một instance thực thi cụ thể của đơn vị công việc Crew. Session run, Cycle run và Step run chỉ rõ cấp công việc được thực thi.

**Story Ready**:
OUTPUT của session refine: Story có đủ đầu vào để session implement nhận mà không cần làm rõ thêm.

**Integration branch**:
Nhánh riêng của một Story, nơi Crew tích hợp các worktree của Story trước khi mở PR vào main.

**Execution scope**:
Phạm vi thực thi cụ thể cùng các tài nguyên và quyền tác động của nó; phân biệt với Session, Story và kết quả được đề xuất.

**Candidate artifact**:
Một bản kết quả cụ thể được đưa vào kiểm chứng hoặc xem xét để công bố; chưa đồng nghĩa với kết quả đã được chấp nhận hay OUTPUT đã đạt.

**Publication target**:
Đối tượng nhận thay đổi được công bố; phân biệt với nơi một worker đang làm việc và candidate artifact dự kiến dùng cho thay đổi đó.
