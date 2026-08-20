# Báo cáo Phân tích Bản phát hành Upstream v1.2.25 (Build 20260818) & Đánh giá Tương thích

**Ngày:** 2026-08-20 07:20  
**Người thực hiện:** AI Assistant  
**Đối tượng:** Bản cập nhật `v1.2.25-20260818` (Commit `7a59d9a1d2c4626de5f82c10310283d2ad902354`) từ `https://github.com/xmannv/xkey.git`

---

## 1. Chi tiết bản cập nhật Upstream v1.2.25

Upstream giải quyết triệt để vấn đề gõ chữ bị đơ/lag ngắt quãng trên các trình soạn thảo web nặng (đặc biệt là Gmail Compose trên Google Chrome):

1. **Lazy Loading AX DOM Attributes:**
   - Trước đây, mỗi lần gọi `FocusedElementInfo.from()` đều truy vấn trực tiếp `AXDOMClassList` và `AXDOMIdentifier`. Các thuộc tính này do Web Renderer của trình duyệt phục vụ, buộc Chromium phải liên tục duy trì cây Accessibility cho trang web gây drop frame / đơ giao diện.
   - Bản mới chuyển `domIdentifier` và `domClasses` thành dạng provider lười nạp (lazy load), chỉ query khi thực sự có component yêu cầu. Chi phí snapshot giảm từ ~10 xuống còn 5 lời gọi AX.
2. **Caching Fallback Detection (Priority 2):**
   - Trước đây `getConfirmedInjectionMethod()` không cache kết quả fallback live-detection. Mỗi khi `clearMethodCache()` kích hoạt (do click chuột, phím mũi tên, chuyển app), các phím gõ tiếp theo đều phải chạy full AX snapshot trên luồng event tap, gây tắc nghẽn luồng gõ khi Chrome phản hồi chậm.
   - Bản mới lưu kết quả qua `setConfirmedInjectionMethod(detected)`.
3. **Hot-path `signature` không gọi Renderer:**
   - Thuộc tính `signature` dùng `domIdentifierIfLoaded` để tính hash nhận diện focus mà không bao giờ kích hoạt lazy query sang renderer.
4. **Role Gate cho Address Bar:**
   - Trong `isChromiumAddressBar` và `isFirefoxStyleAddressBar`, kiểm tra `info.role == "AXTextField"` trước khi fallback xuống DOM check, đảm bảo các phần tử nội dung web (AXTextArea, AXWebArea) không bị query nhầm.
5. **Kiểm thử tự động:**
   - Thêm 17 test cases mới trong `XKeyTests/FocusedElementInfoLazyTests.swift`.

---

## 2. Đánh giá tính cần thiết đối với Repo của chúng ta

### Đánh giá theo hai chế độ gõ:
- **Chế độ IMKit (XKeyIM - Chế độ gõ có gạch chân marked text):**
  - Fork hiện tại của chúng ta đang sử dụng và tùy biến chuyên sâu cho XKeyIM (`XKeyIMController.swift`, hỗ trợ Macro, chống tràn space khi lost focus, bỏ qua false-positive cursor trên VS Code).
  - Chế độ IMKit hoạt động qua `IMKServer` của macOS, không phụ thuộc vào event tap AX injection của `AppBehaviorDetector`. Do đó bản hiện tại không bị ảnh hưởng bởi lỗi freeze trên Gmail.
- **Chế độ CGEvent / Accessibility (XKey.app - App chính):**
  - Chế độ này sử dụng trực tiếp `AppBehaviorDetector` để nhận diện ngữ cảnh và chọn phương thức inject (clipboard/event).
  - Bản vá của upstream mang lại cải thiện hiệu năng rõ rệt khi người dùng sử dụng XKey ở chế độ Accessibility tap với Chrome/Gmail.

### Đánh giá nguy cơ xung đột mã nguồn:
- Các file upstream chỉnh sửa:
  - `Shared/AppBehaviorDetector.swift`
  - `XKeyTests/FocusedElementInfoLazyTests.swift`
  - `.gitignore`
  - `XKey.xcodeproj/project.pbxproj`
  - `Version.xcconfig`
- Các file fork của chúng ta chỉnh sửa:
  - `XKeyIM/XKeyIMController.swift`
  - `XKey/Core/Engine/VNEngine.swift`
  - `build_release.sh`
  - `Version.xcconfig`
- **Kết luận:** Giữa hai bên **không có bất kỳ xung đột mã nguồn nào** (Zero merge conflicts trong logic chính).

---

## 3. Khuyến nghị & Kết luận

1. **Hiện trạng repo:** Repo hiện tại (với bản build `20260816`) đang hoạt động rất tốt, ổn định và đầy đủ tính năng cho nhu cầu hàng ngày.
2. **Không có lỗi khẩn cấp cần fix:** Không phát hiện bất kỳ lỗi nào trong repo cần phải sửa ngay lập tức.
3. **Kế hoạch tương lai:** Khi cần tối ưu thêm cho chế độ CGEvent trên Google Chrome, có thể tích hợp trực tiếp commit `7a59d9a` vào repo mà hoàn toàn yên tâm về độ ổn định của các bản vá XKeyIM hiện tại.
