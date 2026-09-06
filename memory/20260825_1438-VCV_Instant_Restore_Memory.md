# Nhận diện tiếng Anh thông minh qua Luật VCV (Vowel-Consonant-Vowel)

**Ngày:** 2026-08-25 14:38

## Bài học và giải pháp
- **Nguyên lý hình thái tiếng Việt:** Trong 1 từ tiếng Việt (đơn âm tiết), các nguyên âm luôn phải đứng liền kề nhau tạo thành một hạt nhân (nucleus). Tuyệt đối không bao giờ có chuyện Phụ âm xen giữa hai Nguyên âm (V-C-V).
- **Luật nhận diện mới (VCV Rule):** Khi người dùng gõ phím, phân tích Raw Input. Nếu chuỗi có dạng: `Nguyên âm (a,e,i,o,u,y,w)` + `Phụ âm` + `Nguyên âm`.
  - Loại bỏ các phím thanh điệu Telex (`s, f, r, x, j`) vì người dùng gõ tự do (Free Mark) có thể chèn chúng vào bất cứ đâu.
  - Phụ âm kẹp giữa bắt buộc phải chứa ít nhất 1 Phụ âm cứng (Strict Consonant: `b, c, d, g, h, k, l, m, n, p, q, t, v, z`).
  - Nếu thỏa mãn, thì đó là 1 cụm VCV bất hợp pháp trong tiếng Việt (ví dụ `a-d-e` trong `Adsense`, `e-ss-a-g-e` trong `message`).
- **Ngoại lệ (Late Modifiers):** Cần loại trừ các trường hợp gõ Telex muộn dấu mũ/râu:
  - `a + phụ âm + a` -> `â` (ví dụ `cana` -> `cân`).
  - `e + phụ âm + e` -> `ê` (ví dụ `theme` -> `thêm`).
  - `o + phụ âm + o` -> `ô` (ví dụ `xoongo` -> `xông`).
  - `(a,o,u) + phụ âm + w` -> `(ă,ơ,ư)` (ví dụ `duongw` -> `đường`).
- Bằng cách thêm luật này vào thuộc tính `hasEnglishStartPattern`, XKey nay có thể nhận biết và phục hồi tiếng Anh ngay tức khắc (Instant Restore) từ giữa từ (ngay khi chữ cái tạo thành mẫu VCV được gõ xuống) thay vì phải đợi gõ phím cách hoặc gõ xong chữ.
