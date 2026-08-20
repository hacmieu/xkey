# Tích hợp Commit 7a59d9a (Upstream v1.2.25) & Phát hành Build 20260820

**Ngày:** 2026-08-20 09:05  
**Tác giả:** AI Assistant  

---

## 1. Mục đích
- Tối ưu hóa chế độ CGEvent trên Google Chrome bằng cách tích hợp bản vá giảm thiểu truy vấn AX vào Web Renderer từ upstream commit `7a59d9a1d2c4626de5f82c10310283d2ad902354`.
- Đảm bảo giữ nguyên 100% tính năng và bản vá của XKeyIM (Macro, VSCode cursor fix, lost focus space reset, `--deep` codesign).
- Sao lưu bản build cũ `20260816` trước khi thực hiện.
- Build và deploy phiên bản mới `1.2.25 (20260820)` vào hệ thống.

---

## 2. Các bước thực hiện
1. **Backup:** Đã sao lưu toàn bộ `/Applications/XKey.app` và `~/Library/Input Methods/XKeyIM.app` (bản build `20260816`) sang thư mục `~/DevOps/xkey_backups/backup_20260816_stable/`.
2. **Commit & Cherry-pick:**
   - Commit trạng thái stable build `20260816` lên branch `rebuild-20260813-stable`.
   - Cherry-pick upstream commit `7a59d9a` (`Shared/AppBehaviorDetector.swift`, `FocusedElementInfoLazyTests.swift`, `.gitignore`, `project.pbxproj`).
   - Xử lý xung đột `Version.xcconfig`: Cập nhật `MARKETING_VERSION = 1.2.25`, `CURRENT_PROJECT_VERSION = 20260820`.
3. **Kiểm thử tự động:**
   - Chạy test suite `FocusedElementInfoLazyTests`: 17/17 test cases PASSED.
   - Chạy test suite `VNEngineTests`: Toàn bộ bộ gõ tiếng Việt PASSED.
4. **Build & Cài đặt:**
   - Chạy `./build_release.sh` thành công.
   - Ứng dụng đã được cài đặt vào `~/Library/Input Methods/XKeyIM.app` và `/Applications/XKey.app`.
   - Đã gọi `lsregister` đăng ký lại bundle ID với hệ thống.
