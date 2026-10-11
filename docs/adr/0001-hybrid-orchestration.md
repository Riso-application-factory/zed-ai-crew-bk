---
status: accepted
date: 2026-10-11
---

# Tách hiểu yêu cầu bằng LLM khỏi thực thi session bằng code

Human đã đồng ý ở câu 7 của shaping: Orchestrator LLM hiểu yêu cầu và tạo session plan; code kiểm tính hợp lệ, giữ trạng thái và điều khiển thực thi theo dependency, quyền và OUTPUT. Trạng thái thực thi nằm trong run store, tách khỏi hội thoại và spec/ticket trên GitHub Issues. Lựa chọn này giữ đầu vào ngôn ngữ tự nhiên và một tiến trình có thể đối soát; đổi lại session plan cần biểu diễn đủ rõ để code kiểm tra và thực thi. Thay đổi ranh giới này về sau ảnh hưởng đồng thời tới điều phối, phục hồi, quyền và kiểm chứng nên được ghi riêng với glossary.

## Phương án đã cân nhắc

- Hướng lai được chọn: LLM diễn giải/lập plan, code giữ thực thi.
- LLM trực tiếp điều phối qua Crew tools, code giữ bất biến: linh hoạt nhưng tiến trình còn phụ thuộc model gọi đúng tool và phục hồi context điều phối.
- Điều phối bằng prose và gh: ít code ban đầu nhưng chưa có bằng chứng đáp ứng ranh giới quyền, state và recovery đã yêu cầu.

Chưa chọn database, schema, transport MCP, API hoặc provider/model cho Orchestrator. Chưa có benchmark hoặc validation mới chứng minh reliability/cost của hướng đã chọn. G6 đã xác nhận kiểm chứng độc lập và chạy luồng thật; các phép kiểm này cần làm sâu theo phạm vi Epic trước khi giao sử dụng.

Nguồn: câu trả lời Human 7 và G6 trong hội thoại; [sổ quyết định shaping](../../artifacts/shaping/solution-scope.md); [technical landscape §4 và cập nhật discovery](../../artifacts/research/technical-landscape.html); [synthesis R1/R5](../../artifacts/discovery/synthesis.md). Thiết kế cũ chỉ là input nghiên cứu, không được nhập nguyên làm quyết định hiện hành.
