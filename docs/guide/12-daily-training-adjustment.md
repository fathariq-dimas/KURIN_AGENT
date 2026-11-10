# 12. Daily Training Adjustment

Daily Training Adjustment is an evaluation process executed immediately after an exercise session is completed.

The system evaluates Training Load, ACWR, and the **Session RPE of the session just completed** to determine adjustments for the subsequent workout session. Daily Adjustment strictly uses the session's own RPE, **not the weekly average RPE**.

### 12.1. Daily Evaluation Flow

1. The user completes an exercise session.
2. The system obtains the **Session RPE for that specific session** via manual user confirmation or automated estimation.
3. The system computes the session's Training Load.
4. Daily Training Load is updated.
5. Acute and Chronic EWMA values are updated; the system verifies the count of calendar days since the first recorded session.
6. **Days 1–7:** ACWR is not yet active; automated ACWR-based decisions are bypassed and the existing plan baseline is preserved.
7. **Days 8–27:** The system calculates ACWR as **Corrected Acute EWMA / Corrected Chronic EWMA**.
8. **Day 28 onwards:** The system calculates ACWR as **Acute EWMA / Chronic EWMA** without Bias Correction.
9. When ACWR is available, the latest ACWR and **just-completed Session RPE** determine the Daily Decision.
10. If a Decrease is prescribed, the reduction magnitude follows the scaling percentage for that **Session RPE**. The adjustment is applied to the next upcoming workout session.

### 12.2. Daily Decision Rules

The rules below take effect once training history reaches **at least 8 calendar days** so that ACWR is available. Prior to Day 8, ACWR-based Daily decisions are bypassed.

**ACWR > 1.3**

The system prescribes a **Rest** decision to allow adequate physiological recovery before the next workout.

**ACWR <= 1.3 with RPE < 6**

The system prescribes a **Maintain** decision.

**ACWR <= 1.3 with RPE 6–10**

The system prescribes a **Decrease** decision, reducing the subsequent session's duration according to the RPE Scaling percentage for the **session RPE just completed**.

### 12.3. Daily Adjustment Output

Daily evaluation outcomes can result in:

- **Maintain:** The next session duration is preserved as scheduled.
- **Decrease:** The next session duration is scaled down based on the Session RPE.
- **Rest:** The user is prescribed rest, adjusting upcoming session scheduling.

These adjustments enable the app to rapidly respond to acute fatigue and prevent overtraining.
