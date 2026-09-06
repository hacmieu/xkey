# Sparkle Auto-Update Overwrite Fix & Build 20260907

**Date:** 2026-09-07 01:38  
**Build:** 20260907  

---

## 1. Nguyên nhân lỗi tái xuất hiện "Gõ nhanh"
- Tiến trình nền `Updater.app` của `Sparkle.framework` trước đó đã tải bản build mới từ upstream (`xmannv/xkey` bản `20260904`).
- Khi lệnh build kết thúc và ứng dụng tắt đi để khởi động lại, `Updater.app` đã kích hoạt cơ chế ghi đè và thay thế `/Applications/XKey.app` bằng bản `20260904` của upstream (vốn vẫn còn nguyên tính năng Gõ nhanh).
- Do đó, khi người dùng chọn "Mở XKey Settings..." thì hệ điều hành mở ứng dụng `20260904` vừa bị ghi đè.

## 2. Cách xử lý
- **Tắt Sparkle Auto-Update:** Đặt `SUEnableAutomaticChecks = false` và `SUAutomaticallyUpdate = false` trong [`XKey/Info.plist`](file:///Users/hacmieu/DevOps/xkey/XKey/Info.plist).
- **Dọn dẹp:** Xóa sạch thư mục cache `org.sparkle-project.Sparkle` và tắt cờ trong `NSUserDefaults`.
- **Nâng Version & Rebuild:** Tăng `CURRENT_PROJECT_VERSION` lên `20260907` trong [`Version.xcconfig`](file:///Users/hacmieu/DevOps/xkey/Version.xcconfig). Build và triển khai lại sạch vào `/Applications/XKey.app` và `~/Library/Input Methods/XKeyIM.app`.
- **Kiểm chứng:** Kiểm tra cấu trúc nhị phân của `/Applications/XKey.app/Contents/MacOS/XKey`, xác nhận không còn bất kỳ symbol nào của `QuickTypingSection`.
