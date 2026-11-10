# 10. Acute & Chronic Workload

The application employs the Exponentially Weighted Moving Average (EWMA) method to calculate acute and chronic training loads.

EWMA assigns exponentially higher weighting to more recent training sessions relative to earlier activities.

### 10.1. Acute Training Load

Acute Training Load represents short-term training fatigue and stress, modeled with a 7-day EWMA time-decay parameter.

Smoothing Factor:

```text
lambda_acute = 2 / (7 + 1) = 0.25
```

### 10.2. Chronic Training Load

Chronic Training Load represents long-term historical fitness and conditioning, modeled with a 28-day EWMA time-decay parameter.

Smoothing Factor:

```text
lambda_chronic = 2 / (28 + 1) = 2 / 29 ≈ 0.0689655...
```

### 10.3. EWMA Calculation

EWMA is updated recursively each day using the Daily Training Load:

```text
EWMA(t) = lambda * Load(t) + (1 - lambda) * EWMA(t-1)
```

Where:

- `Load(t)`: Daily Training Load on day `t`.
- `EWMA(t-1)`: Preceding day's EWMA value.
- `lambda`: Smoothing factor for either Acute or Chronic parameters.

Days without workout activity record a Daily Training Load of `0` in the recursive calculation.

### 10.4. Minimum Data Requirement & Bias Correction

The system requires a **minimum of 8 recorded calendar days of Daily Training Load history** before ACWR can be utilized to make Daily and Weekly Adjustment decisions. The timeline begins on the **first day of recorded Training Load (Day 1)**. Rest days without workouts are counted as calendar days with a Daily Training Load of `0`.

Rules governing EWMA calculation and Bias Correction:

| History Period | Calculation Method | ACWR Status for Decision-Making |
|---|---|---|
| **Days 1–7** | Acute and Chronic EWMA are tracked and stored without Bias Correction | **Unavailable for Daily/Weekly Decisions** |
| **Days 8–27** | Acute and Chronic EWMA are computed with **Bias Correction** | **Available for Daily/Weekly Decisions** |
| **Day 28 onwards** | Acute and Chronic EWMA are computed **without Bias Correction** | **Available for Daily/Weekly Decisions** |

The Bias Correction formula applied from **Day 8 through Day 27**:

```text
Corrected EWMA(t) = EWMA(t) / (1 - (1 - lambda)^t)
```

Where:

- `t`: Count of elapsed **calendar days** since the first recorded day (not the count of completed exercise sessions).
- `lambda`: Distinct smoothing factor for Acute (7-day) vs. Chronic (28-day).
- Bias Correction is applied **independently** to Acute and Chronic EWMA before computing their ratio.
- **Starting from Day 28, the system transitions to standard EWMA without Bias Correction** per product specifications.

**Example:** If a user completes four exercise sessions across eight calendar days from their first recorded session, ACWR becomes eligible for decision-making on **Day 8**. The user does not need to complete eight separate workout sessions to satisfy this threshold.

### 10.5. ACWR Calculation

The Acute:Chronic Workload Ratio (ACWR) compares the user's short-term training fatigue against long-term physical conditioning:

```text
If t < 8:
    ACWR = unavailable (not used for automated adjustments)

If 8 <= t < 28:
    ACWR = Corrected Acute EWMA / Corrected Chronic EWMA

If t >= 28:
    ACWR = Acute EWMA / Chronic EWMA
```

When available, ACWR serves as an essential governing parameter in determining daily and weekly training plan adjustments.

During **Days 1–7**, the system continuously logs Daily Training Load and updates EWMA series, but bypasses ACWR-based decision branches. The training plan retains the **onboarding baseline** until data criteria are satisfied. If the final session of the current week concludes before Day 8, the subsequent week's plan is generated using that baseline.

If the Chronic EWMA denominator cannot yield a mathematically valid ratio, ACWR remains marked as unavailable; the system never forces a synthetic fallback ratio.

### 10.6. Calculation Example

Sample Training Load sequence:

| Date | Daily Training Load |
|---|---:|
| September 29, 2026 | 101 |
| September 30, 2026 | 0 |
| October 1, 2026 | 0 |
| October 2, 2026 | 0 |
| October 3, 2026 | 87 |
| October 4, 2026 | 23 |
| October 5, 2026 | 0 |
| October 6, 2026 | 0 |

Because October 6 marks the **8th calendar day** since September 29, this date is the earliest point where ACWR is eligible for decision-making. Computing EWMA with Bias Correction on Day 8 yields:

- Corrected Acute EWMA: 17.5363.
- Corrected Chronic EWMA: 23.9795.
- ACWR: approximately **0.73**.

This calculation evaluates **Corrected Acute** divided by **Corrected Chronic** because the historical window is in the 8–27 day range. Had this been Day 7, ACWR would remain excluded from automated decision logic.
