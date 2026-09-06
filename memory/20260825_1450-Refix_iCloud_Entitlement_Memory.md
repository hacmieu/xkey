# Khôi phục fix lỗi Crash Settings do iCloud Entitlement sau Rollback

**Ngày:** 2026-08-25 14:50

## Tình huống
- Lệnh Rollback mã nguồn trước đó đã không may gỡ luôn đoạn code fix lỗi iCloud Entitlement (Code Signature Invalid 162) bên trong file `build_release.sh`. 
- Nguyên nhân: Các công cụ như git checkout/restore thường khôi phục theo phiên bản file thay vì loại trừ tính năng theo ngữ nghĩa.

## Giải pháp 
- Bổ sung lại logic bắt buộc: khi sử dụng `Ad-hoc signing`, tự động áp dụng `ENABLE_ICLOUD_ENTITLEMENT=false`.
- Cơ chế `PlistBuddy` trong script sẽ xóa key iCloud KVS khỏi XKeyRelease.entitlements, giúp hệ điều hành macOS không kill ứng dụng vì lý do sai lệch giữa entitlement và chứng chỉ (Ad-hoc không có profile).
