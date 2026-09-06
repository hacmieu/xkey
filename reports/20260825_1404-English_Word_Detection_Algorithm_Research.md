# Nghiên cứu Thuật toán Nhận diện Từ Tiếng Anh và Khôi phục Phím

**Ngày giờ:** 2026-08-25 14:04

## Vấn đề
Người dùng gặp khó chịu khi gõ các từ tiếng Anh (ví dụ: `adsense`, `of`) nhưng bộ gõ lại cố gắng ghép dấu tiếng Việt (`ádsense`, `ò`), và từ đó liên tục bị gạch dưới (Marked Text) trong quá trình gõ, gây cản trở trải nghiệm. 

## Phân tích Thuật toán "Khôi phục phím" (Auto-Restore)
Trên các bộ gõ phổ biến như Unikey, EVKey hay OpenKey, thuật toán để giải quyết vấn đề này thường dựa trên 2 phương pháp chính:

### 1. Thuật toán Dựa trên Luật Ghép Vần (Phonotactic Rules)
Đây là cách tiếp cận nhẹ, nhanh và phổ biến nhất (chính là tính năng "Tự động khôi phục phím với từ sai" của Unikey).
- **Nguyên lý:** Tiếng Việt có cấu trúc âm tiết rất chặt chẽ (Phụ âm đầu + Nguyên âm/Vần + Phụ âm cuối). Số lượng vần hợp lệ trong tiếng Việt là hữu hạn.
- **Cách hoạt động:**
  - Mỗi khi người dùng gõ một phím, bộ gõ kiểm tra xem chuỗi ký tự hiện tại (tính từ sau dấu cách gần nhất) có khả năng tạo thành một âm tiết tiếng Việt hợp lệ hay không.
  - Nếu người dùng gõ `a` -> `d` -> `s` (trong chữ `adsense`). Chữ `ads` có thể bị engine hiểu lầm `s` là dấu sắc. Nhưng khi gõ tiếp `e`, chuỗi `ádse` hoàn toàn vi phạm luật ghép vần tiếng Việt.
  - Lúc này, thuật toán sẽ kích hoạt **Khôi phục (Restore)**: Hủy bỏ việc ghép dấu sắc trước đó, trả lại nguyên gốc là `ads`, và ngừng áp dụng luật tiếng Việt cho phần còn lại của từ này cho tới khi gặp khoảng trắng (Space).
  - Đồng thời, khi từ đã bị đánh dấu là "từ ngoại lai" (tiếng Anh), bộ gõ có thể tự động **Commit Composition** ngay lập tức để loại bỏ nét gạch chân (Marked Text), giúp chữ đi thẳng vào ứng dụng.

### 2. Thuật toán Dựa trên Từ điển (Dictionary-based / N-gram)
- **Nguyên lý:** Sử dụng một danh sách (Trie hoặc Hash Set) chứa các từ tiếng Anh thông dụng và các từ tiếng Việt hợp lệ.
- **Cách hoạt động:**
  - Khi gõ xong một từ (hoặc đang gõ), hệ thống đối chiếu với từ điển.
  - Nếu chuỗi khớp hoàn toàn với một từ tiếng Anh (như `hello`, `world`), thuật toán sẽ trả lại chuỗi gốc và xóa dấu gạch dưới.
  - Nhược điểm: Tốn bộ nhớ hơn và không xử lý được các từ tiếng Anh/từ viết tắt không có trong từ điển.

## Đề xuất Áp dụng cho XKey
Để giải quyết triệt để sự khó chịu này trên XKeyIM, ta cần kết hợp kiểm tra tính hợp lệ của âm tiết ngay trong vòng lặp của `VNEngine`:
1. **Bổ sung hàm `isValidVietnameseSyllable(prefix)`**: Kiểm tra xem buffer hiện tại có vi phạm luật chính tả tiếng Việt hay không.
2. **Kích hoạt Auto-Restore**: Nếu vi phạm (ví dụ `of`, `adsense`), tự động rollback các phím dấu (nếu đã lỡ đổi thành `ò`, `á`) trả về phím gốc.
3. **Commit xóa gạch dưới**: Khi đã xác định là từ tiếng Anh/từ sai, gọi hàm `commitComposition()` của IMKit để đưa text thẳng ra màn hình và xóa gạch dưới, ngắt buffer của XKeyIM cho từ đó.

## Nguồn tham khảo
- Mã nguồn OpenKey / Unikey (C++): Phân tích hàm `CheckSpelling` và `RestoreKey`.
- Tài liệu về cơ chế Hook và Auto-Restore của các bộ gõ tiếng Việt.
