# 13. Weekly Training Adjustment

Weekly Training Adjustment is an evaluation process executed **immediately after the final scheduled exercise session of the current week is completed** to formulate the training plan for the subsequent week.

The evaluation synthesizes ACWR, training frequency adherence, and reasons for uncompleted sessions. **Once an Increase or Decrease decision direction is determined, Weekly Average RPE is used to specify the exact duration adjustment percentage.**

### 13.1. Weekly Evaluation Parameters

Parameters utilized:

- **Latest ACWR, if history reaches at least 8 calendar days** (Bias Correction applied on days 8–27; uncorrected starting on day 28).
- Planned Training Frequency.
- Actual Training Frequency.
- Reasons for sessions not completed as planned.
- **Weekly Average RPE**, calculated from all recorded session RPEs during the evaluated week (manual confirmations or automated estimates).

```text
Weekly Average RPE = sum(Session RPE during the week) / count(sessions with RPE during the week)
```

For an Initial Training Plan commencing mid-week, Planned Training Frequency refers to the exact count of sessions **actually scheduled within that partial week**.

### 13.2. ACWR Categories

| ACWR Range | Evaluation Category |
|---|---|
| Less than 0.8 | Low Workload Ratio |
| 0.8 to 1.3 | Moderate Workload Ratio |
| Greater than 1.3 | High Workload Ratio |

### 13.3. Training Frequency Evaluation

The system compares the count of scheduled sessions against completed sessions.

If all planned sessions were completed, evaluation proceeds directly according to the ACWR category.

If any scheduled session was missed or uncompleted, the system examines the documented cause:

- **Rest / Overtrained:** The user required recovery time due to fatigue.
- **Skipped:** The user skipped the workout.
- **Injuries:** The user experienced an injury.

Sessions cancelled during the Pre-Exercise Complaint Check hold the status **`Rest (Injury/Complaint)`** and are excluded from completed sessions in Actual Frequency. If the reported cause is an injury, this status informs the **Injuries** branch. Mapping for non-injury complaints within existing cause categories remains unspecified in the current product flow.

### 13.4. Weekly Decision Rules

The following Decision Rules take effect **starting on Day 8 from the first recorded Training Load**, once ACWR is active. For weeks concluding before Day 8, the system generates the next Training Plan based on the onboarding baseline without executing ACWR-based decisions.

**A. ACWR < 0.8**

If training frequency was met:
- Prescribe **Increase**.

If any planned sessions were not met:
- Rest due to fatigue -> **Maintain**.
- Skipped -> **Maintain**.
- Injuries -> **Rest, then Decrease**.

**B. ACWR 0.8 to 1.3**

If training frequency was met:
- Prescribe **Maintain**.

If any planned sessions were not met:
- Rest due to fatigue -> **Maintain**.
- Skipped -> **Maintain**.
- Injuries -> **Rest, then Decrease**.

**C. ACWR > 1.3**

Prescribe **Rest**, and adjust the next Training Plan through **Decrease**.

This decision is enforced taking into account frequency completion status and the underlying non-completion causes.

### 13.5. Weekly Adjustment Output and RPE Scaling

Evaluation yields a Training Plan recommendation for the subsequent week. The **decision direction** is governed by the ACWR + Planned/Actual Frequency + Causes flow (once minimum 8-day history is present), while the **duration adjustment percentage** for Increase or Decrease decisions is derived from **Weekly Average RPE** mapped to the **RPE Scaling table in Section 11.1**, rather than using a single final session's RPE.

Consequently, the RPE Scaling table **remains identical** for Daily and Weekly adjustments; only the **source of the RPE input** differs: Daily = latest completed session, Weekly = mean across all sessions during the evaluated week.

**Increase Example:**

- Session RPEs during the week: 3, 4, and 5.
- Weekly Average RPE = 4.
- Weekly Decision = Increase.
- Scaling percentage for RPE 4 per table = **+10%**.

**Decrease Example:**

- Session RPEs during the week: 6, 7, and 8.
- Weekly Average RPE = 7.
- Weekly Decision = Decrease.
- Scaling percentage for RPE 7 per table = **-12.5%**.

Weekly average RPE may yield a decimal value; handling fractional averages against discrete lookup tiers is not formally defined in current specifications. No arbitrary interpolation or rounding rules are introduced here.

Possible recommendations:

- **Increase.**
- **Maintain.**
- **Decrease.**
- **Rest** prior to resuming the Training Plan.

Adjustments may apply to duration and frequency while respecting the established training type constraints and weekly duration caps.

### 13.6. Weekly Plan Generation Timing

Weekly Adjustment and subsequent plan creation occur **immediately after the final scheduled session of that week ends**, rather than automatically at Sunday midnight. **ACWR availability is a prerequisite for executing ACWR-driven decisions, but not a prerequisite for generating next week's plan**; when history is only 1–7 days, the next plan utilizes the onboarding baseline.

Schedule progression example:

| Workout Day | Position in Week | Next Plan Generation |
|---|---|---|
| Monday | First session | Not yet |
| Thursday | Second session | Not yet |
| Saturday | Last planned session | **Yes, immediately upon completion** |

The generated plan targets the upcoming calendar week (Monday–Sunday) with workout days aligned with the user's selected or adjusted schedule.

**Unspecified edge case:** The trigger mechanism when the **final scheduled session of the week is cancelled** via the Pre-Exercise Complaint Check (`Rest (Injury/Complaint)`) has not yet been formally harmonized with standard session-completion events in the product specification.
