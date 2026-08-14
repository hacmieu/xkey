# Log-Based Optimization — XKeyIM

**Date:** 2026-08-14 10:52  
**Status:** 💡 Đề xuất (chưa thực thi)

## Goal
Biến `~/XKey_Debug.log` thành công cụ phân tích để tối ưu XKeyIM dựa trên data thực.

## Tasks
- [ ] Khảo sát log, thống kê category + tần suất
- [ ] Ghi duration (ms) vào `[TIMING]` logs
- [ ] Giảm noise `[OVERLAY]` (log 1 lần/app, không mỗi key)
- [ ] Thêm flag-level cho `[ENGINE]`
- [ ] Viết `Tools/log_analyzer.swift` đọc & phân tích log
- [ ] Baseline hiệu năng (time-to-first-char, per-keystroke latency)

## Files liên quan
- `Shared/DebugLogger.swift`
- `XKeyIM/IMKitDebugger.swift`
- `XKeyIM/XKeyIMController.swift`
- `Tools/generate_menu_icons.swift` (tham khảo cách tạo tool script)