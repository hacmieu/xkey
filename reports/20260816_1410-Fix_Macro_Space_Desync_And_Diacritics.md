# Báo cáo Phân tích & Phục hồi lỗi Macro, Space thừa khi Lost Focus & Dấu quay lại sau khi sửa

**Ngày:** 2026-08-16 14:10  
**Người thực hiện:** AI Agent  

## 1. Phân tích nguyên nhân gốc (Root Cause Analysis)

Đã đối chiếu trực tiếp nhật ký Debug (`/Users/hacmieu/XKey_Debug.log`) và kiểm tra luồng xử lý mã nguồn:

### Lỗi 1: Thi thoảng Macro không chạy được & bị chèn Space thừa
- **Nguyên nhân:** Ở phím `Space` (`0x31`) trong `XKeyIMController.swift`, nhánh xử lý Macro `if result.isMacroReplacement` trả về `return false` cứng.
- **Hậu quả:** Trong macOS IMKit, trả về `false` làm cho hệ thống tin rằng XKeyIM *chưa xử lý* phím Space. Hệ thống sẽ chèn thêm 1 phím Space thứ hai vào tài liệu ngay sau văn bản macro.
- **Khắc phục:** Sửa `return !settings.addSpaceAfterMacro`. Nếu tắt option thêm space sau macro, trả về `true` để nuốt phím Space hệ thống; nếu bật thì mới trả về `false`.

### Lỗi 2: Xuất hiện một đống Space ở phía sau khi Lost Focus / Chuyển App / Đổi trường nhập
- **Nguyên nhân:** Khi ứng dụng bị mất focus hoặc chuyển trường nhập, macOS gọi `deactivateServer(_ sender: Any!)` hoặc `commitComposition`. Lúc này XKeyIM đẩy văn bản đệm ra nhưng **không gọi `engine.reset()`**. Engine vẫn lưu giữ lịch sử phím Space (`spaceCount = 1` hoặc mảng `history` các khoảng trắng từ trước).
- **Hậu quả:** Khi người dùng quay lại hoặc bấm Backspace / gõ phím mới ở trường nhập mới, bộ gõ khôi phục các khoảng trắng tồn đọng trong `history` và chèn hàng loạt khoảng trắng vào document.
- **Khắc phục:** 
  - Gọi `engine.reset()`, `composingText = ""`, `currentWordLength = 0`, `markedTextStartLocation = NSNotFound` triệt để trong `deactivateServer` và `commitComposition`.
  - Cập nhật điều kiện phát hiện lệch nhịp (DESYNC) trong `handle`: Nếu `composingText` rỗng nhưng `engine.spaceCount > 0` hoặc buffer không rỗng, tự động thực hiện `engine.resetWithCursorMoved()`.

### Lỗi 3: Sửa chữ xong thì dấu đã xóa tự dưng xuất hiện sau ký tự mới
- **Nguyên nhân:** Khi người dùng xóa/sửa ký tự có dấu (ví dụ sửa `tới` thành `tớ` rồi gõ `n`), do `commitComposition` trước đó đã xóa `composingText = ""` nhưng `engine` vẫn chưa xóa đệm hoặc phát hiện di chuyển con trỏ chưa đúng lúc, `VNEngine` lầm tưởng là nhịp khôi phục từ lịch sử và nối thêm các ký tự dấu cũ (`vRestore` path).
- **Khắc phục:** Đồng bộ lại việc khôi phục trạng thái bộ gõ khi thay đổi con trỏ/xóa từ, reset sạch bộ đệm đệm tạm khi con trỏ thoát khỏi vùng marked text.

---

## 2. Kết quả & Đóng gói

- Đã sửa mã nguồn trong `XKeyIMController.swift` và `Version.xcconfig`.
- Đã nâng `CURRENT_PROJECT_VERSION` lên **`20260816`**.
- Đã chạy script `build_release.sh` để biên dịch bản mới và tự động cài đặt vào `~/Library/Input Methods/XKeyIM.app`.
