# Kế hoạch & Thực thi Sửa lỗi Macro, Space khi Lost Focus và Phục hồi Dấu

**Ngày:** 2026-08-16 14:10  
**Trạng thái:** ✅ COMPLETED  

## Các bước thực hiện
1. [x] Phân tích log `~/XKey_Debug.log` và xác định 3 nguyên nhân gây ra lỗi.
2. [x] Fix Macro Space Return: cập nhật `return !settings.addSpaceAfterMacro` trong `XKeyIMController.swift`.
3. [x] Fix Focus Loss Cleanup: reset `engine` và trạng thái `composingText` trong `deactivateServer` và `commitComposition`.
4. [x] Fix DESYNC Check: bổ sung kiểm tra `spaceCount > 0` khi `composingText.isEmpty`.
5. [x] Cập nhật phiên bản build thành `20260816` trong `Version.xcconfig`.
6. [x] Thực thi `build_release.sh` để cài đặt bản mới vào `~/Library/Input Methods/XKeyIM.app`.
