# 11. RPE-Based Duration Scaling

Rate of Perceived Exertion (RPE) is used as the scaling parameter to determine the magnitude of workout duration adjustments.

Lower RPE values unlock potential duration increases, whereas higher RPE values trigger duration decreases. **The source of the RPE value differs between Daily and Weekly Adjustments**: Daily Adjustment uses the RPE of the session just completed, while Weekly Adjustment uses the average RPE across all sessions during the week being evaluated.

### 11.1. Duration Scaling Table

| RPE | Duration Adjustment | Category |
|---|---:|---|
| 1 | +20% | Increase |
| 2 | +17.5% | Increase |
| 3 | +15% | Increase |
| 4 | +10% | Increase |
| 5 | 0% | Maintain |
| 6 | -10% | Decrease |
| 7 | -12.5% | Decrease |
| 8 | -15% | Decrease |
| 9 | -17.5% | Decrease |
| 10 | -20% | Decrease |

Under EWMA ACWR conditions, weekly RPE scaling factor (`alpha`) is defined as:
- ACWR < 0.8:
```text
alpha = abs(avg(RPE last 7 days))
```
- ACWR > 1.3:
```text
alpha = -abs(avg(RPE last 7 days))
```

### 11.2. Duration Adjustment Formula

```text
Duration(t) = Duration(t-1) * (1 + alpha)
```

Where:

- `Duration(t)`: Adjusted workout duration.
- `Duration(t-1)`: Baseline or prior workout duration.
- `alpha`: Adjustment percentage derived from the RPE scaling table.

The percentage in the table specifies the magnitude of change whenever the system prescribes a duration adjustment.

All adjusted durations remain bounded by the minimum (15 min) and maximum duration limits defined in the Training Plan.

### 11.3. RPE Source by Adjustment Period

| Adjustment Level | RPE Value Used for Scaling | When Applied |
|---|---|---|
| **Daily Adjustment** | **Session RPE**: RPE from the session just completed (manual or automated) | When Daily Decision determines a Decrease for the subsequent session |
| **Weekly Adjustment** | **Weekly Average RPE**: Mean RPE across all sessions with recorded RPE in the evaluated week | When Weekly Decision determines an Increase or Decrease for the upcoming week's plan |

Weekly Average RPE formula:

```text
Weekly Average RPE = sum(RPE of sessions in evaluated week) / count(sessions with RPE in that week)
```

The RPE values contributing to the average can originate from manual confirmations or automated estimates. The calculation spans the calendar week under evaluation, including partial initial weeks.

**Example:** If session RPEs on Monday, Thursday, and Saturday are 3, 4, and 5, then Weekly Average RPE = `(3 + 4 + 5) / 3 = 4`. When the weekly decision outcome is **Increase**, the table in Section 11.1 yields a scaling factor of **+10%** for RPE 4.

**Separation of concerns:** In Weekly Adjustment, the combination of **ACWR + Planned vs. Actual Frequency + Causes** determines whether the directional outcome is Increase, Maintain, Decrease, or Rest. **Weekly Average RPE does not override this directional decision flow**; its value is exclusively used to look up the **adjustment percentage** from the RPE Scaling table once an Increase or Decrease direction has been established.
