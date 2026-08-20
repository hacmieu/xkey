# Kế hoạch Cherry-pick 7a59d9a & Build Bản mới v1.2.25 (Build 20260820)

**Ngày:** 2026-08-20 09:05  
**Tác giả:** AI Assistant  

---

## 1. Mục tiêu
- Sao lưu bản build hiện tại (`20260816`) để có thể rollback bất kỳ lúc nào.
- Cherry-pick bản vá AX query / Chrome Gmail freeze (commit `7a59d9a`) từ upstream repo.
- Đảm bảo các tính năng độc quyền của fork (Macro IMKit, con trỏ VS Code, lost-focus reset) tiếp tục hoạt động hoàn hảo.
- Build và deploy bản `1.2.25 (20260820)` vào hệ thống macOS.

---

## 2. Các giai đoạn thực hiện

- [x] **Giai đoạn 1: Backup**
  - Sao lưu `XKey.app` và `XKeyIM.app` vào `~/DevOps/xkey_backups/backup_20260816_stable/`.
- [x] **Giai đoạn 2: Tích hợp mã nguồn**
  - Commit các thay đổi chưa commit của fork.
  - Cherry-pick `7a59d9a` và giải quyết conflict tại `Version.xcconfig`.
  - Cập nhật phiên bản lên `1.2.25 (20260820)`.
- [x] **Giai đoạn 3: Kiểm thử**
  - Chạy `FocusedElementInfoLazyTests` (17 tests - 100% Pass).
  - Chạy `VNEngineTests` (Core engine - 100% Pass).
- [x] **Giai đoạn 4: Đóng gói & Cài đặt**
  - Build release qua `build_release.sh`.
  - Triển khai vào `~/Library/Input Methods/XKeyIM.app` và `/Applications/XKey.app`.
  - Đăng ký `lsregister` và xác minh bundle version.

---

## 3. Trạng thái
- **Trạng thái:** ✅ DONE — Hoàn thành toàn diện.
