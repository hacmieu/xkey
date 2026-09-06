# Kế hoạch: Áp dụng lại fix lỗi Crash iCloud Entitlement

**Ngày:** 2026-08-25 14:50

## Vấn đề
- Sau khi thực hiện lệnh Rollback bằng `git restore` theo yêu cầu của người dùng, toàn bộ mã nguồn bao gồm file `build_release.sh` cũng bị revert.
- Điều này vô tình làm mất đoạn code fix lỗi crash do iCloud Entitlement khi build bằng chứng chỉ Ad-hoc. Do đó ứng dụng Settings của XKey bị crash "Application can't be opened" (Mã 162).

## Giải pháp
- Chỉnh sửa lại `build_release.sh`, thiết lập `ENABLE_ICLOUD_ENTITLEMENT=false` trong trường hợp script phải fallback về Ad-hoc signing.
- Đợi build xong để người dùng có thể mở XKey Settings bình thường.
