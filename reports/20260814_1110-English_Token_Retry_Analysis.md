# English Token Retry Analysis

**Date:** 2026-08-14 11:10  
**Status:** Analysis complete; no production code changed

## Finding

`adsense` bypasses the existing real-time English detector because its `ad` prefix is not impossible in Vietnamese. The detector deliberately relies on impossible prefixes to avoid interrupting valid Vietnamese input. The `s` key can therefore reach Telex tone processing and change the token before the engine has enough evidence to classify it as English.

## Data-driven direction

Track only correction events for composed English candidates, using a stable local hash rather than plaintext. Aggregate the local hashes to identify frequently retried tokens and prioritize their handling.

## Required regression coverage

- `adsense` remains literal in Telex.
- `dd` remains `đ`.
- `lys` remains `lý`.

## Related files

- `XKey/Core/Engine/VNEngine.swift`
- `XKey/Core/Engine/VNEngineEnglishDetection.swift`
- `XKeyTests/VNEngineTests.swift`