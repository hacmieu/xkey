# Kế hoạch: Rollback thay đổi tính năng nhận diện Tiếng Anh (Instant Restore)

**Ngày:** 2026-08-25 14:44

## Vấn đề
- Tính năng Instant Restore (bao gồm bắt lỗi bằng VCV, tự động gỡ gạch chân tiếng Anh) vẫn chưa giải quyết triệt để nhu cầu của người dùng, hoặc gây xung đột ngoài ý muốn.
- Người dùng nhận ra chỉ cần bấm phím `ESC` khi gõ tiếng Anh là đủ để thoát chế độ bỏ dấu (một tính năng có sẵn của bộ gõ) và đáp ứng được nhu cầu của họ.

## Quyết định Rollback
- Tiến hành rollback (hủy bỏ) toàn bộ các thay đổi mã nguồn đã thực hiện trong ngày hôm nay liên quan tới:
  1. `VNEngine.swift`: Bỏ cờ `isInstantRestore`, logic `extCode = 5` và `checkRestoreIfWrongSpelling` ép buộc.
  2. `VNEngineEnglishDetection.swift`: Bỏ luật VCV.
  3. `XKeyIMController.swift`: Bỏ xử lý `commitComposition()` trên `isInstantRestore`.
  4. `build_release.sh`: Bỏ ép `ENABLE_ICLOUD_ENTITLEMENT=false` cho Ad-hoc (hoặc khôi phục về trạng thái gốc).
  5. `Version.xcconfig`: Trở về version ban đầu.
- Giữ lại các tệp nhật ký để làm tư liệu lịch sử.

## Triển khai
- Chạy `git restore` trên các tệp mã nguồn.
- Chạy `./build_release.sh` để biên dịch lại phiên bản nguyên gốc và cài đặt đè lên phiên bản lỗi.
