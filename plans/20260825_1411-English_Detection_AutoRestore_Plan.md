# Plan for English Word Detection & Auto-Restore

## Problem Statement
When the user types English words, they get Vietnamese marks and underlines which is annoying. The system detects "impossible Vietnamese clusters" (e.g., "micros" ending in "s", or starting with "str") but does not instantly restore the previous marks and remove the underline.

## Solution
1. **Enhance `ProcessResult`**: Add a new flag `isInstantRestore: Bool`.
2. **Update `convertHookStateToResult`**: Set `isInstantRestore = true` if `hookState.extCode == 5`.
3. **Trigger Restore in `VNEngine.swift`**:
   - When `isDefinitelyNotVietnamese` is true, trigger `checkRestoreIfWrongSpelling(handleCode: vRestore)` and set `hookState.extCode = 5`.
   - This effectively replaces the current marked Vietnamese sequence with the raw English keystrokes.
4. **Commit Composition in `XKeyIMController.swift`**:
   - When processing the engine's result, check if `result.isInstantRestore == true`.
   - If true, call `commitComposition(client)` immediately after handling the result.
   - This removes the underline from the typing buffer for English words, providing a native English typing experience.

## Testing Strategy
- Compile the project (`xcodebuild`).
- Ensure no regressions with `VNEngineTests`.
- Add documentation and update `README.md` as the single source of truth.
