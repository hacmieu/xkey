# Kế hoạch & Thực thi Sửa lỗi "The application 'XKey' can't be opened"

**Ngày:** 2026-08-16 15:58  
**Trạng thái:** ✅ COMPLETED  

## Các bước thực hiện
1. [x] Kiểm tra nhật ký hệ thống `DiagnosticReports/XKey-*.ips` và phát hiện nguyên nhân `SIGKILL (Code Signature Invalid)` bởi `taskgated`.
2. [x] Phát hiện thiếu cờ `--deep` khi ký ad-hoc ứng dụng khiến `Sparkle.framework` có chữ ký không hợp lệ.
3. [x] Thêm cờ `--deep` vào các lệnh `codesign` ad-hoc trong `build_release.sh`.
4. [x] Ký lại sâu `/Applications/XKey.app` và kiểm tra lại bằng `codesign -vvv --deep --strict`.
5. [x] Thử nghiệm mở `open xkey://settings` và khởi chạy `/Applications/XKey.app` thành công.
