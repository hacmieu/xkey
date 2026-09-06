# English Detection & Auto-Restore Implementation Report

## Changes Made
1. **`VNEngine.swift`**:
   - Updated `ProcessResult` struct to include `isInstantRestore: Bool`.
   - Updated `convertHookStateToResult` to map `hookState.extCode == 5` to `isInstantRestore = true`.
   - Modified `handleMainKey` so that when `isDefinitelyNotVietnamese` is triggered, it calls `checkRestoreIfWrongSpelling(handleCode: vRestore)` and sets `hookState.extCode = 5`. This forces the engine to rollback any Vietnamese marks and return the raw english characters typed so far.
2. **`XKeyIMController.swift`**:
   - Modified the `handleKeyEvent` (specifically around line 894 where the engine result is handled).
   - Added a check: `if result.isInstantRestore { commitComposition(client) }`.
   - By immediately committing the composition when an instant restore is detected, the marked text underline vanishes for English words.

## Result
Typing an impossible Vietnamese cluster (e.g. typing 'e' after 'ád' in 'ads') will instantly revert the previous marks to 'ads' and commit the composition. The user will not experience annoyances with the Vietnamese underline sticking around for English words.
