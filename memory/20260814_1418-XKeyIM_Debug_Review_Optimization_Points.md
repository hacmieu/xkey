# XKeyIM Debug Review — Daily Optimization Points

**Date:** 2026-08-14 14:18  
**Status:** Review complete — 6 optimization points identified

## Context

Reviewed: `memory/README.md`, git diff (4 modified files), all markdown notes in `memory/`, `plans/`, `reports/`, and recent `~/XKey_Debug.log` structure.

## 6 Optimization Points for Today

### 1. Cursor Tracking False-Positive (VS Code)
- **File:** `XKeyIM/XKeyIMController.swift`
- **Status:** Fix implemented (`skipNextCursorCheck`) but unverified by user
- **Risk:** ±1 tolerance was insufficient; final flag-based fix needs real-world validation
- **Action:** Test `dd ow`, `thoong`, `heer`, `cuar` in VS Code after re-selecting XKeyIM

### 2. Macro System in XKeyIM
- **Files:** `XKeyIM/XKeyIMController.swift`, `XKey/Core/Engine/VNEngine.swift`
- **Status:** Implemented (`isMacroReplacement` flag, MacroManager init, Space handler)
- **Risk:** Macro replacement in marked-text mode may have edge cases with `addSpaceAfterMacro`
- **Action:** Add unit tests for macro expansion in IMKit path

### 3. Version Config Linking
- **Files:** `Version.xcconfig`, `XKey.xcodeproj/project.pbxproj`
- **Status:** Done — `CURRENT_PROJECT_VERSION = 20260813`, XKeyIM target now references `Version.xcconfig`
- **Risk:** Release build still fails signing; Debug build works with `CODE_SIGN_IDENTITY=""`
- **Action:** Document build command or fix signing for Release

### 4. Log Noise Reduction
- **Files:** `Shared/DebugLogger.swift`, `XKeyIM/IMKitDebugger.swift`
- **Status:** Identified in `20260814_1052-Log_Based_Optimization`
- **Finding:** `[ENGINE]` 650, `[CURSOR]` 641, `[OVERLAY]` 515 logs per session
- **Action:** Add log levels; reduce `[OVERLAY]` to once-per-app; add duration to `[TIMING]`

### 5. English Token Retry Pain (e.g., `adsense`)
- **Files:** `XKey/Core/Engine/VNEngineEnglishDetection.swift`
- **Status:** Root cause analyzed in `20260814_1110-English_Token_Retry_Analysis`
- **Finding:** Real-time detector only checks impossible prefixes; `adsense` starts with valid `ad`
- **Action:** Add privacy-preserving retry telemetry; learn frequently-corrected tokens locally

### 6. Build & Deploy Friction
- **Finding:** Debug build requires `CODE_SIGN_IDENTITY="" CODE_SIGNING_REQUIRED=NO CODE_SIGNING_ALLOWED=NO`
- **Risk:** Release build blocked by signing cert
- **Action:** Either configure CI signing or standardize Debug-only deploy script

## Recommended Priority

1. Verify cursor fix (user testing)
2. Add macro IMKit tests
3. Reduce log noise + add timing duration
4. Implement English retry telemetry
5. Fix Release signing

## Related Files

- `XKeyIM/XKeyIMController.swift`
- `XKey/Core/Engine/VNEngine.swift`
- `XKey/Core/Engine/VNEngineEnglishDetection.swift`
- `Shared/DebugLogger.swift`
- `Version.xcconfig`
- `XKey.xcodeproj/project.pbxproj`