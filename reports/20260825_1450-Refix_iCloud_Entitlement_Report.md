# Báo cáo: Sửa lại lỗi Crash Settings do iCloud Entitlement

**Ngày:** 2026-08-25 14:50

## Các thao tác đã thực hiện
- Đã chỉnh sửa `build_release.sh` để thêm logic tự động nhận biết: Nếu không có chứng chỉ Developer ID (chạy fallback về Ad-hoc signing) thì lập tức gán `ENABLE_ICLOUD_ENTITLEMENT=false`.
- Điều này khiến script tự động xóa các mục `com.apple.developer.ubiquity-kvstore-identifier` và `com.apple.developer.icloud-services` khỏi file entitlements trước khi ký Ad-hoc.
- Ứng dụng đã được biên dịch và cài đặt lại. Người dùng có thể khởi động XKey Settings bình thường.
