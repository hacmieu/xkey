# Fix Macro Double Space Bug & Remove Quick Typing Feature

**Date:** 2026-09-06 23:50  
**Build:** 20260906  

---

## 1. Macro Double Space Bug Fix
- **Nguyên nhân:** Khi nhấn Space để kích hoạt Macro, nếu `settings.addSpaceAfterMacro` bật, `VNEngine` (`vAddSpaceAfterMacro == 1`) đã chèn sẵn 1 dấu cách vào mảng `result.newCharacters`. Do đó `replacementText` đã kết thúc bằng khoảng trắng khi `setMarkedText` và `commitComposition`. Nhưng `XKeyIMController.swift` lại thực hiện `return !settings.addSpaceAfterMacro` (tức `return false`), làm macOS hiểu là chưa tiêu thụ phím Space và chuyển tiếp sự kiện phím Space xuống ứng dụng, tạo ra dấu cách thứ 2.
- **Khắc phục:** 
  - Đổi giá trị trả về thành `return true` trong nhánh `result.isMacroReplacement`.
  - Cập nhật `lastKnownSelectionLocation = currentSelection.location` và gán `skipNextCursorCheck = true`.
  - Kết quả: Khi bật `addSpaceAfterMacro`, văn bản macro có chính xác 1 dấu cách; khi tắt có 0 dấu cách. Không còn hiện tượng 2 dấu space.

## 2. Loại bỏ tính năng Gõ nhanh (Quick Typing)
- **UI Settings:** Xóa bỏ mục `Gõ nhanh` khỏi Sidebar trong [`SettingsView.swift`](file:///Users/hacmieu/DevOps/xkey/XKey/UI/SettingsView.swift) (`SettingsSection.quickTyping`) và [`PreferencesView.swift`](file:///Users/hacmieu/DevOps/xkey/XKey/UI/PreferencesView.swift) (`PreferencesSection.quickTyping`), loại bỏ `QuickTypingSection` khỏi detail view.
- **Engine & Core:**
  - Vô hiệu hóa `quickTelexEnabled`, `quickStartConsonantEnabled`, `quickEndConsonantEnabled` trong [`Preferences.swift`](file:///Users/hacmieu/DevOps/xkey/XKey/Core/Models/Preferences.swift).
  - Ép `vQuickTelex = 0`, `vQuickStartConsonant = 0`, `vQuickEndConsonant = 0` trong [`VNEngine.swift`](file:///Users/hacmieu/DevOps/xkey/XKey/Core/Engine/VNEngine.swift) và [`VNEngineSettings.swift`](file:///Users/hacmieu/DevOps/xkey/XKey/Core/Engine/VNEngineSettings.swift).
  - Khóa giá trị `false` trong `XKeyIMSettings` tại [`XKeyIMController.swift`](file:///Users/hacmieu/DevOps/xkey/XKeyIM/XKeyIMController.swift).

## 3. Build & Deploy
- Đã nâng `CURRENT_PROJECT_VERSION` lên `20260906` trong `Version.xcconfig`.
- Đã build và cài đặt thành công vào `/Applications/XKey.app` và `~/Library/Input Methods/XKeyIM.app`.
