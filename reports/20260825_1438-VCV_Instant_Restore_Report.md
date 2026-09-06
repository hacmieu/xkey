# Báo cáo: Triển khai Luật VCV (Vowel-Consonant-Vowel) cho Instant Restore

**Ngày:** 2026-08-25 14:38

## Chi tiết cập nhật
- Khi người dùng gõ chuỗi tiếng Anh có chứa cấu trúc Nguyên âm - Phụ âm - Nguyên âm (ví dụ `a-d-e` trong `Adsense`, `a-c-e` trong `facebook`), hệ thống đã không thể nhận diện ngay lập tức trong phiên bản trước.
- **Tính năng mới:** Thêm luật VCV. Quét chuỗi `Raw Input`. Nếu tìm thấy một phụ âm cứng (`b, c, d, g, h, k, l, m, n, p, q, t, v, z`) nằm kẹp giữa 2 nguyên âm (`a, e, i, o, u, y, w`), hệ thống sẽ phân loại đây là từ tiếng Anh.
- Đã xử lý ngoại lệ cẩn thận với cơ chế bỏ dấu muộn (Late Modifiers) của Telex: các chuỗi như `a-n-a` (cân), `e-m-e` (thêm), `o-n-w` (ươn) sẽ không bị dính cờ Tiếng Anh.
- Đã cập nhật version lên `20260825.1436`. 
- **Kết quả:** Gõ `Adsense`, `facebook`, `youtube`, `message` sẽ lập tức tắt gạch chân và phục hồi thành tiếng Anh ngay từ giữa từ.
