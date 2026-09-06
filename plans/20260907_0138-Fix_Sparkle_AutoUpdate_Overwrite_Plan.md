# Kế hoạch Khắc phục Sparkle Auto-Update tự động ghi đè bản Upstream làm xuất hiện lại "Gõ nhanh"

**Ngày:** 2026-09-07 01:38  
**Người thực hiện:** AI Agent  

---

## 1. Mục tiêu
- Điều tra lý do vì sao sau khi đã loại bỏ tính năng "Gõ nhanh", menu hệ thống của người dùng vẫn hiển thị `Phiên bản 1.2.25 (20260904)` và cửa sổ Cài đặt vẫn còn tab "Gõ nhanh".
- Tìm nguyên nhân gốc: Phát hiện tiến trình nền `Sparkle.framework` (`Updater.app`) đã tải sẵn bản phát hành mới từ upstream (`xmannv/xkey` bản `20260904` phát hành ngày 04/09/2026) và tự động tráo đổi `/Applications/XKey.app` ngay sau khi bản build cục bộ hoàn tất.
- Vô hiệu hóa tính năng tự động kiểm tra và cập nhật của Sparkle (`SUEnableAutomaticChecks = false`, `SUAutomaticallyUpdate = false`) trong `Info.plist` và `NSUserDefaults`.
- Xóa sạch cache cập nhật đang chờ của Sparkle.
- Nâng `CURRENT_PROJECT_VERSION` lên `20260907`, biên dịch lại bản Release và cài đặt sạch vào hệ thống.

---

## 2. Các bước thực hiện
1. **Chỉnh sửa cấu hình Sparkle trong `XKey/Info.plist`:**
   - Đặt `SUEnableAutomaticChecks = false`.
   - Bổ sung `SUAutomaticallyUpdate = false`.
2. **Dọn dẹp hệ thống:**
   - Hủy bỏ các tiến trình Sparkle đang chạy (`Autoupdate`, `Updater`).
   - Xóa thư mục cache `/Users/hacmieu/Library/Caches/com.codetay.XKey/org.sparkle-project.Sparkle/`.
   - Cập nhật cấu hình người dùng qua lệnh `defaults write`.
3. **Nâng phiên bản & Biên dịch:**
   - Sửa `Version.xcconfig`: `CURRENT_PROJECT_VERSION = 20260907`.
   - Chạy `ENABLE_SPARKLE_SIGN=false ./build_release.sh`.
4. **Kiểm tra và xác thực:**
   - Kiểm tra `CFBundleVersion` của `/Applications/XKey.app` và `~/Library/Input Methods/XKeyIM.app` đạt `20260907`.
   - Kiểm tra ký hiệu và chuỗi nhị phân xác nhận `QuickTypingSection` đã biến mất hoàn toàn khỏi ứng dụng.
5. **Đồng bộ tài liệu & Git:**
   - Viết báo cáo và nhật ký ghi nhớ.
   - Cập nhật các file `README.md` trục chính.
   - Commit và push lên repository `hacmieu/xkey`.
