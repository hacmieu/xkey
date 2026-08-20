# Báo cáo Lỗi "The application 'XKey' can't be opened" khi chọn "Mở XKey Settings..."

**Ngày:** 2026-08-16 15:58  
**Người thực hiện:** AI Agent  

## 1. Phân tích nguyên nhân (Root Cause)
Khi click vào mục **"Mở XKey Settings..."** trên menu bar:
- `XKeyIMController.swift` gọi `NSWorkspace.shared.open(URL(string: "xkey://settings")!)`.
- Hệ thống macOS kích hoạt dịch vụ LaunchServices / RunningBoard để khởi chạy `/Applications/XKey.app`.
- Hệ thống macOS trả về lỗi `RBSRequestErrorDomain Code=5 "Launch failed." (POSIX Error 162)`.
- Nhật ký hệ thống (`DiagnosticReports/XKey-*.ips`) cho thấy tiến trình bị kernel tiêu diệt lập tức với thông báo `SIGKILL (Code Signature Invalid)` - `Taskgated Invalid Signature`.
- Lý do chữ ký số bị lỗi: Trong quá trình ad-hoc signing (`codesign --sign -`) ở script `build_release.sh`, các framework nhúng bên trong (`Sparkle.framework`) bị hủy niêm phong chữ ký (a sealed resource is missing or invalid) vì thiếu cờ ký sâu `--deep`. Khi macOS khởi chạy ứng dụng chính, `taskgated` kiểm tra thấy chữ ký số của framework con không hợp lệ nên chặn và kill ứng dụng ngay lập tức.

## 2. Giải pháp thực hiện
1. **Thêm cờ `--deep` khi Ad-hoc signing:** Đã cập nhật script [`build_release.sh`](file:///Users/hacmieu/DevOps/xkey/build_release.sh) thêm thuộc tính `--deep` cho lệnh `codesign` đối với `Release/XKey.app` và `Release/XKeyIM.app`.
2. **Ký lại sâu và đồng bộ:** Đã thực hiện `codesign --force --deep --sign -` lại toàn bộ ứng dụng `/Applications/XKey.app`.

## 3. Kết quả
- Kiểm tra chữ ký số: `codesign -vvv --deep --strict /Applications/XKey.app` trả về **`valid on disk`**.
- Đã kiểm tra mở ứng dụng: `open /Applications/XKey.app` và `open xkey://settings` chạy mượt mà, cửa sổ **XKey Settings** mở thành công không còn thông báo lỗi.
