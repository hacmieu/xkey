# Memory — XKey

**Cập nhật lần cuối:** 2026-08-20 09:05

---

## Ghi chú thiết kế (Design Notes)

### 2026-08-20 — Tích hợp Commit 7a59d9a (Upstream v1.2.25) & Build 20260820
- Đã sao lưu bản build `20260816` vào `~/DevOps/xkey_backups/backup_20260816_stable/`.
- Đã cherry-pick thành công commit `7a59d9a`: Lazy AX DOM attributes, Priority-2 fallback cache, role gate cho address bar để chống freeze trên Chrome/Gmail.
- Đã chạy 17/17 Unit Tests `FocusedElementInfoLazyTests` và toàn bộ `VNEngineTests` thành công 100%.
- Đã build và cập nhật phiên bản mới `1.2.25 (20260820)` vào `~/Library/Input Methods/XKeyIM.app` và `/Applications/XKey.app`.
- Nhật ký đầy đủ: xem [`memory/20260820_0905-Cherry_Pick_7a59d9a_And_Build_v1.2.25.md`](file:///Users/hacmieu/DevOps/xkey/memory/20260820_0905-Cherry_Pick_7a59d9a_And_Build_v1.2.25.md)

### 2026-08-20 — Đánh giá Upstream Release v1.2.25 (Build 20260818)
- Phân tích bản cập nhật mới `v1.2.25-20260818` (commit `7a59d9a`) từ upstream: Tối ưu lazy AX queries cho DOM element và cache Priority-2 fallback để chống đơ/lag trên Chrome/Gmail.
- Đánh giá tính tương thích: Không có xung đột với các bản vá IMKit hiện tại của fork (Macro, cursor tracking, space lost focus).
- Bản hiện tại (`20260816`) đang hoạt động rất tốt; không có lỗi cấp bách cần sửa.
- Nhật ký đầy đủ: xem [`memory/20260820_0720-Analysis_Upstream_v1.2.25_20260818_Sync_Evaluation.md`](file:///Users/hacmieu/DevOps/xkey/memory/20260820_0720-Analysis_Upstream_v1.2.25_20260818_Sync_Evaluation.md)

### 2026-08-16 — Sửa lỗi Macro, Space thừa khi Lost Focus & Phục hồi dấu
- Đã sửa return của Macro `return !settings.addSpaceAfterMacro` tránh nạp space thừa.
- Reset engine triệt để khi `deactivateServer` và `commitComposition` để không tràn space khi lost focus.
- Bổ sung DESYNC check cho `spaceCount > 0` và đồng bộ diacritic restore.
- Nhật ký đầy đủ: xem [`memory/20260816_1410-Fix_Macro_Space_Desync_And_Diacritics.md`](file:///Users/hacmieu/DevOps/xkey/memory/20260816_1410-Fix_Macro_Space_Desync_And_Diacritics.md)

### 2026-08-16 — Sửa lỗi Apple Settings nhảy về bản 21/7 (20260721)
- Phát hiện `/Applications/XKey.app` mang bản build cũ `20260721`.
- Cập nhật bản mới `20260816` vào cả `/Applications/XKey.app` và `~/Library/Input Methods/XKeyIM.app`.
- Sửa `build_release.sh` để tự động đồng bộ `/Applications/XKey.app` và gọi `lsregister`.
- Nhật ký đầy đủ: xem [`memory/20260816_1440-Fix_Apple_Settings_Version_Fallback.md`](file:///Users/hacmieu/DevOps/xkey/memory/20260816_1440-Fix_Apple_Settings_Version_Fallback.md)

### 2026-08-16 — Sửa lỗi "The application 'XKey' can't be opened"
- Nguyên nhân: `taskgated` kill `XKey.app` do chữ ký số của `Sparkle.framework` thiếu cờ `--deep`.
- Bổ sung `--deep` vào lệnh `codesign` trong `build_release.sh` và ký lại ứng dụng.
- Nhật ký đầy đủ: xem [`memory/20260816_1558-Fix_XKey_Settings_Cant_Be_Opened.md`](file:///Users/hacmieu/DevOps/xkey/memory/20260816_1558-Fix_XKey_Settings_Cant_Be_Opened.md)

### 2026-08-14 — Fork & Commit lên GitHub hacmieu
- Commit `cb5a720` trên branch `rebuild-20260813-stable` — rebuild 20260813 + docs.
- Fork `xmannv/xkey` → `hacmieu/xkey`, push branch thành công.
- Bản stable giờ đã an toàn trên GitHub cá nhân.
- Chi tiết: xem [`20260814_1656-Fork_Commit_GitHub_Hacmieu.md`](20260814_1656-Fork_Commit_GitHub_Hacmieu.md)

### 2026-08-14 — Rebuild 20260813 Stable Build
- Bản 20260813 (Macro XKeyIM + cursor fix + version linking) bị mất do `git reset --hard HEAD` — chưa từng được commit.
- Re-implement toàn bộ từ documentation 20260813, build & deploy thành công, `CFBundleVersion = 20260813`.
- Chi tiết: xem [`20260814_1640-Rebuild_20260813_Stable_Build.md`](20260814_1640-Rebuild_20260813_Stable_Build.md)

### 2026-08-14 — Restore Stable Build v1.2.24-20260721
- Xóa hẳn các thay đổi chưa commit vì gây regression.
- Checkout về tag `v1.2.24-20260721`, build & deploy thành công.
- Chi tiết: xem [`20260814_1529-Restore_Stable_Build.md`](20260814_1529-Restore_Stable_Build.md)

### 2026-08-14 — Deploy Fix: Version Cache Issue
- Menu hiển thị `20260721` vì `cp -R` không xóa app cũ.
- Fix: `rm -rf` trước khi copy, verify `CFBundleVersion`.
- Skill `xkey-build-deploy` đã cập nhật.
- Chi tiết: xem [`20260814_1510-Deploy_Fix_Version_Cache.md`](20260814_1510-Deploy_Fix_Version_Cache.md)

### 2026-08-14 — Version Bump & Skill xkey-build-deploy
- Bump `CURRENT_PROJECT_VERSION` lên `20260814`.
- Build & deploy thành công.
- Tạo skill `xkey-build-deploy` để tự động hóa build/deploy sau này.
- Chi tiết: xem [`20260814_1459-Version_Bump_and_Skill_Created.md`](20260814_1459-Version_Bump_and_Skill_Created.md)

### 2026-08-14 — Punctuation Context Fix Deployed
- Triển khai fix: bỏ qua cursor-move detection trong 50ms sau dấu câu/symbol.
- Build & deploy thành công, cần re-select input source.
- Chi tiết: xem [`20260814_1455-Punctuation_Context_Fix_Deployed.md`](20260814_1455-Punctuation_Context_Fix_Deployed.md)

### 2026-08-14 — Punctuation Context Bug
- Sau dấu `,` `.`, từ tiếp theo hay lỗi vì `cursorMovedSinceReset` bị bật bởi event cursor từ editor.
- Đề xuất: reset flag trên phím printable đầu tiên sau punctuation, tăng tolerance cursor detection.
- Chi tiết: xem [`20260814_1450-Punctuation_Context_Bug_Analysis.md`](20260814_1450-Punctuation_Context_Bug_Analysis.md)

### 2026-08-14 — Timestamp Convention cho ngày 14/08
- Tất cả file log dùng định dạng `YYYYMMDD_HHMM-<Nội dung>.md`.
- Mỗi file mới phải cập nhật ngay vào README.md của thư mục.
- Chi tiết: xem [`20260814_1429-Timestamp_Convention_1408.md`](20260814_1429-Timestamp_Convention_1408.md)

### 2026-08-14 — Log Analysis 14:15: 4 Typing Issues
- `skipNextCursorCheck` đang skip quá nhiều trường hợp (actual=1 expected=142).
- Macro log leak plaintext user content.
- `handleVowelKey: no pattern matched` với nguyên âm đơn lẻ.
- English detection vẫn aggressive, `tempDisableKey` kéo dài sang từ mới.
- Chi tiết: xem [`20260814_1418-Log_Analysis_1415_Typing_Issues.md`](20260814_1418-Log_Analysis_1415_Typing_Issues.md)
- Build mới đã deploy (XKeyIM Debug 2026-08-14).

### 2026-08-14 — XKeyIM Debug Review: 6 Optimization Points
- Tổng hợp từ git diff, log structure, và các file markdown trong ngày.
- 6 điểm chính: cursor false-positive, macro IMKit, version config, log noise, English token retry, Release signing.
- Chi tiết: xem [`20260814_1418-XKeyIM_Debug_Review_Optimization_Points.md`](20260814_1418-XKeyIM_Debug_Review_Optimization_Points.md)

### 2026-08-14 — English Token Retry Analysis
- `adsense` có thể bị `s` xử lý như dấu sắc vì detector real-time chỉ xét prefix bất hợp lệ.
- Đề xuất telemetry local không lưu plaintext: hash token, độ dài, trigger và dạng correction.
- Chi tiết: xem [`20260814_1110-English_Token_Retry_Analysis.md`](20260814_1110-English_Token_Retry_Analysis.md)

### 2026-08-14 — Log-Based Optimization (đề xuất khả thi)
- Log XKeyIM có cấu trúc máy đọc được, đủ để tối ưu dựa trên data.
- 9 category: ENGINE(650), CURSOR(641), OVERLAY(515), BACKSPACE(453), TIMING(188), ...
- Kế hoạch: thêm duration vào TIMING, giảm noise ENGINE/OVERLAY, script analyzer.
- Chi tiết: xem [`memory/20260814_1052-Log_Based_Optimization_Idea.md`](file:///Users/hacmieu/DevOps/xkey/memory/20260814_1052-Log_Based_Optimization_Idea.md)

### 2026-08-13 — Cursor Tracking False-Positive Fix (VS Code)
- VS Code trả về `selectedRange().location` sai trong 1 event cycle sau `setMarkedText()`.
- Gây false-positive "CURSOR MOVED" → engine reset → mất state tone → bỏ dấu thất bại.
- Fix: thêm flag `skipNextCursorCheck`, bỏ qua cursor check 1 event sau `setMarkedText`/`commitComposition`.
- Nhật ký đầy đủ: xem [`memory/20260813_2256-Cursor_Tracking_VSCode_Fix.md`](file:///Users/hacmieu/DevOps/xkey/memory/20260813_2256-Cursor_Tracking_VSCode_Fix.md)

### 2026-08-13 — Phân tích Macro System

- **Phát hiện chính:** Hệ thống Macro **hoàn toàn chưa được implement trong XKeyIM** (IMKit / chế độ gạch dưới marked text).
- Macro chỉ hoạt động ở chế độ CGEvent (XKey main app).
- 4 thiếu sót cụ thể: settings, engine config, MacroManager init, Space handler.
- Chi tiết đầy đủ: xem [`reports/20260813_1648-Macro_System_Analysis_XKeyIM.md`](file:///Users/hacmieu/DevOps/xkey/reports/20260813_1648-Macro_System_Analysis_XKeyIM.md)

### 2026-08-13 — Sửa lỗi Macro System
- Đã thiết lập cài đặt Macro trong `XKeyIMSettings`.
- Khởi tạo và nạp `MacroManager` trong `XKeyIMController`.
- Cập nhật `ProcessResult.isMacroReplacement` để XKeyIM hứng được kết quả Macro.
- Thay thế thành công từ khóa Macro trong luồng `Space` (0x31).
- Nhật ký đầy đủ: xem [`memory/20260813_2200-Macro_Fix_Completed.md`](file:///Users/hacmieu/DevOps/xkey/memory/20260813_2200-Macro_Fix_Completed.md)

### 2026-08-13 — Liên kết Version.xcconfig cho XKeyIM
- Sửa `project.pbxproj` gán `baseConfigurationReference` cho target `XKeyIM`.
- Cập nhật phiên bản hiển thị trên menu thành `1.2.24 (20260813)`.
- Nhật ký đầy đủ: xem [`memory/20260813_2226-Fix_XKeyIM_Version_Config_Linking.md`](file:///Users/hacmieu/DevOps/xkey/memory/20260813_2226-Fix_XKeyIM_Version_Config_Linking.md)

### 2026-08-16 — Sửa lỗi Macro, Space thừa khi Lost Focus & Phục hồi dấu
- Đã sửa return của Macro `return !settings.addSpaceAfterMacro` tránh nạp space thừa.
- Reset engine triệt để khi `deactivateServer` và `commitComposition` để không tràn space khi lost focus.
- Bổ sung DESYNC check cho `spaceCount > 0` và đồng bộ diacritic restore.
- Nhật ký đầy đủ: xem [`memory/20260816_1410-Fix_Macro_Space_Desync_And_Diacritics.md`](file:///Users/hacmieu/DevOps/xkey/memory/20260816_1410-Fix_Macro_Space_Desync_And_Diacritics.md)

### Kiến trúc Macro (reference)

| Component | File | Mô tả |
|-----------|------|-------|
| `MacroManager` | `XKey/Core/Engine/MacroManager.swift` | Storage, lookup, import/export |
| `VNEngineMacro` | `XKey/Core/Engine/VNEngineMacro.swift` | Engine extension cho macro |
| `VNEngine.processWordBreak()` | `XKey/Core/Engine/VNEngine.swift:3380` | Trigger macro khi Space |
| `KeyboardEventHandler` | `XKey/EventHandling/KeyboardEventHandler.swift` | CGEvent mode init & injection |
| `XKeyIMController` | `XKeyIM/XKeyIMController.swift` | IMKit mode — **thiếu macro** |
