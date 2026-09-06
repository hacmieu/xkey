# Kế hoạch tự động khắc phục lỗi Entitlement khi build Ad-hoc

**Ngày:** 2026-08-25 14:23

## Vấn đề
Khi build XKey bằng kịch bản `build_release.sh` trên một máy không có chứng chỉ Apple Developer ID, script sẽ tự động chuyển về chế độ "Ad-hoc signing" (ký không có Profile).
Tuy nhiên, mặc định script vẫn giữ quyền (entitlement) `com.apple.developer.ubiquity-kvstore-identifier` dành cho iCloud. 
Việc ứng dụng được ký Ad-hoc nhưng lại yêu cầu một quyền bị hạn chế (restricted entitlement) sẽ khiến dịch vụ `amfid` và `taskgated` của macOS từ chối và chặn khởi chạy ứng dụng (Crash Code 162 - Launch failed).

## Giải pháp (Phương án lâu dài)
Cần sửa đổi `build_release.sh` để:
1. Nhận biết khi nào ứng dụng đang bị ép xuống chế độ Ad-hoc (`ENABLE_CODESIGN=false`).
2. Nếu đang ở chế độ Ad-hoc, buộc `ENABLE_ICLOUD_ENTITLEMENT=false` một cách tự động (trừ khi có override cụ thể nào đó) để quá trình xóa entitlement iCloud được kích hoạt.
3. Điều này đảm bảo rằng các developer khi build máy nội bộ không có chứng chỉ sẽ không bao giờ bị văng ứng dụng khi mở `XKey Settings`.

## Các bước thực hiện
1. Sửa `build_release.sh` dòng thiết lập `ENABLE_CODESIGN=false` khi không tìm thấy chứng chỉ, thêm dòng gán `ENABLE_ICLOUD_ENTITLEMENT=false`.
2. Ghi nhận log.
