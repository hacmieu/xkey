# Rollback các thay đổi tự động nhận diện tiếng Anh (Instant Restore & VCV)

**Ngày:** 2026-08-25 14:44

## Bối cảnh & Nguyên nhân
- Suốt ngày 2026-08-25, chúng ta đã cố gắng giải quyết bài toán "gõ từ tiếng Anh (vd: Adsense) bị kẹt dấu tiếng Việt".
- Các phương pháp đã áp dụng gồm: Tự động `commitComposition` (Instant Restore), mở rộng luật `hasEnglishStartPattern`, và thêm luật VCV (Nguyên âm - Phụ âm - Nguyên âm).
- Tuy nhiên người dùng nhận thấy việc sử dụng phím `ESC` để ngắt tiếng Việt là phương pháp hiệu quả và đã giải quyết được vấn đề thực tế của họ mà không cần các logic phức tạp trên.
- Do đó toàn bộ logic thêm vào hôm nay được Rollback.

## Chi tiết Rollback
- Mã nguồn đã được khôi phục về trạng thái trước đó thông qua `git restore`.
- File `build_release.sh` cũng bị revert về trạng thái ban đầu (không còn disable iCloud entitlement cho Adhoc). Nếu tương lai bị lỗi "Application can't be opened (162)", cần xem lại nhật ký `20260825_1423-Fix_AdHoc_iCloud_Entitlement_Memory.md` để khôi phục fix này một cách độc lập nếu cần.
- Tính năng nhấn `ESC` để thoát bộ gõ tiếng Việt đã hoạt động tốt như thiết kế nguyên bản.
