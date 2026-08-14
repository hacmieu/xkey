# Timestamp Convention — 2026-08-14 Builds

**Date:** 2026-08-14 14:29  
**Status:** Convention established

## Quy ước file log cho ngày 14/08

Vì hôm nay sẽ build nhiều bản để fix và test, tất cả file ghi chú trong `./memory/`, `./plans/`, `./reports/` sẽ sử dụng định dạng:

```
YYYYMMDD_HHMM-<Nội dung ngắn gọn>.md
```

## Ví dụ

- `20260814_1429-Build_Debug_XKeyIM.md`
- `20260814_1430-Fix_Cursor_Skip_Logic.md`
- `20260814_1515-Test_VSCode_Typing.md`

## Quy tắc

1. **Timestamp = thời điểm tạo file**, không phải thời điểm bắt đầu task.
2. **Tên file ngắn gọn**, không dấu, không khoảng trắng (dùng `_`).
3. **Mỗi file phải có link trong README.md** của thư mục đó.
4. **README.md là Single Source of Truth** — tóm tắt và liên kết đến file chi tiết.

## Cập nhật README

Sau mỗi file mới, cập nhật ngay dòng tóm tắt trong `memory/README.md`, `plans/README.md`, `reports/README.md` với timestamp và link.

## Build stamp hôm nay

- Build lần này: `20260814_1429`
- Trạng thái: Đã deploy XKeyIM Debug
- Ghi chú: Re-select input source sau mỗi lần deploy