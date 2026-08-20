# Đánh giá & Phân tích Bản phát hành Upstream v1.2.25 (Build 20260818)

**Ngày:** 2026-08-20 07:20  
**Tác giả:** AI Assistant  

---

## 1. Tóm tắt bản phát hành Upstream v1.2.25 (Build 20260818)
- **Commit:** `7a59d9a1d2c4626de5f82c10310283d2ad902354`
- **Mục tiêu chính:** Sửa lỗi đơ/lag gõ phím trên Gmail/Chrome do truy vấn AX vào Web Renderer.
- **Các thay đổi cốt lõi:**
  1. **Lazy Loading cho DOM Attributes (`AXDOMIdentifier`, `AXDOMClassList`):** Chuyển từ eager query sang provider callback trong `FocusedElementInfo`. Giảm số lượng cuộc gọi AX eager từ ~10 xuống còn 5.
  2. **Cache Priority-2 Fallback trong `getConfirmedInjectionMethod()`:** Lưu kết quả live-detection vào `confirmedInjectionMethod` để tránh việc mỗi phím nhấn đều kích hoạt full AX snapshot trên luồng event tap sau khi `clearMethodCache()`.
  3. **Tối ưu hóa `signature`:** Chỉ sử dụng DOM ID nếu đã được query trước đó (`domIdentifierIfLoaded`), không kích hoạt lazy query trên hot path.
  4. **Gate kiểm tra Address Bar theo Role (`info.role == "AXTextField"`):** Tránh việc nội dung web (AXTextArea, AXWebArea) kích hoạt query DOM renderer khi kiểm tra Omnibox/URL bar.
  5. **Unit Tests:** Bổ sung 17 test cases trong `FocusedElementInfoLazyTests.swift`.

---

## 2. Đối chiếu & Đánh giá rủi ro với Repo hiện tại (Fork)
- **Tình trạng Repo hiện tại:**
  - Branch: `rebuild-20260813-stable` (bản build `20260816`).
  - Các tính năng độc quyền của Fork:
    - Hỗ trợ Macro trong XKeyIM (IMKit Mode).
    - Fix space thừa khi Lost Focus (`spaceCount > 0`, `deactivateServer` reset).
    - Fix nhầm lẫn con trỏ trong VS Code (`skipNextCursorCheck`).
    - Fix ký số `--deep` cho `Sparkle.framework` và đồng bộ app vào `/Applications/XKey.app` + `lsregister`.
- **Phạm vi tác động của Upstream:**
  - Toàn bộ thay đổi nằm trong `Shared/AppBehaviorDetector.swift` và bộ test `FocusedElementInfoLazyTests.swift`.
  - Repo hiện tại **hoàn toàn không sửa đổi** `AppBehaviorDetector.swift`.
  - Hai luồng phát triển **hoàn toàn độc lập và không xung đột** (zero conflict).
- **Kết luận:**
  - Bản hiện tại của chúng ta đang hoạt động ổn định và đáp ứng tốt nhu cầu gõ IMKit.
  - Thay đổi của upstream là một cải tiến hiệu năng rất tốt cho chế độ CGEvent/Accessibility (chống giật lag trên Gmail/Chrome).
  - Có thể đưa (port/cherry-pick) thay đổi này vào repo bất cứ lúc nào mà không làm ảnh hưởng đến các fix IMKit đã có.
