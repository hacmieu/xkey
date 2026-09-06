# Báo cáo Điều tra & Khắc phục: Sparkle Auto-Update tự động ghi đè bản Upstream làm xuất hiện lại "Gõ nhanh"

**Ngày:** 2026-09-07 01:38  
**Người thực hiện:** AI Agent  

---

## 1. Hiện tượng
- Người dùng phản ánh: "Kiểm tra lại cho tôi vì sao vẫn thấy Gõ nhanh nhỉ? Dù tôi ấn macro không bị 2 cách nữa rồi."
- Kèm ảnh chụp màn hình hiển thị:
  - Menu trạng thái bộ gõ XKey báo: `Phiên bản 1.2.25 (20260904)`.
  - Cửa sổ Cài đặt XKey mở ra vẫn còn nguyên tab "Gõ nhanh".

---

## 2. Phân tích nguyên nhân gốc (Root Cause Analysis)

1. **Kiểm tra phiên bản các file ứng dụng trên máy:**
   - Khi chạy lệnh kiểm tra các bản cài đặt:
     ```bash
     /Applications/XKey.app: 20260904
     ~/Library/Input Methods/XKeyIM.app: 20260904
     Release/XKey.app: 20260906 (bản vừa build trước đó)
     ```
   - Thời điểm file thực thi `/Applications/XKey.app/Contents/MacOS/XKey` ghi nhận ngày **05/09/2026** thay vì thời điểm vừa build ngày **07/09/2026**.

2. **Truy vết nguyên nhân: Sparkle Framework Auto-Updater:**
   - Trước khi tiến hành lượt build trước đó, trong bộ nhớ hệ thống đã có sẵn tiến trình nền:
     ```
     Updater.app/Contents/MacOS/Updater /Applications/XKey.app 0
     ```
   - File cấu hình `XKey/Info.plist` của dự án trỏ vào Feed cập nhật của tác giả gốc (upstream):
     ```xml
     <key>SUFeedURL</key>
     <string>https://xmannv.github.io/xkey/appcast.xml</string>
     <key>SUEnableAutomaticChecks</key>
     <true/>
     ```
   - Upstream `xmannv/xkey` vừa phát hành bản build mới `v1.2.25 (Build 20260904)` vào ngày 04/09/2026.
   - Khi ứng dụng XKey cũ chạy, Sparkle đã âm thầm tải bản `20260904` về thư mục đệm:
     `/Users/hacmieu/Library/Caches/com.codetay.XKey/org.sparkle-project.Sparkle/`
   - Đúng lúc script build kết thúc và gọi `killall XKey`, tiến trình `Updater.app` của Sparkle phát hiện ứng dụng chính vừa tắt, liền tự động giải nén và **ghi đè bản `20260904` của upstream vào `/Applications/XKey.app`**.
   - Do đó, ứng dụng trong `/Applications/XKey.app` bị quay trở lại bản upstream nguyên bản, dẫn đến việc tab "Gõ nhanh" vẫn còn hiển thị.

---

## 3. Các biện pháp khắc phục triệt để

1. **Vô hiệu hóa tính năng tự động cập nhật của Sparkle trong mã nguồn:**
   - Sửa [XKey/Info.plist](file:///Users/hacmieu/DevOps/xkey/XKey/Info.plist):
     - `SUEnableAutomaticChecks` -> `false`.
     - Bổ sung `SUAutomaticallyUpdate` -> `false`.
   - Ngăn chặn hoàn toàn việc Sparkle tự ý kiểm tra feed của upstream và tráo đổi app trên máy người dùng.

2. **Dọn dẹp tiến trình và bộ nhớ đệm Sparkle:**
   - Buộc dừng toàn bộ tiến trình liên quan: `killall -9 XKey XKeyIM Autoupdate Updater`.
   - Xóa bỏ toàn bộ thư mục `/Users/hacmieu/Library/Caches/com.codetay.XKey/org.sparkle-project.Sparkle/`.
   - Thiết lập cấu hình hệ thống bằng `defaults write com.codetay.XKey SUEnableAutomaticChecks -bool false`.

3. **Nâng phiên bản & Biên dịch lại:**
   - Tăng `CURRENT_PROJECT_VERSION` trong [Version.xcconfig](file:///Users/hacmieu/DevOps/xkey/Version.xcconfig) lên **`20260907`** (cao hơn mọi bản upstream trước đó).
   - Chạy `ENABLE_SPARKLE_SIGN=false ./build_release.sh` biên dịch Universal Release thành công.
   - Tự động sao chép đè vào:
     - `/Applications/XKey.app` (build `20260907`)
     - `~/Library/Input Methods/XKeyIM.app` (build `20260907`)
   - Đăng ký lại dịch vụ LaunchServices (`lsregister`).

4. **Xác nhận kết quả:**
   - Đã kiểm tra symbols nhị phân của `/Applications/XKey.app/Contents/MacOS/XKey`: `QuickTypingSection` hoàn toàn không tồn tại (`NOT IN SYMBOLS`).
   - Cả `/Applications/XKey.app` và `XKeyIM.app` đều mang mã build chuẩn xác `20260907`.
