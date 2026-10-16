---
status: accepted
date: 2026-10-16
---

# Dùng nền tảng lưu trữ hiện có của Zed cho Crew

Human yêu cầu dùng nền tảng của Zed và cách Zed đang lưu trữ, thay vì chọn một hệ lưu trữ độc lập cho Crew. Crew dùng lại SQLite qua các crate `db`/`sqlez` của Zed: trạng thái run có bảng/migration theo domain trong `AppDatabase`, cùng cơ chế đường dẫn, release channel và hàng đợi ghi nền. Đây là lựa chọn nền tảng đã xác nhận; mapping kỹ thuật này dựa trên inspection của repository, chưa phải code đã triển khai hay runtime qualification.

## Lý do và phương án thay thế

Đề xuất trước đó là một SQLite store riêng hoặc journal file tự quản lý. Dùng nền tảng Zed giảm phần storage/packaging/migration phải xây và phù hợp engine/controller chạy trong Zed đã chọn. Đổi lại Crew chia sẻ vòng đời/schema initialization và hàng đợi ghi với Zed; phải kiểm ảnh hưởng migration, lỗi persistence, contention và độ tương thích khi nâng cấp. Thay hướng này về sau đòi chuyển dữ liệu và thay recovery integration nên cần giữ rationale riêng.

Zed không lưu mọi dữ liệu trong một chỗ: metadata sidebar dùng domain của `AppDatabase`; lịch sử native agent dùng `ThreadsDatabase` ở `data_dir()/threads/threads.db`, qua `sqlez` và payload JSON nén. Crew giữ run/operation/receipt/binding riêng về mặt dữ liệu, không dùng chat transcript hoặc một JSON key thay authoritative run state. Các bản transcript/cache native bên ngoài phần Crew kiểm soát vẫn theo inventory và giới hạn xóa đã chốt.

## Hệ quả cho thiết kế

- Dùng mẫu domain/migration hiện có cho các bảng Crew có project/run/operation identity; không tự tạo dịch vụ database, journal engine hoặc thay thư mục dữ liệu Zed.
- Controller trong Zed là writer của Crew ledger. Dùng `ThreadSafeConnection::write` và savepoint/transaction, chờ kết quả ghi thành công rồi mới xét dispatch; `write_and_log` chỉ log/detach không đủ cho intent/receipt quan trọng. Lỗi phải tới trạng thái/UI, không báo hoàn tất trước commit.
- Kiểm `Connection::persistent()` trên connection thực sự dùng trong queued write; chỉ nhìn tên path hoặc flag cấp App không đủ vì `Connection::open_file` cũng có fallback RAM. Stateless/fallback/migration/write failure chặn thao tác Crew cần recovery state; không nhận store rỗng như run mới hợp lệ sau một lỗi lưu trữ.
- Giữ defaults Zed đã quan sát (`WAL`, `synchronous=NORMAL` ở app DB initialization); không âm thầm đổi cấu hình chung sang `FULL`, và không suy power-loss durability từ source hoặc những probe SQLite riêng trước đó. Nếu qualification chứng minh default không đáp ứng contract thì nêu đúng incompatibility để xử lý; không đổi sang store khác hay nới yêu cầu ngầm.
- Independent effect owners không được tự mở/ghi toàn bộ AppDatabase hoặc nhận SQLite handle/path qua worker. Khi controller mất, giữ intent/uncertainty và đối soát theo #10/#20; không suy receipt đã lưu hoặc replay mutation. Custody/publication/IPC cụ thể còn thuộc #14.
- Dùng migration metadata của Zed cùng version của payload/binding Crew để kiểm compatibility; không coi migration helper hiện có là bằng chứng đã chống mọi downgrade/future schema. Không sửa migration cũ hoặc reset DB để né lỗi. Trạng thái hoàn thành giữ yêu cầu của US được giao; ý tưởng mới thuộc US mới.

## Nguồn và giới hạn

Nguồn quyết định: Human nói “hãy sử dụng nền tảng của zed, zed đang lưu trữ thế nào thì giờ sử dụng cái đó” trong hội thoại; [ticket kiến trúc #14](https://github.com/Riso-application-factory/zed-ai-crew/issues/14). Inspection tại local HEAD `de4ca3f4d415da54fe002f06656f2ded9fb1fcf0`: `crates/db/src/db.rs`, `crates/sqlez/src/{connection,thread_safe_connection,domain,migrations,savepoint}.rs`, `crates/agent/src/{db,thread_store}.rs`, `crates/agent_ui/src/thread_metadata_store.rs`, `crates/paths/src/paths.rs`, `crates/zed/src/main.rs`; exact hashes/excerpts ở evidence của #14. Không mở/sửa database thật, chạy migration, chạy test runtime hoặc thay setting Zed trong lượt quyết định này. Không thay ADR-0001/0002; không tự chọn MCP, provider/model, private profile, topology hay owner bảo trì bên ngoài.
