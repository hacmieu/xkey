# Báo cáo Lỗi Apple Settings tự động quay về bản Build cũ 21/7 (20260721)

**Ngày:** 2026-08-16 14:40  
**Người thực hiện:** AI Agent  

## Phân tích nguyên nhân
Khi người dùng vào **Apple System Settings -> Keyboard -> Input Sources**, thực hiện xóa (`-`) và thêm lại (`+`) bộ gõ XKey:
1. macOS quét danh sách ứng dụng trong thư mục `/Applications/` để đăng ký Input Method.
2. Trước đó, trong thư mục `/Applications/XKey.app` tồn tại ứng dụng cũ (ngày 21/7/2026, version `20260721`).
3. Script build cũ chỉ ghi đè vào `~/Library/Input Methods/XKeyIM.app` mà không ghi đè bản mới vào `/Applications/XKey.app`.
4. Vì vậy, mỗi khi thêm lại bộ gõ từ Cài đặt hệ thống (Apple Settings), macOS LaunchServices sẽ load lại bản `XKeyIM.app` cũ nằm bên trong `/Applications/XKey.app/Contents/Resources/XKeyIM.app` (chuỗi ngày `20260721`).

## Giải pháp & Đã thực hiện
1. **Ghi đè ứng dụng hệ thống:** Đã chép bản build mới `20260816` vào `/Applications/XKey.app` và `~/Library/Input Methods/XKeyIM.app`.
2. **Cập nhật script build:** Cập nhật [`build_release.sh`](file:///Users/hacmieu/DevOps/xkey/build_release.sh) để tự động cập nhật đồng thời cả `/Applications/XKey.app` và `~/Library/Input Methods/XKeyIM.app`, sau đó đăng ký lại với LaunchServices (`lsregister`).
3. **Đăng ký lại LaunchServices:** Đã chạy `lsregister -f -R` cho cả hai đường dẫn để macOS ghi nhận bản build `20260816`.

## Kết quả
Cả 2 vị trí ứng dụng hiện tại đều đồng bộ phiên bản `1.2.24 (20260816)`. Khi xóa và thêm lại từ Apple Settings, bộ gõ sẽ luôn giữ đúng bản mới `20260816`.
