# Role/skills ACP — findings and confirmed binding

**2026-10-11 · complete for this decision ticket.** Canonical ticket: [Kiểm chứng kênh role và skills trên ba adapter ACP](https://github.com/Riso-application-factory/zed-ai-crew/issues/17). Prototype thực đã chạy; [Human xác nhận binding riêng theo adapter, gồm Antigravity prompt fallback có version](https://github.com/Riso-application-factory/zed-ai-crew/issues/17#issuecomment-6109950179). Không claim provider parity hoặc sản phẩm đã giao. Map toàn sản phẩm vẫn draft / awaiting decision.

## Scope, sources and execution

Đọc map, glossary/ADR hybrid, ACP research, data/quality resolutions và TD1; TD1 giữ ngày/giới hạn cũ. Tạo ba detached worktrees từ ba scratch repositories riêng dưới `/private/tmp/zed-crew-role-skills-20261011`; không checkout hay gửi target-project. Fixture gồm một `SKILL.md`, resource và Python script chỉ print marker, với R1/R2 khác nhau. Prompts là dữ liệu tổng hợp, không chứa marker body/resource/stdout mong đợi. Model tự đọc/chạy fixture; tool-result events được dùng thay cho tự báo pass.

Có **21 prompt turns** trong 11 phases có prompt; preflight riêng được lưu, gồm sandbox failures. Không ép cạn quota, đổi provider/model/account/billing hoặc gọi tools production. Ba adapters dùng registry binaries đã cài và saved native auth; không đọc/sao chép giá trị secret. Native adapters tạo state/cache/session theo cơ chế của chúng; sandbox chặn Codex SQLite và Antigravity session nên đã xin và nhận escalation cho harness. Profile no-training, account plan và custom integration terms vẫn chưa xác minh; synthetic probes không cấp phép gửi private content.

Harness không phải confinement: có client read seam giới hạn fixture, deny mutation/terminal/delegation requests và allow_once cho print script/fixture reads khi callback xuất hiện, nhưng native tools có thể chạy ngoài callback. Thực tế Codex dùng native execution. Permission/source differences vẫn thuộc confinement ticket, không suy sandbox hoàn chỉnh từ các lượt này.

Trước khi thêm source probe, README root đã được thêm review marker bắt buộc theo repo. Không sửa source production, commit/push repo Crew hoặc thay rules/account config. Scratch Git commits chỉ là empty fixture baselines.

Primary archived evidence:
- [Claude actual events](https://github.com/Riso-application-factory/zed-ai-crew/issues/17#issuecomment-6108332891)
- [Codex actual events](https://github.com/Riso-application-factory/zed-ai-crew/issues/17#issuecomment-6108333203)
- [Antigravity actual events, gồm controls/failures](https://github.com/Riso-application-factory/zed-ai-crew/issues/17#issuecomment-6108333555)
- Harness/source revisions: [snapshot 1](https://github.com/Riso-application-factory/zed-ai-crew/issues/17#issuecomment-6108332116), [snapshot 2](https://github.com/Riso-application-factory/zed-ai-crew/issues/17#issuecomment-6108332369), [snapshot 3](https://github.com/Riso-application-factory/zed-ai-crew/issues/17#issuecomment-6108332634). Các phiên bản dựng lại từ edits đã khớp byte-for-byte SHA ghi lúc chạy; preflight cũ không có hash không được gán hash hồi tố.

Full sanitized JSONL/summaries, selected evidence index và versioned source giữ local cạnh `probe.py`. Snapshot issue dùng thay GitHub blob links ở local HEAD chưa publish.

## Version/profile observed

| Adapter | Backend/auth evidence | Mode/model đã dùng |
| --- | --- | --- |
| Claude ACP **0.89.1**, khác TD1 0.89.0 | SDK dependency **0.3.293**. CLI auth utility báo loggedIn=true, oauth_token, firstParty; CLI utility version 2.1.292. Không khẳng định utility đó là executable backend thực mà SDK query đã dùng. | default/manual; model config default, được mô tả Sonnet. Không tự nâng/hạ model. |
| Codex ACP **2.2.2** | Installed Codex backend **0.160.1**; native login status báo ChatGPT authenticated. Không suy plan/setting no-training. | agent; **gpt-6.1-sol**, low effort, fast off. |
| Google Antigravity ACP **1.3.0** | Native binary initialize/prompt thành công. Auth methods được quảng bá, nhưng effective saved auth type/plan không được lấy từ success; vẫn unknown. | default; **gemini-3.8-flash-high**. |

Đây là registry-default compatibility profiles của harness, không khẳng định đã reproduce mọi launch override/config của Zed hay mọi backend setting. ACP session IDs thuộc fixtures; Crew chưa có Run store để binding thật.

## What was actually observed

| Provider / channel | New context | Restart + session/load | Interpretation |
| --- | --- | --- | --- |
| Claude AIR customInstructions | Role trả `ROLE_META_R1` trước user prompt yêu cầu `USER_OVERRIDE`. | Role vẫn trả R1, không resend metadata. | Role delivery/conflict canary đạt trong các cases này; source AIR append preset hỗ trợ giải thích. Không chứng minh mọi instruction attack. |
| Claude skill chỉ ở .agents | Không quảng bá fixture; native Skill trả Unknown skill. Search rộng sau đó bị harness deny. | Không dùng failure này làm success. | Không coi native Claude auto-discover layout chung đã hoạt động. |
| Claude .claude symlink → .agents | Fixture command được quảng bá; native Skill load, resource read và actual script execution trả đúng R1. | Cùng skill được nạp lại; body/resource/actual stdout R2. | Bridge đã có positive evidence. Model báo alias .claude, filesystem realpath trỏ .agents; binding phải resolve/hash, không chỉ tin path trong câu trả lời. |
| Codex AIR + .agents skill | Role giữ R1; fixture command quảng bá; file/resource read + native execute stdout R1, exit 0. | Role giữ R1; live skill files đọc/chạy R2, exit 0. | Delivery và execution riêng đều có events; không chỉ một output marker. |
| Antigravity AIR metadata | Role trả `USER_OVERRIDE`. | Cùng kết quả sau load. | Field đã thử không giữ role trong canary này. Không kết luận mọi native hook đều không hỗ trợ. |
| Antigravity GEMINI.md role file | Prompt đối lập trả `USER_OVERRIDE`; positive control sau load, không override, trả `ROLE_FILE_R1`. | File/control cho thấy nội dung tới context, nhưng không giữ thắng trước prompt đối lập. | Phân biệt nhận role với priority/conflict behavior. Docs hiện hành không thay observed behavior của binary pin. |
| Antigravity prompt fallback | Bootstrap role trả `ROLE_FALLBACK_R1`; turn sau yêu cầu override vẫn trả cùng marker. | Không resend role; control và conflict đều trả `ROLE_FALLBACK_R1`. | Fallback giữ role trong các cases đã chạy; vẫn là user-prompt/history channel, không có API system/developer-tier bảo đảm. |
| Antigravity .agents skill | R1 body/resource đã đọc; script đầu bị harness deny do chưa xử lý payload CommandLine/Cwd, nên execution chưa đạt ở lượt đó. Rule-file phase sau có execution R1. | Sửa harness mapping, R2 body/resource/actual stdout đã đọc/chạy, exit 0. | Giữ nguyên failed control; permission harness bug không được gán thành provider thiếu skill. Native commands update chỉ plan/logout, không quảng bá fixture dù execution được quan sát. |

Claude stdout proof nằm ở completed tool result; Codex ở terminal_output_delta + terminal_exit; Antigravity ở rawOutput combinedOutput + exitCode. Marker trong script source hay assistant answer đơn lẻ không là execution proof. Available commands được tổng hợp từ toàn trace vì update có thể đến sau setup response; absence trong list không tự chứng minh không discovery.

**Revision finding:** Claude/Codex giữ role R1 từ session cũ trong khi skill file đã đổi sang R2 và được đọc/chạy sau restart/load. Thay revision giữa các phases là hành động có chủ ý của harness. Production contract phải record/check role/skill binding trước dispatch/resume; cơ chế freeze snapshot, drift/hot-reload và reconcile vẫn do configuration/plan tickets chốt, không âm thầm chấp nhận revision mới trong một Run.

## Primary documentation, with limits

- [Claude native skill locations/symlinks](https://code.claude.com/docs/en/skills): .claude project skills và symlink được mô tả; không tìm thấy .agents native guarantee tại trang này. Probe bridge là evidence của version thực, không chỉ docs.
- [Codex skill locations](https://learn.chatgpt.com/docs/build-skills): repository .agents scanning và nguồn user/admin/system; probe không hermetic đối với mọi customization global.
- [Google Zed native integration/auth](https://antigravity.google/docs/ide/extensions/zed/): hướng dẫn Registry và các auth surfaces; không chứng minh entitlement/custom orchestration/privacy profile hiện tại.
- [Google skills](https://antigravity.google/docs/skills/) và [rules](https://antigravity.google/docs/rules/): mô tả discovery theo surface, GEMINI/AGENTS rules và activation. Đó là docs hiện hành; chúng không pin toàn semantics vào ACP 1.3.0 hoặc làm canary conflict tự pass.

## Binding proposal presented — now accepted

- Claude/Codex: AIR metadata role đã versioned; .agents là nguồn skill chung, Claude có native .claude symlink trong worktree.
- Antigravity: bootstrap role versioned qua prompt fallback và kiểm lại binding khi resume/config changes. .agents skill giữ đường nguồn; không dùng metadata/file rule để claim cùng priority với Claude/Codex. Không áp thêm provider/model swap.
- Mỗi worker binding record adapter/backend nếu biết, profile/model/mode, realpath worktree, role/skill revisions/hashes và delivery channel. Case có native priority/skill capability bắt buộc mà profile chưa đáp ứng phải báo capability gap, không tự giảm yêu cầu.
- Mandatory quyền/data/side-effect/OUTPUT vẫn do code/gateway và G6 independent evidence giữ. Role text hay canary pass không là cơ chế cưỡng chế quyền. No-training/profile evidence phải đủ trước private payload.

Đây là đề xuất theo góc nhìn Architect/Engineer được Coordinator tổng hợp từ probe, không có specialist bên ngoài đã nhận hoặc approve. Human đã chấp nhận trade-off priority của user fallback trong resolution; production binding chưa được triển khai.

## Human decision recorded

**Câu hỏi đã giải quyết:** Có chấp nhận binding theo từng adapter ở trên, gồm Antigravity dùng prompt fallback có version với giới hạn priority rõ ràng không?

**Human trả lời “đồng ý”.** [Canonical resolution](https://github.com/Riso-application-factory/zed-ai-crew/issues/17#issuecomment-6109950179) chấp nhận binding khác nhau, giữ nguyên mọi gate về quyền/dữ liệu/OUTPUT và tiêu chí chất lượng. Không gán parity từ marker; tác vụ thực sự cần native priority mà chưa có evidence vẫn blocked. Không bỏ Antigravity hoặc tự chuyển billing/provider. Không còn câu hỏi Human chặn lựa chọn binding này.

## Remaining limits / next route

Đã kiểm restart qua **session/load**. Chưa kiểm ACP **session/resume** riêng, compaction, role đổi revision trên session sống, malicious file/tool injection, đủ roles/skills production, cancel đang chạy, confinement hoặc quota exhaustion. Những claims tương ứng không được suy từ probe này; active cancel/resume và cấu hình có tickets riêng. Global native customizations không bị reset; no-training/account/native-integration conditions còn unverified, private path cần preflight riêng trước thực thi.

Resolution đã được lưu và ticket đã đóng; context pointers chuyển findings vào plan, config, cancel/recovery, architecture và confinement. Frontier kế tiếp là [Kiểm chứng confinement worker và đường bypass Crew gateway](https://github.com/Riso-application-factory/zed-ai-crew/issues/18). Map chưa complete. Planning artifacts không thay implementation/quality evidence của Crew cuối cùng.
