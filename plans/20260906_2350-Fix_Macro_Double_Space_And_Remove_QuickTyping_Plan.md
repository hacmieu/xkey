# Kế hoạch Sửa lỗi Macro bị thêm 2 dấu Space và Loại bỏ tính năng Gõ nhanh

**Ngày:** 2026-09-06 23:50  
**Người thực hiện:** AI Agent  

---

## 1. Mục tiêu

1. **Khắc phục lỗi Macro bị thêm 2 dấu space vào cuối câu:**
   - Khi gõ phím tắt macro (ví dụ `dc` -> `được`) và nhấn phím cách (Space), câu văn bản thay thế bị chèn 2 dấu space thay vì 1 (hoặc 0 nếu tắt tùy chọn).
   - Phân tích nguyên nhân gốc từ `VNEngine.swift` và `XKeyIMController.swift`.
   - Điều chỉnh giá trị trả về trong nhánh xử lý Macro của phím Space ở `XKeyIMController.swift` và cập nhật vị trí con trỏ đồng bộ.

2. **Loại bỏ tính năng "Gõ nhanh" khỏi bản build trên máy này:**
   - Xóa bỏ mục "Gõ nhanh" khỏi thanh danh mục Sidebar của cửa sổ Cài đặt (`SettingsView.swift` và `PreferencesView.swift`).
   - Vô hiệu hóa triệt để cấu hình Quick Telex (`cc -> ch`, `gg -> gi`,...) và Quick Consonants ở cả tầng cài đặt (`Preferences.swift`, `XKeyIMSettings`) và tầng engine (`VNEngine.swift`, `VNEngineSettings.swift`).

3. **Biên dịch, cài đặt & Đóng gói:**
   - Tăng `CURRENT_PROJECT_VERSION` lên `20260906`.
   - Chạy `build_release.sh` để biên dịch Release, cài đặt vào `~/Library/Input Methods/XKeyIM.app` và `/Applications/XKey.app`.
   - Ghi nhận nhật ký đầy đủ vào `memory/`, `plans/`, `reports/`, cập nhật Single Source of Truth `README.md` tại mỗi thư mục và git commit/push.

---

## 2. Chi tiết các bước thực hiện

### Bước 1: Sửa lỗi Macro Double Space
- **File:** `XKeyIM/XKeyIMController.swift`
- **Thay đổi:**
  - Sửa `return !settings.addSpaceAfterMacro` thành `return true`.
  - Cập nhật `lastKnownSelectionLocation = currentSelection.location` và gán `skipNextCursorCheck = true`.
  - Lý do: `VNEngine.swift` (tại `vAddSpaceAfterMacro == 1`) đã tự động nối sẵn dấu cách vào mảng ký tự `result.newCharacters`. Do đó `replacementText` đã chứa sẵn khoảng trắng cần thiết. Việc trả về `false` khiến IMKit hiểu là chưa xử lý sự kiện, chuyển tiếp phím Space xuống ứng dụng tạo thành khoảng trắng thứ 2. Trả về `true` sẽ nuốt phím Space chuẩn xác.

### Bước 2: Loại bỏ tính năng Gõ nhanh (Quick Typing)
- **File UI:**
  - `XKey/UI/SettingsView.swift`: Gỡ bỏ `case quickTyping = "Gõ nhanh"` khỏi `SettingsSection` và view detail.
  - `XKey/UI/PreferencesView.swift`: Gỡ bỏ `case quickTyping = "Gõ nhanh"` khỏi `PreferencesSection`, ánh xạ `from(tabIndex:)` và view detail.
- **File Core & Settings:**
  - `XKey/Core/Models/Preferences.swift`: Đặt mặc định `quickTelexEnabled = false`, `quickStartConsonantEnabled = false`, `quickEndConsonantEnabled = false`.
  - `XKey/Core/Engine/VNEngine.swift`: Đặt `vQuickTelex = 0`, `vQuickStartConsonant = 0`, `vQuickEndConsonant = 0`.
  - `XKey/Core/Engine/VNEngineSettings.swift`: Vô hiệu hóa `vQuickTelex`, `vQuickStartConsonant`, `vQuickEndConsonant` (ép về 0).
  - `XKeyIM/XKeyIMController.swift`: Tắt `quickTelexEnabled`, `quickStartConsonantEnabled`, `quickEndConsonantEnabled` trong `XKeyIMSettings`.
  - `XKey/EventHandling/KeyboardEventHandler.swift`: Đặt mặc định `false`.

### Bước 3: Build & Kiểm thử
- Cập nhật `Version.xcconfig`: `CURRENT_PROJECT_VERSION = 20260906`.
- Chạy `build_release.sh`.
- Xác nhận bản build cài đặt thành công vào `/Applications/XKey.app` và `~/Library/Input Methods/XKeyIM.app`.

### Bước 4: Viết báo cáo & Single Source of Truth
- Tạo file log chi tiết trong `memory/`, `plans/`, `reports/`.
- Cập nhật `README.md` tại các thư mục tương ứng.
- Thực hiện git commit và push.
