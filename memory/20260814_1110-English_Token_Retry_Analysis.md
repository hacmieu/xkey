# English Token Retry Analysis

**Date:** 2026-08-14 11:10  
**Status:** Root cause identified; implementation pending design approval

## User symptom

English tokens such as `adsense` are uncomfortable to type while Vietnamese Telex is enabled because a character inside the token may be interpreted as a Vietnamese modifier.

## Root cause

The real-time English detector in `VNEngine` intentionally checks only impossible *starting* patterns. It catches `street`, `chrome`, `fast`, and similar tokens early, but `adsense` starts with `ad`, which is not an impossible Vietnamese prefix.

When the user reaches `s`, the Telex path can interpret it as a sắc-tone key and change `a` to `á`. The broader English classifier knows about invalid endings and inner patterns, but it is not wired into this per-keystroke decision because applying it eagerly could break incomplete Vietnamese words.

## Log-driven optimization proposal

Do not store all typed content. Emit one local structured event only when an English candidate is affected by Vietnamese composition and is then corrected:

```text
[TELEMETRY] english-retry tokenHash=<stable-local-hash> length=7 trigger=s action=tone-applied correction=backspace
```

The analyzer can aggregate `tokenHash` locally and report only candidates whose correction count passes a threshold. This identifies repeated pain points without writing readable user text to disk.

## Validation target

Add a failing engine test first: typing `adsense` in Telex must remain `adsense`. The test must run alongside Vietnamese regression cases such as `dd` -> `đ` and `lys` -> `lý`.

## Design constraint

The solution must defer conversion only when evidence is strong enough that the token is English, preserving ordinary incomplete Vietnamese input.