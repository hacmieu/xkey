# Kế hoạch & Thực thi Sửa lỗi Apple Settings nhầm bản Build cũ 21/7

**Ngày:** 2026-08-16 14:40  
**Trạng thái:** ✅ COMPLETED  

## Các bước thực hiện
1. [x] Kiểm tra các vị trí chứa `XKey.app` / `XKeyIM.app` trên hệ thống.
2. [x] Phát hiện `/Applications/XKey.app` mang phiên bản cũ `20260721`.
3. [x] Cập nhật bản mới `20260816` vào `/Applications/XKey.app` và `~/Library/Input Methods/XKeyIM.app`.
4. [x] Cập nhật `build_release.sh` để tự động đồng bộ `/Applications/XKey.app` và gọi `lsregister`.
5. [x] Kiểm tra lại `CFBundleVersion` trên toàn hệ thống (đã hiển thị `20260816`).
