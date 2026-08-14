# Kế hoạch sửa Macro cho XKeyIM

**Ngày:** 2026-08-13  
**Cập nhật:** 2026-08-13 17:25 — Implementation DONE, đang verify build  
**Ưu tiên:** Cao — tính năng Macro hoàn toàn không hoạt động trong XKeyIM  
**Tham chiếu phân tích:** [`reports/20260813_1648-Macro_System_Analysis_XKeyIM.md`](file:///Users/hacmieu/DevOps/xkey/reports/20260813_1648-Macro_System_Analysis_XKeyIM.md)

---

## Mục tiêu

Khi người dùng bật XKeyIM (chế độ gạch dưới / marked text) và đã cấu hình macro (vd: `rrr → replacement`), gõ `rrr` + Space phải thay thế bằng văn bản macro tương ứng.

---

## Các bước cần thực hiện

### Bước 1: Cập nhật `XKeyIMSettings` — đọc macro settings từ plist

**File:** `XKeyIM/XKeyIMController.swift` (class `XKeyIMSettings`)

Thêm properties:
```swift
var macroEnabled: Bool = false
var macroInEnglishMode: Bool = false
var autoCapsMacro: Bool = false
var addSpaceAfterMacro: Bool = false
```

Trong `reload()`, thêm:
```swift
macroEnabled = readBool(forKey: "XKey.macroEnabled")
macroInEnglishMode = readBool(forKey: "XKey.macroInEnglishMode")
autoCapsMacro = readBool(forKey: "XKey.autoCapsMacro")
addSpaceAfterMacro = readBool(forKey: "XKey.addSpaceAfterMacro")
```

> ⚠️ Cần xác minh tên key chính xác trong plist bằng cách đọc `SharedSettings.swift`.

### Bước 2: Cập nhật `applySettings()` — truyền macro config vào Engine

**File:** `XKeyIM/XKeyIMController.swift` (method `applySettings`)

```swift
engineSettings.macroEnabled = settings.macroEnabled
engineSettings.macroInEnglishMode = settings.macroInEnglishMode
engineSettings.autoCapsMacro = settings.autoCapsMacro
engineSettings.addSpaceAfterMacro = settings.addSpaceAfterMacro
```

### Bước 3: Khởi tạo MacroManager và load macro data

**File:** `XKeyIM/XKeyIMController.swift` (trong `init` hoặc `applySettings`)

Cần:
1. Tìm đường dẫn file macro (shared qua App Group hoặc fixed path)
2. Khởi tạo `MacroManager()` instance
3. Load macro data từ file: `macroManager.loadFromFile(path:)`
4. Gắn vào engine: `VNEngine.setSharedMacroManager(macroManager)`

> ⚠️ Cần kiểm tra cách XKey main app lưu/chia sẻ file macro giữa hai processes.

### Bước 4: Xử lý Macro result trong Space handler

**File:** `XKeyIM/XKeyIMController.swift` (case `0x31: // Space`)

Thêm nhánh xử lý macro trước restore:

```swift
case 0x31: // Space
    if !composingText.isEmpty || currentWordLength > 0 {
        let result = engine.processWordBreak(character: " ")
        
        // Check macro replacement (đây là case mới)
        let isMacroCase = result.shouldConsume && result.backspaceCount > 0
        
        if isMacroCase && effectiveUseMarkedText {
            // Lấy replacement text từ result.newCharacters (KHÔNG dùng getCurrentWord!)
            let replacementText = result.newCharacters.map { $0.unicode(codeTable: settings.codeTable) }.joined()
            
            // Clear marked text hiện tại
            // Insert replacement text
            // Commit
        }
    }
```

> ⚠️ Cần phân biệt macro result vs spell restore result. Có thể dùng `result.newCharacters.count` vs `result.backspaceCount` hoặc thêm flag trong `ProcessResult`.

---

## Rủi ro / Lưu ý

1. **Phân biệt Macro vs Restore:** Cả hai đều có `shouldConsume=true` và `backspaceCount > 0`. Cần cơ chế phân biệt (flag hoặc so sánh content).
2. **Macro file sharing:** Cần đảm bảo XKeyIM process có thể đọc được file macro từ shared container.
3. **Quick Telex conflict:** `kk→kh` xảy ra trước macro check. Nếu macro key là `kkk`, engine sẽ transform thành `khk` trước → macro không match. Cần quyết định: Quick Telex ưu tiên hơn hay Macro?
4. **English mode macro:** Cần xử lý riêng path cho macro khi ở chế độ English.

---

## Ước tính

| Bước | Effort |
|------|--------|
| Bước 1: Settings | Nhỏ (15 phút) |
| Bước 2: applySettings | Nhỏ (5 phút) |
| Bước 3: MacroManager init | Trung bình (1-2 giờ, cần nghiên cứu file sharing) |
| Bước 4: Space handler | Trung bình (1-2 giờ, cần test kỹ) |
| **Tổng** | **~3-4 giờ** |
