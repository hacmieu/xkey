# Kế hoạch: Bổ sung luật VCV để nhận diện gõ tiếng Anh giữa chừng (Instant Restore)

**Ngày:** 2026-08-25 14:38

## Vấn đề
- Người dùng gõ "Adsense", tuy nhiên các ký tự vẫn bị bám dấu (ví dụ: 's' biến 'Ad' thành 'Ád', giữ gạch chân). 
- Thuật toán `isDefinitelyNotVietnameseForRawInput` và `hasEnglishStartPattern` cũ chỉ kiểm tra từ ở **đầu hoặc cuối** (chứa các cụm phụ âm vô lý như `str`, `bl`, hoặc ký tự `f`, `j`, `z`). Do đó từ "Adsense" (bắt đầu bằng A) đi lọt qua lưới lọc.

## Giải pháp (Luật VCV)
- Trong tiếng Việt, cấu trúc âm tiết là `Phụ âm đầu + Nguyên âm + Phụ âm cuối`. Các nguyên âm luôn phải đứng liền kề nhau.
- Nếu chuỗi thô (Raw input) chứa dạng: `Nguyên âm` + `(Các) Phụ âm` + `Nguyên âm`, trong đó **có ít nhất một Phụ âm thực sự (Strict Consonant)** (không phải dấu thanh Telex như `s, f, r, x, j`), thì chắc chắn đó **không phải là tiếng Việt** (hoặc là 2 từ viết dính liền không dấu cách).
- **Loại trừ ngoại lệ:** Các mẫu gõ tắt nguyên âm trễ của Telex như `c a n a -> cân`, `t h e m e -> thêm`, `c o n g o -> công`, hoặc `d u o n g w -> đường`.
- Khi gõ "Adsense", chuỗi nhận được `a` (vowel) + `d` (strict) + `s` (tone) + `e` (vowel). Phát hiện `d` nằm giữa 2 nguyên âm -> Kích hoạt Instant Restore ngay ở chữ `e`!

## Triển khai
- Chỉnh sửa `VNEngineEnglishDetection.swift`.
- Viết lại hàm `hasEnglishStartPattern` bằng việc dò tìm Vowel - Consonants - Vowel với strict check.
- Compile và Deploy lại bản `1.2.25 (20260825.1436)`.
