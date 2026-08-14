# XKeyIM Debug Review — Report

**Date:** 2026-08-14 14:18  
**Status:** Review complete

## Summary
Đọc `memory/README.md`, git diff, các file markdown liên quan, và phân tích log structure để tìm điểm tối ưu cho XKeyIM.

## Findings

| # | Điểm tối ưu | File chính | Trạng thái | Hành động |
|---|-------------|-----------|-----------|-----------|
| 1 | Cursor tracking false-positive (VS Code) | `XKeyIM/XKeyIMController.swift` | Fix đã deploy, chưa verify | Test `dd ow`, `thoong`, `heer`, `cuar` |
| 2 | Macro system trong XKeyIM | `XKeyIM/XKeyIMController.swift`, `VNEngine.swift` | Implemented | Thêm unit test IMKit |
| 3 | Version config linking | `Version.xcconfig`, `project.pbxproj` | Done | Document build command |
| 4 | Log noise | `DebugLogger.swift`, `IMKitDebugger.swift` | Identified | Giảm OVERLAY, thêm duration TIMING |
| 5 | English token retry (`adsense`) | `VNEngineEnglishDetection.swift` | Root cause known | Telemetry local + learn |
| 6 | Release signing | `XKey.xcodeproj` | Blocked | Fix hoặc dùng Debug-only |

## Log Structure (confirmed)
- `[ENGINE]` 650, `[CURSOR]` 641, `[OVERLAY]` 515, `[BACKSPACE]` 453, `[TIMING]` 188
- `[TIMING]` chưa có duration — cần thêm để đo latency

## Diff Summary
- `Version.xcconfig`: `CURRENT_PROJECT_VERSION` 20260721 → 20260813
- `project.pbxproj`: XKeyIM target now references `Version.xcconfig`
- `VNEngine.swift`: added `ProcessResult.isMacroReplacement`
- `XKeyIMController.swift`: added `skipNextCursorCheck`, macro manager init, macro space handler

## Next Steps
1. User verifies cursor fix
2. Add macro IMKit tests
3. Reduce log noise
4. Implement English retry telemetry
5. Resolve Release signing

## Related
- [memory/20260814_1418-XKeyIM_Debug_Review_Optimization_Points.md](../memory/20260814_1418-XKeyIM_Debug_Review_Optimization_Points.md)
- [plans/20260814_1418-XKeyIM_Optimization_Plan.md](../plans/20260814_1418-XKeyIM_Optimization_Plan.md)