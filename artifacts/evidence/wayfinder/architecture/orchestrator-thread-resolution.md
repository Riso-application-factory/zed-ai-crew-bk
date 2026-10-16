# #14 — Orchestrator là thread mang bộ hướng dẫn điều phối

**Status: confirmed Human domain clarification; technical thread/binding mapping source-inspected, not implemented or runtime-qualified.**

Human làm rõ: “Ochestrator thực ra là 1 thread với system prompt + SOUL + ..<anything guided LLM orchestrating>.., dù chọn model của provider nào, sau khi được load bộ não của orchestrator thì trở thành orchestrator”. Vì vậy Orchestrator được nhận diện bởi vai trò/bộ hướng dẫn được nạp vào thread, không phải một model/vendor/CLI riêng.

## Điều đã chốt và câu hỏi rút lại

- **Thread** là mạch hội thoại có context/history. **Session** là đơn vị công việc theo glossary; không suy một thread tương đương một Session hoặc một OS thread/process.
- Bộ hướng dẫn Orchestrator gồm system prompt, SOUL và các thành phần điều phối được chọn. Đây là tập thành phần có thể mở rộng, không phải quyền Crew tự thêm hướng dẫn không có nguồn hoặc âm thầm đổi chính sách.
- Model/provider là backend được cấu hình cho thread. Cùng vai trò Orchestrator có thể dùng các model/provider phù hợp khác nhau; chọn một backend không tự xác định hoặc thay thế vai trò.
- Câu A/native Zed LLM versus B/ACP vừa hỏi đã trộn identity với mechanism và tạo một gate Human không cần thiết cho định nghĩa Orchestrator. **Rút câu A/B đó; không ghi Human chọn A hoặc B.** Backend delivery/capability mapping vẫn là việc kỹ thuật của #14, không được gọi là đã chọn mọi transport.
- Giữ engine/controller trong Zed, storage Zed db/sqlez, ba worker ACP và ADR-0001. “Nạp bộ não” không chuyển authoritative run state/quyền/OUTPUT từ code sang lời tự báo của LLM; không tự đổi provider khi quota cạn.

## Ví dụ phân biệt

| Trường hợp | Kết luận |
| --- | --- |
| Thread dùng model A, đã nạp bộ hướng dẫn điều phối R1 | Là thread Orchestrator với binding R1 và backend A |
| Thread dùng model B/provider khác, được nạp cùng vai trò phù hợp | Vẫn là Orchestrator; adapter/profile/capability và binding backend phải được xác định đúng |
| Thread thường dùng đúng model A nhưng không có bộ hướng dẫn Orchestrator | Model giống nhau không làm nó tự trở thành Orchestrator |
| Load history cũ nhưng thiếu hoặc đổi bundle R1/R2 | History không chứng minh đã nạp đúng bộ hướng dẫn; revalidate/materialize trước khi tiếp tục phần bị ảnh hưởng |

Đây là scenario reasoning, không phải các request/model tests đã thực thi. Không hứa mọi model bất kỳ trên thị trường có system-tier/capability tương đương; dùng các backend đã được hỗ trợ/admission theo version.

## Mapping vào nền tảng Zed và contract đã có

`agent::Thread` thực sự tách id/messages, profile, templates và model. `Thread::new` nhận model và context/templates riêng; load từ DB resolve saved provider/model thành Ready hoặc Unresolved. Khi thiếu saved selection, một số nhánh dùng profile/default registry; Crew phải giữ explicit backend binding theo #11 và không biến fallback thành permission tự đổi provider.

Native request builder dựng `Role::System` từ `SystemPromptTemplate`, project context và user rules rồi thêm history. Đây là seam có thể tích hợp bundle, **không** chứng minh stock Zed đã có SOUL loader hoặc Crew bundle binding. Stock native thread có tool registry/execution và đọc một số context khi build request; không gọi nguyên path đó là controlled Orchestrator chỉ vì thay prompt. Crew adapter cần nhận proposal/question/result có typed identity để code validate theo #9; effect admission vẫn ở controller/owners.

Thiết kế kế thừa #11: giữ thread/conversation refs riêng với run/attempt; record ordered instruction component refs, canonical source/content versions, delivery channel, model/provider/profile/mode và context refs. Trước new/resume/compaction, resolve và materialize bundle được chọn, kiểm ảnh hưởng của revision drift và effective backend capability; prompt/role text không được cấp quyền runtime. Không cần sửa glossary thành schema kỹ thuật.

Không tự quy định SOUL phải là file nào, nội dung nào hoặc mọi thành phần phải được nạp ở native system tier. Không tìm thấy file có tên SOUL/soul trong các artifact/domain/skill paths đã kiểm; nội dung/tổ chức source của bundle còn là phần kỹ thuật cần cụ thể hóa từ cấu hình/role definitions. Không yêu cầu Human cung cấp một file để hoàn thành việc ghi nhận khái niệm này, cũng không tự claim đã load một bộ não thật.

## Trạng thái và tiếp tục

Glossary cập nhật ba thuật ngữ Thread/Orchestrator/Bộ hướng dẫn Orchestrator. Không tạo ADR mới: clarification này phù hợp ADR-0001, chưa là lựa chọn công nghệ/transport hard-to-reverse có trade-off mới đã được giải quyết. Giữ các ADR hiện có và source/audit lịch sử; packet A/B cũ là đề xuất đã rút, không hiện như câu chờ Human.

#14 vẫn phải cụ thể hóa instruction-source assembly và backend/host capability mapping, controller/effect-owner channel/custody/reconnect, publication và upgrade/resource/packaging seams. Exact SOUL/default role authoring không được tự suy từ tên file hoặc lời model tự báo. #15 vẫn phải đối chiếu toàn C01–C11/S01–S15. Không spec/build ticket hoặc code production được tạo từ correction này.
