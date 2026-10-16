---
status: accepted
date: 2026-10-13
---

# Khởi chạy filesystem helper qua launcher giới hạn ngoài worker

Human chọn A trong [quyết định #35](https://github.com/Riso-application-factory/zed-ai-crew/issues/35): dùng launcher giới hạn ở phía tin cậy, ngoài quan hệ tiến trình của worker, để khởi chạy native filesystem helper. Worker tiếp tục bị cách ly; quyền của mỗi helper phải đồng thời nằm trong giới hạn worker và policy của request, được hệ điều hành cưỡng chế. Đây là hướng thiết kế đã được chấp nhận để kiểm chứng tích hợp; launcher chưa được triển khai hoặc xác minh đầy đủ.

[Phép thử #34](https://github.com/Riso-application-factory/zed-ai-crew/issues/34#issuecomment-6140172881) cho thấy helper trong cấu trúc sandbox hiện tại lỗi trước thao tác file, còn cùng lệnh/payload native khởi chạy độc lập có cặp ghi hợp lệ/từ chối chỉ đọc hoạt động. Chỉ giữ sandbox ngoài khiến request chỉ đọc vẫn ghi được; chỉ áp dụng policy request ở launcher độc lập có thể cho ghi ngoài worktree. Vì vậy launcher phải cưỡng chế cả hai giới hạn; các phép so sánh này chưa chứng minh một launcher tích hợp an toàn.

## Phương án đã cân nhắc

- A được chọn: launcher giới hạn độc lập, giữ policy từng request ở hệ điều hành. Đổi lại cần thêm thành phần tin cậy, giao thức, đóng gói và trách nhiệm bảo trì.
- B không được chọn: giữ helper trong worker và cưỡng chế policy từng request bằng code. Phương án này giữ giới hạn OS của worker nhưng chuyển quyền hẹp hơn sang hợp đồng code cần kiểm đầy đủ path, symlink, alias và ngữ nghĩa thao tác file.

## Hệ quả và phần chưa chốt

Launcher chỉ nhận thao tác đã được authority có thẩm quyền chấp nhận và chỉ khởi chạy native helper đã pin. Không nhận tùy ý program, argv, environment hoặc descriptor từ worker. Request, policy, attempt, ngữ cảnh và danh tính executable cần được ràng buộc; guard phải giữ tới khi helper hoàn tất và được thu hồi, bao gồm các nhánh disconnect, cancellation và publication. Đây là điều kiện thiết kế cần kiểm chứng, chưa phải bảo đảm runtime đã có.

Chưa chọn IPC, cơ chế tính giao quyền, XPC hay cách đóng gói cụ thể; chưa có owner bên ngoài nhận trách nhiệm hoặc cam kết bảo trì lâu dài. Quyết định không cấp quyền production, private data/credentials, thay đổi managed policy thật, account/provider/model/billing hoặc release. Các quyền và lựa chọn đã xác nhận trước vẫn giữ nguyên. Quyền/giao tác xét theo từng thao tác file; không hứa rollback toàn patch hoặc nguồn copy bất biến.

Nguồn: Human trả lời nguyên văn `A` trong hội thoại sau hai lựa chọn của #35; [bằng chứng #34](https://github.com/Riso-application-factory/zed-ai-crew/issues/34#issuecomment-6140172881); [direct filesystem admission #33](https://github.com/Riso-application-factory/zed-ai-crew/issues/33#issuecomment-6139161784). Không thay [ADR-0001](0001-hybrid-orchestration.md): code tiếp tục giữ trạng thái, quyền và tính hợp lệ của thực thi.
