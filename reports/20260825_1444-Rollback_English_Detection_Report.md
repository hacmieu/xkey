# Báo cáo: Rollback mã nguồn về bản trước ngày hôm nay

**Ngày:** 2026-08-25 14:44

## Hoạt động thực hiện
- Sử dụng `git restore` để khôi phục mã nguồn của toàn bộ các file đã bị thay đổi trong ngày hôm nay:
  - `Version.xcconfig`
  - `XKey/Core/Engine/VNEngine.swift`
  - `XKey/Core/Engine/VNEngineEnglishDetection.swift`
  - `XKeyIM/XKeyIMController.swift`
  - `build_release.sh`
- Biên dịch lại ứng dụng thông qua script `./build_release.sh` và cài đặt lại vào macOS.

## Kết quả
- Ứng dụng đã trở về trạng thái ổn định của commit trước đó.
- Các nỗ lực thêm tính năng tự nhận diện tiếng Anh (Instant Restore / VCV rule) đã được gỡ bỏ khỏi luồng thực thi.
- Người dùng sử dụng phím `ESC` để ngắt bỏ dấu tiếng Việt khi cần. Các tệp nhật ký của ngày hôm nay được giữ lại trong hệ thống file để làm tài liệu tham khảo cho tương lai.
