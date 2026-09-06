# Khắc phục lỗi Crash khi mở XKey Settings (Code Signature Invalid) do iCloud Entitlement

**Ngày:** 2026-08-25 14:23

## Bài học và giải pháp
- **Nguyên lý:** Restricted Entitlements (như iCloud KVS) YÊU CẦU bắt buộc phải có Apple Provisioning Profile đi kèm trong ứng dụng. Nếu ký ứng dụng kiểu Ad-hoc (tự ký không profile) mà vẫn giữ entitlement này, macOS sẽ crash ứng dụng ngay khi mở (Crash 162 - Code Signature Invalid).
- **Khắc phục:** Đã điều chỉnh `build_release.sh`. Mỗi khi script fallback về Ad-hoc signing (do thiếu chứng chỉ Developer ID trên máy hiện tại), script sẽ tự động gán `ENABLE_ICLOUD_ENTITLEMENT=false` để gỡ bỏ các quyền iCloud.
- **Kết quả:** XKey Settings khởi chạy bình thường cho các bản build local. Bản build trên CI (có chứng chỉ) vẫn sẽ giữ quyền iCloud.
