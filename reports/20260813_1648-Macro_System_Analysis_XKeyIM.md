q

# Phân tích hệ thống Macro trong XKey — Tại sao Macro không hoạt động trong XKeyIM (chế độ gạch dưới)

**Ngày:** 2026-08-13
**Phạm vi:** Toàn bộ luồng Macro từ nhập liệu → engine → injection
**Git HEAD:** `4a57de2` (clean, không có uncommitted changes)

---

## 1. Tóm tắt vấn đề

Khi gõ macro shortcut (vd: `rrr`, `kkk`) trong chế độ **XKeyIM** (Input Method Kit — chế độ có gạch dưới / marked text), văn bản thay thế **không xuất hiện**. Macro chỉ hoạt động ở chế độ **CGEvent** (XKey main app).

---

## 2. Kiến trúc tổng quan Macro

### 2.1. Các file liên quan

| File                                                                                                                            | Vai trò                                                              |
| ------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------- |
| [`MacroManager.swift`](file:///Users/hacmieu/DevOps/xkey/XKey/Core/Engine/MacroManager.swift)                                  | Lưu trữ, tìm kiếm, import/export macro                            |
| [`VNEngineMacro.swift`](file:///Users/hacmieu/DevOps/xkey/XKey/Core/Engine/VNEngineMacro.swift)                                | Extension VNEngine cho macro processing                               |
| [`VNEngine.swift`](file:///Users/hacmieu/DevOps/xkey/XKey/Core/Engine/VNEngine.swift) (L3380-3410)                             | `processWordBreak()` — trigger macro khi Space                     |
| [`VNEngineSettings.swift`](file:///Users/hacmieu/DevOps/xkey/XKey/Core/Engine/VNEngineSettings.swift) (L32-35,84-87)           | Cấu hình macro: enabled, englishMode, autoCaps, addSpace            |
| [`KeyboardEventHandler.swift`](file:///Users/hacmieu/DevOps/xkey/XKey/EventHandling/KeyboardEventHandler.swift) (L137,152,669) | CGEvent mode: tạo`MacroManager`, gắn vào engine, accumulate keys |
| [`XKeyIMController.swift`](file:///Users/hacmieu/DevOps/xkey/XKeyIM/XKeyIMController.swift)                                    | **XKeyIM mode: KHÔNG có bất kỳ xử lý macro nào**         |

### 2.2. Luồng xử lý Macro trong CGEvent mode (hoạt động)

```
1. User gõ ký tự → KeyboardEventHandler.handleKeyDown()
2. Với mỗi ký tự:
   - engine.addKeyToMacroBuffer(keyCode, isCaps) → thêm vào hookState.macroKey
   - engine.processKey() → xử lý Vietnamese bình thường, đồng thời update macroKey
3. User gõ Space:
   - handler gọi engine.processWordBreak(" ")
   - processWordBreak() gọi findAndReplaceMacro()
   - findAndReplaceMacro() tìm trong MacroManager.findMacro()
   - Nếu tìm thấy → trả về ProcessResult (shouldConsume=true, backspaceCount, newCharacters)
   - handler nhận result → injector.inject(backspace + replacement text)
4. MacroManager được khởi tạo trong KeyboardEventHandler(L137) và gắn vào VNEngine(L152)
```

### 2.3. Luồng trong XKeyIM mode (KHÔNG hoạt động)

```
1. User gõ ký tự → XKeyIMController.handle(_:client:)
2. engine.processKey() → xử lý Vietnamese
3. handleResult() → setMarkedText(currentWord) → hiển thị gạch dưới ✅
4. User gõ Space (case 0x31):
   - engine.processWordBreak(" ")
   - processWordBreak() NỘI BỘ gọi findAndReplaceMacro()
   - Nhưng shouldUseMacro() return FALSE → vì vUseMacro = 0
   - KẾT QUẢ: macro KHÔNG BAO GIỜ được tìm kiếm ❌
```

---

## 3. Nguyên nhân gốc (Root Causes)

### ❌ Nguyên nhân #1: XKeyIMSettings THIẾU hoàn toàn các setting về Macro

**File:** [`XKeyIMController.swift:1376-1496`](file:///Users/hacmieu/DevOps/xkey/XKeyIM/XKeyIMController.swift#L1376-L1496)

`XKeyIMSettings` class chỉ đọc các setting cơ bản từ plist:

- inputMethod, codeTable, modernStyle, spellCheckEnabled
- quickTelex, quickStart/EndConsonant, restoreIfWrongSpelling
- upperCaseFirstChar, capitalizeOnlyAfterSpace, customConsonants
- useMarkedText, debugModeEnabled

**THIẾU hoàn toàn:**

- `macroEnabled` ❌
- `macroInEnglishMode` ❌
- `autoCapsMacro` ❌
- `addSpaceAfterMacro` ❌

### ❌ Nguyên nhân #2: applySettings() KHÔNG truyền Macro settings vào Engine

**File:** [`XKeyIMController.swift:134-159`](file:///Users/hacmieu/DevOps/xkey/XKeyIM/XKeyIMController.swift#L134-L159)

```swift
private func applySettings() {
    var engineSettings = VNEngine.EngineSettings()
    engineSettings.inputMethod = settings.inputMethod
    // ... các setting khác ...
  
    // ❌ THIẾU:
    // engineSettings.macroEnabled = ???
    // engineSettings.macroInEnglishMode = ???
    // engineSettings.autoCapsMacro = ???
    // engineSettings.addSpaceAfterMacro = ???
  
    engine.updateSettings(engineSettings)
}
```

Vì `EngineSettings.macroEnabled` default là `false` (L32), engine luôn có `vUseMacro = 0`.

### ❌ Nguyên nhân #3: XKeyIM KHÔNG khởi tạo MacroManager

Trong CGEvent mode:

```swift
// KeyboardEventHandler.swift L137, L152
private let macroManager = MacroManager()
VNEngine.setSharedMacroManager(macroManager)
```

Trong XKeyIM:

- **KHÔNG có** `MacroManager()` initialization
- **KHÔNG có** `VNEngine.setSharedMacroManager()` call
- **KHÔNG có** macro file loading

Engine vẫn tạo lazy `MacroManager` (VNEngineMacro.swift L19-22), nhưng instance này **trống rỗng** — không có macro data nào.

### ❌ Nguyên nhân #4: XKeyIM Space handler KHÔNG xử lý Macro result đúng

**File:** [`XKeyIMController.swift:627-693`](file:///Users/hacmieu/DevOps/xkey/XKeyIM/XKeyIMController.swift#L627-L693)

Ngay cả nếu macro settings được bật và data được load, handler Space:

```swift
case 0x31: // Space
    let result = engine.processWordBreak(character: " ")
    let isRestoreCase = result.shouldConsume && result.backspaceCount > 0
  
    if isRestoreCase && effectiveUseMarkedText {
        // Xử lý restore case — NHƯNG macro CŨNG là restore case!
        // Macro: shouldConsume=true, backspaceCount > 0
        // → Sẽ vào branch này, nhưng code chỉ dùng cho spell restore
        // → Macro content từ result.newCharacters sẽ KHÔNG được format đúng
    }
```

Vấn đề: khi macro found, `processWordBreak` gọi `reset()` sau đó trả result. Branch `isRestoreCase` gọi `handleResult` → `engine.getCurrentWord()` nhưng engine đã reset → trả rỗng.

**Thực tế:** Nếu macro hoạt động, result flow cũng bị xử lý sai vì `handleResult()` dùng `getCurrentWord()` thay vì `result.newCharacters`.

---

## 4. Tại sao ký tự có gạch dưới

Khi XKeyIM (marked text mode) nhận ký tự:

```swift
// XKeyIMController.swift L780-793
} else if character.isLetter {
    let currentWord = engine.getCurrentWord()
    if !currentWord.isEmpty {
        setMarkedText(currentWord, client: client) // ← gạch dưới
    } else {
        setMarkedText(String(character), client: client) // ← gạch dưới
    }
    return true
}
```

Mọi ký tự letter đều được hiển thị với underline thông qua `setMarkedText()`. Đây là hoạt động IMKit chuẩn và ĐÚNG — nhưng khi Space, macro replacement không xảy ra nên text chỉ được commit nguyên bản.

---

## 5. So sánh CGEvent vs XKeyIM

| Khía cạnh                   | CGEvent (XKey)                         | XKeyIM                               |
| ----------------------------- | -------------------------------------- | ------------------------------------ |
| MacroManager initialized      | ✅`KeyboardEventHandler(L137)`       | ❌ Không                            |
| MacroManager shared w/ engine | ✅`setSharedMacroManager(L152)`      | ❌ Không                            |
| Macro data loaded             | ✅ Từ file`.txt`                    | ❌ Trống                            |
| macroEnabled in settings      | ✅`SharedSettings → EngineSettings` | ❌ Default = false                   |
| `vUseMacro` in engine       | ✅ = 1                                 | ❌ = 0                               |
| Space → macro check          | ✅`findAndReplaceMacro()`            | ❌`shouldUseMacro()` returns false |
| Macro result injection        | ✅`injector.inject()`                | ❌ N/A                               |

---

## 6. Kết luận

**Macro trong XKeyIM hoàn toàn chưa được implement.** Có 4 thiếu sót cần fix:

1. `XKeyIMSettings` cần đọc macro settings từ plist
2. `applySettings()` cần truyền macro settings vào `VNEngine.EngineSettings`
3. `XKeyIMController` cần khởi tạo `MacroManager`, load macro data, và gắn vào engine
4. Space handler cần xử lý macro result riêng (không dùng chung path với spell restore)

---

## Appendix: Quick Telex cho `kk` và `rr`

- **`kk → kh`**: Thuộc Quick Telex feature (`isQuickTelexKey`). Key K nằm trong danh sách (L1242). Điều kiện: `prevKey == keyCode && index == 1` (double letter ở đầu từ). Feature này **hoạt động** trong XKeyIM vì `quickTelexEnabled` được truyền.
- **`rrr`**: Key R **KHÔNG nằm** trong Quick Telex (C,G,K,N,Q,P,T). `rrr` chỉ có thể là macro shortcut → cần Macro system hoạt động.
- **`kkk`**: Gõ `kk` → Quick Telex chuyển thành `kh`. Gõ thêm `k` → thành `khk`. Nếu `kkk` là macro, nó sẽ không match vì engine đã transform `kk→kh` trước khi macro buffer nhận được.
