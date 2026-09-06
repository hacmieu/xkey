# Báo cáo: Khắc phục lỗi Crash khi mở XKey Settings (Code Signature Invalid)

**Ngày:** 2026-08-25 14:23

## Hiện tượng
Người dùng báo cáo ứng dụng XKey không thể mở được (hiện popup "The application 'XKey' can't be opened"). Khi mở qua terminal nhận lỗi `Launch failed` (Code 162).

## Nguyên nhân
- Script `build_release.sh` cấu hình mặc định có bật quyền (entitlement) `com.apple.developer.ubiquity-kvstore-identifier` dành cho việc đồng bộ cài đặt qua iCloud.
- Trên máy không cài sẵn chứng chỉ Apple Developer ID, script `build_release.sh` tự động chuyển sang chế độ ký Ad-hoc (`ENABLE_CODESIGN=false`).
- macOS có cơ chế bảo vệ nghiêm ngặt (thông qua `amfid` và `taskgated`): một ứng dụng ký Ad-hoc nhưng lại chứa một "restricted entitlement" (như iCloud) sẽ bị kill ngay lập tức vì không có Provisioning Profile hợp lệ đi kèm. 

## Giải pháp đã triển khai
1. Sửa `build_release.sh`: Khi script phát hiện không có chứng chỉ và phải fallback về Ad-hoc, nó sẽ tự động đặt `ENABLE_ICLOUD_ENTITLEMENT=false`.
2. Khi cờ này tắt, script sẽ dùng `PlistBuddy` để gỡ bỏ quyền iCloud khỏi file entitlements tạm thời trước khi ký.
3. Ứng dụng Ad-hoc mới sẽ hoàn toàn "sạch" và được phép khởi chạy trên máy local mà không bị crash.
