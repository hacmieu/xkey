# Tổng hợp Sửa lỗi Macro cho XKeyIM

**Ngày:** 2026-08-13 22:00  
**Người thực hiện:** AI Agent  

## Bối cảnh
Tính năng Macro trên ứng dụng XKey không hoạt động ở chế độ XKeyIM (bộ gõ trực tiếp). Dù bật hay tắt thì gõ phím tắt (vd: `rrr`) vẫn không hiển thị văn bản thay thế.

## Nguyên nhân (Root Cause)
1. **Thiếu cài đặt:** `XKeyIMSettings` chưa ánh xạ cài đặt macro từ SharedSettings.
2. **Thiếu nạp Macro:** `XKeyIMController` chưa khởi tạo và load `MacroManager` bằng dữ liệu từ `XKey.macroData`.
3. **Thiếu Injection Text:** Ở luồng phím Space (`0x31`) trong `XKeyIMController`, khi `engine.processWordBreak(character: " ")` trả về một Macro match (engine đã reset), mã nguồn không xử lý trường hợp này, dẫn đến nội dung macro bị phớt lờ, hoặc lầm tưởng là lệnh Undo (Spell check restore).

## Thay đổi thực hiện
- Sửa `XKeyIMSettings` (thêm `macroEnabled`, `autoCapsMacro`, v.v.)
- Khởi tạo `MacroManager` trong `XKeyIMController.init` và truyền setting vào Engine.
- Sửa kiểu trả về `ProcessResult` trong `VNEngine.swift`, thêm biến `isMacroReplacement`.
- Bắt `isMacroReplacement == true` tại sự kiện Space ở `XKeyIMController`, truyền `replacementText` xuống trực tiếp cho hệ thống Text Input của macOS (`setMarkedText` hoặc `replaceTextDirect`).
- Nâng `CURRENT_PROJECT_VERSION` lên `20260813` (trong `Version.xcconfig`) để menu bar hiện đúng ngày phân biệt bản Build.

## Kết quả
Sau khi build (lệnh `bash build_release.sh`), file `XKeyIM.app` được chép vào `~/Library/Input Methods/`. 
Kiểm tra trên Menu bar của XKey đã hiển thị "Phiên bản 1.2.24 (20260813)". Macro hoạt động bình thường kể cả trong các Editor native của hệ thống.
