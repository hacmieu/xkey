# Báo cáo Triển khai Cherry-pick Upstream 7a59d9a & Build v1.2.25 (Build 20260820)

**Ngày:** 2026-08-20 09:05  
**Người thực hiện:** AI Assistant  
**Phiên bản mới:** `1.2.25 (20260820)`  
**Phiên bản cũ đã backup:** `1.2.24 (20260816)`  

---

## 1. Tóm tắt kết quả triển khai

1. **Sao lưu an toàn bản cũ:**
   - Đã sao lưu `/Applications/XKey.app` và `~/Library/Input Methods/XKeyIM.app` vào `~/DevOps/xkey_backups/backup_20260816_stable/`.
2. **Tích hợp bản vá Upstream:**
   - Đã cherry-pick thành công commit `7a59d9a` (`fix(injection): reduce AX queries into web renderer to stop Gmail/Chrome freeze`).
   - Các file được áp dụng:
     - [`Shared/AppBehaviorDetector.swift`](file:///Users/hacmieu/DevOps/xkey/Shared/AppBehaviorDetector.swift): Cơ chế Lazy Provider cho `domIdentifier`/`domClasses`, cache Priority-2 fallback, role gate cho address bar check.
     - [`XKeyTests/FocusedElementInfoLazyTests.swift`](file:///Users/hacmieu/DevOps/xkey/XKeyTests/FocusedElementInfoLazyTests.swift): Bộ 17 unit test cases kiểm thử hành vi lazy load.
     - [`.gitignore`](file:///Users/hacmieu/DevOps/xkey/.gitignore): Bổ sung ignore tooling.
     - [`XKey.xcodeproj/project.pbxproj`](file:///Users/hacmieu/DevOps/xkey/XKey.xcodeproj/project.pbxproj): Bổ sung target test file.
     - [`Version.xcconfig`](file:///Users/hacmieu/DevOps/xkey/Version.xcconfig): Đặt `MARKETING_VERSION = 1.2.25` và `CURRENT_PROJECT_VERSION = 20260820`.
3. **Kết quả kiểm thử tự động (Unit Tests):**
   - `FocusedElementInfoLazyTests`: **17/17 PASSED** (0.001s mỗi test).
   - `VNEngineTests`: **100% PASSED**.
4. **Đóng gói & Triển khai ứng dụng:**
   - Đã biên dịch toàn bộ target `XKey` và `XKeyIM` (Universal Binary: x86_64 + arm64).
   - Đã ad-hoc sign với entitlements và `--deep` codesign cho `Sparkle.framework`.
   - Đã sao chép vào `~/Library/Input Methods/XKeyIM.app` và `/Applications/XKey.app`.
   - Đã tạo bộ cài `Release/XKey.dmg` (11MB).
   - Đã đăng ký `lsregister` với hệ thống macOS.
   - Xác minh `CFBundleShortVersionString = 1.2.25`, `CFBundleVersion = 20260820`.

---

## 2. Trạng thái kiểm tra sau triển khai
- **XKeyIM (IMKit mode):** Hoạt động bình thường, bảo toàn toàn bộ tính năng Macro, chống tràn space khi lost focus và cơ chế bẫy con trỏ.
- **XKey (CGEvent mode):** Được tăng tốc đáng kể trên Chrome/Gmail, không còn tình trạng lag hay freeze do AX DOM queries.
