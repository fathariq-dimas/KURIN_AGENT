# Rule: iOS & watchOS Platform Standards

**Applies to:** All code in `Kurin/` and `KurinWatch/`  
**Mandatory:** Yes — no exceptions without explicit team approval.

---

## iOS 27 / iPhone 17

- Use **SwiftUI** for all UI. No UIKit unless wrapping a system API unavailable in SwiftUI.
- Minimum deployment target: **iOS 27.0**. Do not use `#available` guards for anything introduced before iOS 27.
- Support **Dynamic Type** — use `.font(.body)`, `.font(.headline)`, etc.; never hard-code font sizes.
- Support **Dark Mode** — use semantic colors from the asset catalog, not hex literals.
- All interactive controls must have `.accessibilityLabel` for VoiceOver.

## watchOS 11 / Apple Watch Series 11

- Minimum deployment target: **watchOS 11.0**.
- Use **WKExtendedRuntimeSession** for uninterrupted background session tracking — do not rely on foreground-only timers.
- Use **WKHapticType** for haptic feedback at session phase transitions (`.notification`, `.success`, `.stop`).
- Use **AVSpeechSynthesizer** for audio guidance cues (warm-up start, phase change, cool-down start, session end).
- Watch UI must show only the most essential information: phase name, elapsed time, HR, and the primary action button.
- Tap targets on watch: minimum 44×44 pt.
- Always test on the smallest watch canvas (41 mm).

## HealthKit

- Declare only the permissions the current feature needs in `Info.plist` (NSHealthShareUsageDescription).
- Handle `.notDetermined`, `.denied`, `.restricted` gracefully — permission denial is not a crash condition.
- Use `HKWorkoutSession` + `HKLiveWorkoutBuilder` on watchOS for active workout HR streaming.
- Tag every HealthKit-sourced value with provenance metadata (`HEALTH` vs `MANUAL`).
- Never store raw health data to iCloud or any remote server.

