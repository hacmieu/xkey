# Phân tích vấn đề "gạch dưới" và "đẩy dấu" khi gõ tiếng Anh

**Ngày giờ:** 2026-08-24 14:31

## Nguyên nhân
Dựa trên việc phân tích mã nguồn (`XKeyIMController.swift`) và tài liệu (`IMKitResearch.md`, `20260814_1110-English_Token_Retry_Analysis.md`):

1. **Vấn đề "bị gạch ở dưới" (Marked Text):** 
   - Đây là hành vi thiết kế tiêu chuẩn của framework **Input Method Kit (IMKit)** trên macOS. 
   - XKeyIM cần giữ các ký tự vừa gõ trong một buffer tạm thời (được hiển thị bằng nét gạch dưới) để có thể thay đổi chúng nếu người dùng gõ thêm dấu. Ví dụ: khi gõ `t`, `h`, `u`, chữ `thu` sẽ được gạch chân để chờ nếu người dùng gõ tiếp `w` thì sẽ biến thành `thư`. 
   - IMKit giữ marked text cho mọi ký tự chữ (letters) cho tới khi bạn gõ dấu cách (Space) hoặc dấu câu (Punctuation) để hoàn thành từ.

2. **Vấn đề "đẩy dấu vào" khi gõ tiếng Anh:**
   - Cơ chế bắt dấu của `VNEngine` hoạt động theo thời gian thực. Khi bạn gõ các phím có thể là dấu (như `s`, `f`, `j`, `r`, `x` trong kiểu gõ Telex), engine sẽ áp dụng dấu ngay lập tức nếu tiền tố hợp lệ.
   - Như được ghi nhận trong `English_Token_Retry_Analysis`, từ tiếng Anh như `adsense` có tiền tố `ad` là hợp lệ trong tiếng Việt, nên phím `s` sẽ bị xử lý thành dấu sắc (`ádsense`). Chỉ sau khi gõ xong toàn bộ từ và ấn Space, hệ thống kiểm tra chính tả (Spell Check) mới nhận diện đây là từ ngoại lệ và khôi phục lại nguyên bản. Tuy nhiên trong lúc gõ sẽ gây cảm giác "bị đẩy dấu vào".

## Hướng xử lý (Solutions)

Tài liệu và mã nguồn hiện tại đã hỗ trợ một số phương án giải quyết triệt để vấn đề này:

### 1. Sử dụng tính năng "Phím tắt chuyển nhanh sang XKey" (Khuyến nghị)
Đây là cách native và tối ưu nhất của macOS để gõ tiếng Anh thuần túy:
- **Thực hiện:** Mở **XKey Settings** -> **Quản lý Input Sources** -> Cài đặt **Phím tắt chuyển nhanh sang XKey** (ví dụ: `Cmd + Space` hoặc một phím tùy thích).
- **Tác dụng:** Phím tắt này giúp bạn chuyển qua lại cực nhanh giữa **XKey** và bộ gõ tiếng Anh gốc của máy (ví dụ: **ABC**). Khi chuyển sang ABC, bạn sẽ gõ tiếng Anh thuần túy, hoàn toàn không có gạch dưới và không bị đẩy dấu.

### 2. Tắt chế độ "Hiển thị gạch chân khi gõ" (Direct Mode)
- **Thực hiện:** Mở **XKey Settings** -> **Input Method Kit (Thử nghiệm)** -> Tắt tùy chọn **Hiển thị gạch chân khi gõ**.
- **Tác dụng:** XKeyIM sẽ chuyển sang **Direct Mode**, chữ sẽ được insert trực tiếp vào ứng dụng mà không có nét gạch dưới. 
- **Lưu ý:** Chữ vẫn có thể bị "đẩy dấu" tạm thời (do VNEngine vẫn chạy) và XKeyIM sẽ dùng phím backspace giả lập để sửa lại chữ.

### 3. Tắt Tiếng Việt qua Menu Bar
- **Thực hiện:** Bấm vào icon của XKey trên thanh Menu Bar ở góc phải màn hình, chọn **"Tắt Tiếng Việt"**.
- **Tác dụng:** Tắt toàn bộ engine VNEngine, cho phép gõ tiếng Anh hoàn toàn bình thường mà không bị can thiệp.

## Kết luận
Hành vi này là do bản chất hoạt động của Input Method Kit và Engine tiếng Việt chứ không phải là lỗi phần mềm. Người dùng nên sử dụng **Phím tắt chuyển nhanh Input Source (sang ABC)** khi cần gõ văn bản tiếng Anh dài để có trải nghiệm mượt mà nhất.
