# Kế hoạch Đánh giá & Đồng bộ Upstream v1.2.25 (Build 20260818)

**Ngày:** 2026-08-20 07:20  
**Tác giả:** AI Assistant  

---

## 1. Mục tiêu
- Đánh giá bản phát hành mới của repo gốc (`v1.2.25-20260818`, commit `7a59d9a`).
- Kiểm tra tính tương thích, nguy cơ xung đột với các bản vá độc quyền của fork (Macro IMKit, cursor fix, lost focus reset, codesign `--deep`).
- Đề xuất các phương án tích hợp và hành động tiếp theo.

---

## 2. Kế hoạch Thực hiện

### Giai đoạn 1: Phân tích Kỹ thuật (Đã hoàn thành)
- [x] Fetch thông tin remote `origin` (commit `7a59d9a`).
- [x] Kiểm tra diff giữa repo hiện tại và upstream.
- [x] Đánh giá mức độ phụ thuộc của `Shared/AppBehaviorDetector.swift` đối với `XKeyIMController.swift` và `VNEngine.swift`.

### Giai đoạn 2: Lựa chọn Phương án
- **Phương án A — Giữ nguyên hiện trạng (Khuyên dùng nếu bản hiện tại đang chạy tốt):**
  - Giữ vững branch `rebuild-20260813-stable` (build `20260816`).
  - Không cần sửa mã nguồn ngay lập tức vì hệ thống IMKit hiện tại đang hoạt động trơn tru.
- **Phương án B — Cherry-pick upstream commit `7a59d9a` vào fork:**
  - Áp dụng các thay đổi trong `Shared/AppBehaviorDetector.swift`, `FocusedElementInfoLazyTests.swift`, `.gitignore`.
  - Giữ nguyên các chỉnh sửa trong `XKeyIMController.swift`, `VNEngine.swift`, và `build_release.sh`.
  - Cập nhật phiên bản lên `1.2.25` và build lại bộ cài.

---

## 3. Trạng thái
- **Trạng thái:** ✅ Đã hoàn thành phân tích & lập tài liệu.
