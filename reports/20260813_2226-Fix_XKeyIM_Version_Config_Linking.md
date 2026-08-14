# Sửa lỗi hiển thị Version của XKeyIM (Version.xcconfig linking)

**Ngày:** 2026-08-13 22:26  
**Người thực hiện:** AI Agent  

## Bối cảnh & Vấn đề
Mặc dù đã đổi `CURRENT_PROJECT_VERSION = 20260813` trong `Version.xcconfig`, menu hiển thị của `XKeyIM` vẫn giữ nguyên chuỗi phiên bản cũ `1.2.24 (20260721)`.

## Nguyên nhân
Trong file cấu hình Xcode (`XKey.xcodeproj/project.pbxproj`), target `XKey` chính có thuộc tính `baseConfigurationReference = VERSION0001 /* Version.xcconfig */`, nhưng hai configuration Debug & Release của target `XKeyIM` lại thiếu thuộc tính này. Do đó, `XKeyIM` không nhận biến `CURRENT_PROJECT_VERSION` từ `Version.xcconfig` mà dùng giá trị fallback cũ.

## Giải pháp thực hiện
1. Đã cập nhật `XKey.xcodeproj/project.pbxproj` để gán `baseConfigurationReference = VERSION0001 /* Version.xcconfig */;` cho cả 2 cấu hình Debug (`100D2BB5`) và Release (`100D2BB6`) của target `XKeyIM`.
2. Chạy lại `build_release.sh` để biên dịch lại `XKeyIM`.

## Kết quả
Kiểm tra `CFBundleVersion` trong `~/Library/Input Methods/XKeyIM.app/Contents/Info.plist` cho kết quả chính xác: `20260813`. Menu hệ thống hiển thị đúng phiên bản `1.2.24 (20260813)`.
