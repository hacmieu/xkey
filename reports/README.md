# Reports — XKey

**Cập nhật lần cuối:** 2026-08-20 09:05

---

## Nhật ký Báo cáo (Reports)

### 2026-09-06 — Báo cáo Sửa lỗi Macro Double Space & Loại bỏ tính năng Gõ nhanh
- Phân tích nguyên nhân lỗi Macro bị chèn 2 space: `VNEngine` đã nối sẵn space vào `replacementText`, nhưng `XKeyIMController` trả về `false` làm macOS pass-through thêm phím Space gốc vào app đích. Đã sửa thành `return true`.
- Loại bỏ hoàn toàn tính năng "Gõ nhanh" khỏi UI Sidebar và vô hiệu hóa các biến cờ Quick Telex / Quick Consonants trong Engine.
- Build và đóng gói bản phát hành `20260906`, cài đặt vào `/Applications/XKey.app` và `~/Library/Input Methods/XKeyIM.app`.
- Chi tiết: xem [`reports/20260906_2350-Fix_Macro_Double_Space_And_Remove_QuickTyping_Report.md`](file:///Users/hacmieu/DevOps/xkey/reports/20260906_2350-Fix_Macro_Double_Space_And_Remove_QuickTyping_Report.md)

### 2026-08-25 — Báo cáo sửa lỗi XKey Settings bị crash do Rollback
- Đã bổ sung lại logic: Nếu không có chứng chỉ Developer ID -> tự động fallback sang chứng chỉ Ad-hoc -> tự động xóa thông số `ENABLE_ICLOUD_ENTITLEMENT`.
- Tránh được lỗi Crash 162 trên hệ điều hành khi người dùng mở app Settings.
- Chi tiết: xem [`reports/20260825_1450-Refix_iCloud_Entitlement_Report.md`](file:///Users/hacmieu/DevOps/xkey/reports/20260825_1450-Refix_iCloud_Entitlement_Report.md)

### 2026-08-25 — Báo cáo Rollback toàn bộ thay đổi ngày 2026-08-25
- Toàn bộ mã nguồn đã được khôi phục về phiên bản trước ngày hôm nay (loại bỏ VCV rule và Instant Restore).
- Đã cài đặt lại ứng dụng phiên bản cũ. Các nhật ký cũ trong ngày vẫn được giữ lại làm dữ liệu.
- Chi tiết: xem [`reports/20260825_1444-Rollback_English_Detection_Report.md`](file:///Users/hacmieu/DevOps/xkey/reports/20260825_1444-Rollback_English_Detection_Report.md)

### 2026-08-25 — Báo cáo triển khai Luật VCV cho Instant Restore
- Phát hiện từ tiếng Anh ngay giữa chừng thông qua cấu trúc Nguyên âm - Phụ âm - Nguyên âm.
- Ngăn chặn triệt để tình trạng các từ như Adsense, facebook, youtube bị bám dấu tiếng Việt khi đang gõ.
- Chi tiết: xem [`reports/20260825_1438-VCV_Instant_Restore_Report.md`](file:///Users/hacmieu/DevOps/xkey/reports/20260825_1438-VCV_Instant_Restore_Report.md)

## Báo cáo phân tích & Sửa lỗi

| Ngày | Tên | Loại | Link |
|------|-----|------|------|
| 2026-09-06 | Fix Macro Double Space & Remove Quick Typing | 🛠️ Sửa lỗi & Tối ưu | [`20260906_2350-Fix_Macro_Double_Space_And_Remove_QuickTyping_Report.md`](file:///Users/hacmieu/DevOps/xkey/reports/20260906_2350-Fix_Macro_Double_Space_And_Remove_QuickTyping_Report.md) |
| 2026-08-25 | English Detection Auto-Restore Impl | 🛠️ Sửa lỗi bộ gõ | [`20260825_1411-English_Detection_AutoRestore_Report.md`](file:///Users/hacmieu/DevOps/xkey/reports/20260825_1411-English_Detection_AutoRestore_Report.md) |
| 2026-08-25 | English Word Detection Algorithm | 🔍 Nghiên cứu thuật toán | [`20260825_1404-English_Word_Detection_Algorithm_Research.md`](file:///Users/hacmieu/DevOps/xkey/reports/20260825_1404-English_Word_Detection_Algorithm_Research.md) |
| 2026-08-24 | English Marked Text & Diacritics Issue | 🔍 Phân tích UX | [`20260824_1431-English_Marked_Text_Issue_Analysis.md`](file:///Users/hacmieu/DevOps/xkey/reports/20260824_1431-English_Marked_Text_Issue_Analysis.md) |
| 2026-08-20 | Triển khai Cherry-pick Upstream 7a59d9a & Build v1.2.25 | 🚀 Phát hành & Triển khai | [`20260820_0905-Cherry_Pick_7a59d9a_And_Build_v1.2.25.md`](file:///Users/hacmieu/DevOps/xkey/reports/20260820_0905-Cherry_Pick_7a59d9a_And_Build_v1.2.25.md) |
| 2026-08-20 | Phân tích Upstream v1.2.25 & Đánh giá Tương thích | 🔍 Phân tích & Đánh giá | [`20260820_0720-Analysis_Upstream_v1.2.25_20260818_Sync_Evaluation.md`](file:///Users/hacmieu/DevOps/xkey/reports/20260820_0720-Analysis_Upstream_v1.2.25_20260818_Sync_Evaluation.md) |
| 2026-08-14 | Rebuild 20260813 Stable Build | 🔧 Khôi phục | [`20260814_1640-Rebuild_20260813_Stable_Build.md`](20260814_1640-Rebuild_20260813_Stable_Build.md) |
| 2026-08-14 | Punctuation Context Bug Report | 🔍 Phân tích | [`20260814_1450-Punctuation_Context_Bug_Report.md`](20260814_1450-Punctuation_Context_Bug_Report.md) |
| 2026-08-14 | XKeyIM Debug Review Report | 📊 Tổng hợp | [`20260814_1418-XKeyIM_Debug_Review_Report.md`](20260814_1418-XKeyIM_Debug_Review_Report.md) |
| 2026-08-14 | English Token Retry Analysis | 🔍 Phân tích | [`20260814_1110-English_Token_Retry_Analysis.md`](20260814_1110-English_Token_Retry_Analysis.md) |
| 2026-08-14 | Log-Based Optimization — XKeyIM | 💡 Khả thi | [`20260814_1052-Log_Based_Optimization.md`](file:///Users/hacmieu/DevOps/xkey/reports/20260814_1052-Log_Based_Optimization.md) |
| 2026-08-13 | Cursor Tracking False-Positive Fix (VS Code) | 🐛 Bug fix | [`20260813_2256-Cursor_Tracking_VSCode_Fix.md`](file:///Users/hacmieu/DevOps/xkey/reports/20260813_2256-Cursor_Tracking_VSCode_Fix.md) |
| 2026-08-13 | Macro System Analysis — XKeyIM | 🔍 Phân tích thiết kế | [`20260813_1648-Macro_System_Analysis_XKeyIM.md`](file:///Users/hacmieu/DevOps/xkey/reports/20260813_1648-Macro_System_Analysis_XKeyIM.md) |
| 2026-08-13 | Macro Fix Completed | ✅ Hoàn thành tính năng | [`20260813_2200-Macro_Fix_Completed.md`](file:///Users/hacmieu/DevOps/xkey/reports/20260813_2200-Macro_Fix_Completed.md) |
| 2026-08-13 | Fix XKeyIM Version Config Linking | 🔧 Sửa cấu hình Build | [`20260813_2226-Fix_XKeyIM_Version_Config_Linking.md`](file:///Users/hacmieu/DevOps/xkey/reports/20260813_2226-Fix_XKeyIM_Version_Config_Linking.md) |
| 2026-08-16 | Fix Macro, Space Desync & Diacritics | 🛠️ Sửa lỗi bộ gõ | [`20260816_1410-Fix_Macro_Space_Desync_And_Diacritics.md`](file:///Users/hacmieu/DevOps/xkey/reports/20260816_1410-Fix_Macro_Space_Desync_And_Diacritics.md) |
| 2026-08-16 | Fix Apple Settings Version Fallback | 🔧 Cập nhật đăng ký App | [`20260816_1440-Fix_Apple_Settings_Version_Fallback.md`](file:///Users/hacmieu/DevOps/xkey/reports/20260816_1440-Fix_Apple_Settings_Version_Fallback.md) |
| 2026-08-16 | Fix XKey Settings Can't Be Opened | 🔐 Sửa chữ ký số `--deep` | [`20260816_1558-Fix_XKey_Settings_Cant_Be_Opened.md`](file:///Users/hacmieu/DevOps/xkey/reports/20260816_1558-Fix_XKey_Settings_Cant_Be_Opened.md) |
