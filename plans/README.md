# Plans — XKey

**Cập nhật lần cuối:** 2026-08-20 09:05

---

## Nhật ký Kế hoạch (Plans)

### 2026-09-07 — Khắc phục Sparkle Auto-Update tự động ghi đè bản Upstream
- Điều tra hiện tượng sau khi loại bỏ "Gõ nhanh", app bị tráo đổi về bản upstream `20260904` do Sparkle framework âm thầm ghi đè.
- Kế hoạch: Tắt `SUEnableAutomaticChecks` và `SUAutomaticallyUpdate` trong `Info.plist`, xóa cache Sparkle, nâng build version lên `20260907` và build lại.
- Chi tiết: xem [`plans/20260907_0138-Fix_Sparkle_AutoUpdate_Overwrite_Plan.md`](file:///Users/hacmieu/DevOps/xkey/plans/20260907_0138-Fix_Sparkle_AutoUpdate_Overwrite_Plan.md)

### 2026-09-06 — Sửa lỗi Macro Double Space & Loại bỏ tính năng Gõ nhanh
- Khắc phục lỗi Macro bị chèn 2 dấu space do `XKeyIMController` không nuốt sự kiện Space khi `addSpaceAfterMacro` bật.
- Loại bỏ hoàn toàn tính năng "Gõ nhanh" (Quick Telex, Quick Consonants) khỏi giao diện Settings và logic Engine cho bản build máy này.
- Chi tiết: xem [`plans/20260906_2350-Fix_Macro_Double_Space_And_Remove_QuickTyping_Plan.md`](file:///Users/hacmieu/DevOps/xkey/plans/20260906_2350-Fix_Macro_Double_Space_And_Remove_QuickTyping_Plan.md)

### 2026-08-25 — Áp dụng lại fix lỗi Crash iCloud Entitlement
- Lệnh Rollback mã nguồn trước đó đã xóa nhầm đoạn code sửa lỗi chứng chỉ khi build Ad-hoc.
- Kế hoạch: Cài đặt lại đoạn mã chặn iCloud Entitlement trong `build_release.sh` nếu đang build không có tài khoản (Ad-hoc signing).
- Chi tiết: xem [`plans/20260825_1450-Refix_iCloud_Entitlement_Plan.md`](file:///Users/hacmieu/DevOps/xkey/plans/20260825_1450-Refix_iCloud_Entitlement_Plan.md)

### 2026-08-25 — Kế hoạch Rollback thay đổi nhận diện tiếng Anh
- Hủy bỏ các tính năng tự động nhận diện tiếng Anh do người dùng đã có giải pháp phím ESC.
- Khôi phục mã nguồn về nguyên trạng cũ nhất trước ngày hôm nay và build lại để cài đặt đè.
- Chi tiết: xem [`plans/20260825_1444-Rollback_English_Detection_Plan.md`](file:///Users/hacmieu/DevOps/xkey/plans/20260825_1444-Rollback_English_Detection_Plan.md)

### 2026-08-25 — Kế hoạch bổ sung luật VCV để nhận diện tiếng Anh tức thì
- Vấn đề: Từ "Adsense" vẫn bị kẹt dấu do các luật cũ chỉ kiểm tra đầu và cuối từ.
- Giải pháp: Thêm luật VCV (Nguyên âm - Phụ âm cứng - Nguyên âm) để phát hiện từ tiếng Anh ngay từ giữa từ.
- Chi tiết: xem [`plans/20260825_1438-VCV_Instant_Restore_Plan.md`](file:///Users/hacmieu/DevOps/xkey/plans/20260825_1438-VCV_Instant_Restore_Plan.md)

## Kế hoạch đang mở (Completed)

| Ngày | Tên | Trạng thái | Link |
|------|-----|-----------|------|
| 2026-09-07 | Fix Sparkle Auto-Update Overwrite Plan | ✅ DONE | [`20260907_0138-Fix_Sparkle_AutoUpdate_Overwrite_Plan.md`](file:///Users/hacmieu/DevOps/xkey/plans/20260907_0138-Fix_Sparkle_AutoUpdate_Overwrite_Plan.md) |
| 2026-09-06 | Fix Macro Double Space & Remove Quick Typing Plan | ✅ DONE | [`20260906_2350-Fix_Macro_Double_Space_And_Remove_QuickTyping_Plan.md`](file:///Users/hacmieu/DevOps/xkey/plans/20260906_2350-Fix_Macro_Double_Space_And_Remove_QuickTyping_Plan.md) |
| 2026-08-25 | English Detection Auto-Restore Plan | ✅ DONE | [`20260825_1411-English_Detection_AutoRestore_Plan.md`](file:///Users/hacmieu/DevOps/xkey/plans/20260825_1411-English_Detection_AutoRestore_Plan.md) |
| 2026-08-20 | Tích hợp Commit 7a59d9a & Build v1.2.25 | ✅ DONE | [`20260820_0905-Cherry_Pick_7a59d9a_And_Build_v1.2.25.md`](file:///Users/hacmieu/DevOps/xkey/plans/20260820_0905-Cherry_Pick_7a59d9a_And_Build_v1.2.25.md) |
| 2026-08-20 | Đánh giá & Đồng bộ Upstream v1.2.25 | ✅ DONE | [`20260820_0720-Analysis_Upstream_v1.2.25_20260818_Sync_Evaluation.md`](file:///Users/hacmieu/DevOps/xkey/plans/20260820_0720-Analysis_Upstream_v1.2.25_20260818_Sync_Evaluation.md) |
| 2026-08-14 | Rebuild 20260813 Stable Build | ✅ DONE | [`20260814_1640-Rebuild_20260813_Stable_Build.md`](20260814_1640-Rebuild_20260813_Stable_Build.md) |
| 2026-08-14 | Punctuation Context Bug Fix | 📋 Plan | [`20260814_1450-Punctuation_Context_Bug_Fix.md`](20260814_1450-Punctuation_Context_Bug_Fix.md) |
| 2026-08-14 | XKeyIM Optimization Plan | 📋 Plan | [`20260814_1418-XKeyIM_Optimization_Plan.md`](20260814_1418-XKeyIM_Optimization_Plan.md) |
| 2026-08-14 | English Token Retry Optimization | 💡 Đề xuất | [`20260814_1110-English_Token_Retry_Optimization.md`](20260814_1110-English_Token_Retry_Optimization.md) |
| 2026-08-14 | Log-Based Optimization — XKeyIM | 💡 Đề xuất | [`20260814_1052-Log_Based_Optimization.md`](file:///Users/hacmieu/DevOps/xkey/plans/20260814_1052-Log_Based_Optimization.md) |
| 2026-08-13 | Fix Cursor Tracking False-Positive (VS Code) | ✅ DONE | [`20260813_2256-Cursor_Tracking_VSCode_Fix.md`](file:///Users/hacmieu/DevOps/xkey/plans/20260813_2256-Cursor_Tracking_VSCode_Fix.md) |
| 2026-08-13 | Fix Macro cho XKeyIM | ✅ DONE | [`20260813_1648-Fix_Macro_XKeyIM.md`](file:///Users/hacmieu/DevOps/xkey/plans/20260813_1648-Fix_Macro_XKeyIM.md) |
| 2026-08-16 | Fix Macro, Space Desync & Diacritics | ✅ DONE | [`20260816_1410-Fix_Macro_Space_Desync_And_Diacritics.md`](file:///Users/hacmieu/DevOps/xkey/plans/20260816_1410-Fix_Macro_Space_Desync_And_Diacritics.md) |
| 2026-08-16 | Fix Apple Settings Version Fallback | ✅ DONE | [`20260816_1440-Fix_Apple_Settings_Version_Fallback.md`](file:///Users/hacmieu/DevOps/xkey/plans/20260816_1440-Fix_Apple_Settings_Version_Fallback.md) |
| 2026-08-16 | Fix XKey Settings Can't Be Opened | ✅ DONE | [`20260816_1558-Fix_XKey_Settings_Cant_Be_Opened.md`](file:///Users/hacmieu/DevOps/xkey/plans/20260816_1558-Fix_XKey_Settings_Cant_Be_Opened.md) |
