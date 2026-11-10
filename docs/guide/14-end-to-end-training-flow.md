# 14. End-to-End Training Flow

The complete end-to-end operational lifecycle of the application—from initial plan formulation to workout guidance, physiological load computation, and continuous adaptive adjustments—is organized into 10 structured phases:

### Phase 1 — Initial Training Plan Generation

During onboarding, the user first completes Health Screening, reviews the Health Disclaimer if triggered, grants Apple Health permissions or inputs their profile manually, establishes a weight goal, and reviews the Weight Goal Overview. The **Final Assessment** assigns the user to either **HR Zone 2 Activity** (no screening issues and BMI < 30) or **Easy Walking** (failure to meet one or both criteria; baseline: 3 sessions/week × 15 min).

The user selects training days, available frequency/duration values based on their assigned track, and a start date. Easy Walking frequency and duration remain fixed while its weekdays are selectable. The system synthesizes the Initial Training Plan based on user characteristics, screening considerations, and onboarding baselines. A separate name-entry step and workout-reminder time are omitted by the approved Feature 001 flow (user decision 2026-11-10).

- **Start day matches onboarding day:** The user can select **Today** or **Same day next week**.
- **Start day differs from onboarding day:** The system automatically uses the **next nearest calendar date** of that chosen day without additional prompts.
- **Start date falls in the current week:** The plan covers the remaining selected workout days available in that week.
- **Start date falls in the upcoming week:** The plan covers all selected workout days throughout that entire week, including days preceding the anchor date.

The system enforces Monday through Sunday as the standard calendar week boundaries.

### Phase 2 — Exercise Preparation

The user selects a scheduled exercise session from the Home screen.

The system displays the Training Type, total duration, and phase breakdown. When the user taps **Start Session**, the system presents the **Pre-Exercise Complaint Check**.

- **YES (complaint or injury present):** The session is immediately cancelled and logged with the status `Rest (Injury/Complaint)`; it does not advance to Warm-up.
- **NO:** The user advances directly to the Warm-up phase.

### Phase 3 — Exercise Execution

The user initiates the workout and progresses sequentially through three distinct phases:

1. Warm-up.
2. Actual Training.
3. Cool-down.

The system delivers synchronized audio cues and haptic feedback tailored to the current phase and assigned Training Type.

For HR Zone 2 Activity, the system provides real-time adaptive pacing guidance (Speed Up, Maintain, Slow Down, Take Your Time) driven by continuous heart rate monitoring.

### Phase 4 — Exercise Completion

Upon reaching the target session duration, the system delivers completion audio guidance.

A session can conclude automatically upon duration expiry, end manually via user action, or continue if the user chooses to extend their workout.

### Phase 5 — RPE Estimation & Confirmation

Immediately upon session termination, the system computes an automated HRR-based RPE estimate and displays it pre-populated in the RPE confirmation popup. The user may accept the default estimate or adjust it manually on a 1–10 scale.

### Phase 6 — Training Load Calculation & Exercise Summary

The system computes the session's Training Load as **final RPE × actual session duration (minutes)**.

This value is accumulated into the Daily Training Load. **Only after these calculations conclude** does the app display the Exercise Summary, detailing the current session's metrics, session Training Load, and historical Training Load logs.

### Phase 7 — Workload Evaluation

The system continuously updates Acute (7-day) and Chronic (28-day) EWMA series using Daily Training Load. ACWR eligibility requires a **minimum of 8 calendar days** of recorded history:

- **Days 1–7:** ACWR is marked unavailable for automated decision-making.
- **Days 8–27:** ACWR is computed using Bias-Corrected EWMA.
- **Day 28 onwards:** ACWR is computed using standard uncorrected EWMA.

### Phase 8 — Daily Adjustment

When ACWR is available, the system determines a Maintain, Decrease, or Rest outcome based on ACWR and the **just-completed Session RPE**.

The decision directly governs adjustments to the next scheduled exercise session.

### Phase 9 — Weekly Adjustment

If the completed session represents the **final scheduled workout of the current calendar week**, the system executes the Weekly Adjustment.

If history has reached at least 8 calendar days, the system evaluates ACWR, frequency adherence, and non-completion causes to arrive at an Increase, Maintain, Decrease, or Rest decision. For Increase or Decrease decisions, the magnitude of duration scaling follows the **RPE Scaling table using the Weekly Average RPE**. If data spans only 1–7 days, ACWR decisions are bypassed and the upcoming plan retains the onboarding baseline.

### Phase 10 — Next Weekly Training Plan

**Immediately at the conclusion of the week's final scheduled session**, the system synthesizes the Training Plan for the **subsequent calendar week** based on the Weekly Adjustment result.

The new plan encompasses scheduled workout days across the entire calendar week. This closed-loop cycle repeats week after week, ensuring continuous adaptation to the user's evolving physical fitness.
