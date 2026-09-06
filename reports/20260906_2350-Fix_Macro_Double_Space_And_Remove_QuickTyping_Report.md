# Báo cáo Sửa lỗi Macro bị chèn 2 dấu Space và Loại bỏ tính năng Gõ nhanh

**Ngày:** 2026-09-06 23:50  
**Người thực hiện:** AI Agent  
**Mục tiêu:** Kiểm tra và khắc phục lỗi Macro bị thêm 2 space, sau đó loại bỏ tính năng Gõ nhanh (Quick Typing) khỏi bản build trên máy này.

---

## 1. Phân tích nguyên nhân lỗi Macro bị thêm 2 dấu space (Root Cause Analysis)

### Hiện tượng
Khi người dùng nhập phím tắt Macro (ví dụ: `dc` -> `được`) và nhấn phím cách (`Space`, mã phím `0x31`):
- Từ khóa `dc` được thay thế bằng văn bản mở rộng `được`.
- Nhưng ở cuối câu/từ macro luôn xuất hiện **2 dấu cách (double space)** thay vì 1 dấu cách (khi bật tùy chọn thêm khoảng trắng) hoặc 0 dấu cách.

### Cơ chế hoạt động & Nguyên nhân gốc
1. **Tại `VNEngine.swift` (lines 3824–3832):**
   ```swift
   if vAddSpaceAfterMacro == 1 {
       if let character = currentCharacter {
           // currentCharacter là phím kích hoạt (Space)
           result.newCharacters.append(VNCharacter(character: character))
       }
   }
   ```
   Khi tùy chọn `addSpaceAfterMacro` được bật (`vAddSpaceAfterMacro == 1`), `VNEngine` **đã chủ động chèn sẵn ký tự Space vào cuối mảng `result.newCharacters`**.
   
2. **Tại `XKeyIMController.swift` (lines 719–727):**
   Chuỗi `replacementText` được tạo từ `result.newCharacters`:
   ```swift
   let replacementText = result.newCharacters
       .map { $0.unicode(codeTable: .unicode) }
       .joined()
   setMarkedText(replacementText, client: client)
   commitComposition(client)
   ```
   Do đó, văn bản `replacementText` đã chứa sẵn 1 dấu cách ở cuối và được commit trực tiếp vào trường soạn thảo (đây là Space thứ nhất).

3. **Sai lệch tại giá trị trả về của `XKeyIMController.swift` (line 750 cũ):**
   ```swift
   return !settings.addSpaceAfterMacro
   ```
   - Trong macOS Input Method Kit (IMKit), phương thức `handle(_ event: NSEvent!, client: Any!) -> Bool` yêu cầu trả về:
     - `true`: Input Method đã xử lý và tiêu thụ sự kiện phím này (swallow event), hệ điều hành **không** chuyển tiếp phím đến app đích.
     - `false`: Input Method **chưa** xử lý hoặc bỏ qua sự kiện, hệ điều hành sẽ **chuyển tiếp phím này trực tiếp vào app đích**.
   - Khi `settings.addSpaceAfterMacro == true`, biểu thức `!settings.addSpaceAfterMacro` cho kết quả `false`!
   - Hậu quả: macOS chuyển tiếp phím Space gốc xuống app đích, khiến app đích tự gõ thêm 1 dấu space nữa (Space thứ hai).
   - Tổng cộng: **1 space (từ `replacementText`) + 1 space (do IMKit pass-through event) = 2 spaces!**

---

## 2. Giải pháp kỹ thuật

### Sửa lỗi Macro Double Space
1. Sửa giá trị trả về tại nhánh xử lý Macro trong `XKeyIMController.swift`:
   - Luôn trả về `return true` để tiêu thụ phím Space, ngăn macOS chèn thêm phím Space của hệ thống.
   - Khi `addSpaceAfterMacro == true`: `VNEngine` cung cấp 1 space bên trong `replacementText`, IM tiêu thụ phím Space -> Kết quả: đúng 1 space.
   - Khi `addSpaceAfterMacro == false`: `VNEngine` không chèn space, IM tiêu thụ phím Space -> Kết quả: 0 space.
2. Cập nhật vị trí con trỏ dự đoán:
   - Đặt `lastKnownSelectionLocation = currentSelection.location` (vì văn bản macro đã được commit hoàn tất, không còn sự kiện space trễ từ hệ thống).
   - Đặt `skipNextCursorCheck = true` để tránh hiện tượng false-positive cursor move trên các editor như VS Code.

### Loại bỏ tính năng Gõ nhanh (Quick Typing)
Theo yêu cầu loại bỏ tính năng Gõ nhanh khỏi bản build này:
1. **Giao diện người dùng (UI):**
   - [`XKey/UI/SettingsView.swift`](file:///Users/hacmieu/DevOps/xkey/XKey/UI/SettingsView.swift): Xóa `case quickTyping = "Gõ nhanh"` khỏi `SettingsSection` (Sidebar) và loại bỏ `QuickTypingSection` khỏi detail view.
   - [`XKey/UI/PreferencesView.swift`](file:///Users/hacmieu/DevOps/xkey/XKey/UI/PreferencesView.swift): Xóa `case quickTyping = "Gõ nhanh"` khỏi `PreferencesSection`, xóa khỏi `from(tabIndex:)` và detail view.
2. **Cấu hình & Engine:**
   - [`XKey/Core/Models/Preferences.swift`](file:///Users/hacmieu/DevOps/xkey/XKey/Core/Models/Preferences.swift): Đặt mặc định `quickTelexEnabled = false`, `quickStartConsonantEnabled = false`, `quickEndConsonantEnabled = false`.
   - [`XKey/Core/Engine/VNEngine.swift`](file:///Users/hacmieu/DevOps/xkey/XKey/Core/Engine/VNEngine.swift): Đặt `vQuickTelex = 0`, `vQuickStartConsonant = 0`, `vQuickEndConsonant = 0`.
   - [`XKey/Core/Engine/VNEngineSettings.swift`](file:///Users/hacmieu/DevOps/xkey/XKey/Core/Engine/VNEngineSettings.swift): Khóa cố định `vQuickTelex = 0`, `vQuickStartConsonant = 0`, `vQuickEndConsonant = 0` trong `updateSettings`.
   - [`XKeyIM/XKeyIMController.swift`](file:///Users/hacmieu/DevOps/xkey/XKeyIM/XKeyIMController.swift): Ép `quickTelexEnabled = false`, `quickStartConsonantEnabled = false`, `quickEndConsonantEnabled = false` trong cả `reload()` và `updateEngineSettings()`.
   - [`XKey/EventHandling/KeyboardEventHandler.swift`](file:///Users/hacmieu/DevOps/xkey/XKey/EventHandling/KeyboardEventHandler.swift): Đặt `quickTelexEnabled = false`.

---

## 3. Kết quả Build & Triển khai

1. **Phiên bản:**
   - Nâng `CURRENT_PROJECT_VERSION` trong `Version.xcconfig` lên `20260906`.
2. **Biên dịch & Ký số Ad-hoc:**
   - Chạy `./build_release.sh` biên dịch Universal (x86_64 + arm64) thành công 100%.
   - Ký số Ad-hoc đầy đủ entitlements cho cả `XKey.app` và `XKeyIM.app`.
3. **Cài đặt hệ thống:**
   - Đã cập nhật vào `~/Library/Input Methods/XKeyIM.app` (build `20260906`).
   - Đã cập nhật vào `/Applications/XKey.app` (build `20260906`).
   - Đã đăng ký lại với LaunchServices (`lsregister`).
   - Đã khởi động lại ứng dụng XKey mới.
