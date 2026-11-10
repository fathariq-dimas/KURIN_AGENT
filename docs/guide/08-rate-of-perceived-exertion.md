# 8. Rate of Perceived Exertion (RPE)

Rate of Perceived Exertion (RPE) reflects the level of exertion and subjective intensity experienced by the user during exercise.

RPE serves as one of the primary parameters for session evaluation and adaptive training plan adjustments.

The system incorporates two complementary mechanisms to establish the session RPE.

### 8.1. Manual RPE

After a session ends and the automated RPE estimation is calculated, the system displays an RPE confirmation popup with the **estimated value pre-populated as default**.

The user can **keep the default estimated value** or **manually adjust it** based on their perceived workout difficulty using a 1–10 Borg CR10 scale.

The finalized RPE value confirmed or adjusted by the user becomes the session's definitive RPE used for Training Load calculation.

### 8.2. Automatic RPE Estimation

The system computes an **automated RPE estimate immediately after session completion** using Heart Rate Reserve (HRR). This estimated value serves as the **default value in the RPE confirmation popup** before the user confirms or edits it.

Input parameters:

- Average Heart Rate (`HRavg`).
- Resting Heart Rate (`HRrest`).
- Maximum Heart Rate (`HRmax`).

### 8.3. Maximum Heart Rate

Maximum Heart Rate is calculated using the Tanaka formula:

```text
HRmax = 208 - (0.7 * Age)
```

### 8.4. Heart Rate Reserve

Heart Rate Reserve (HRR) represents the reserve capacity between Maximum Heart Rate and Resting Heart Rate:

```text
HRR = HRmax - HRrest
```

The exercise intensity percentage (%HRR) is calculated using Average Heart Rate:

```text
%HRR = ((HRavg - HRrest) / (HRmax - HRrest)) * 100
```

### 8.5. RPE Interpolation

The user's %HRR determines their position within defined physiological intensity brackets.

The system then applies linear interpolation to compute the estimated RPE.

Variables:

- `P`: User's %HRR.
- `P1`: Lower %HRR bound of the bracket.
- `P2`: Upper %HRR bound of the bracket.
- `R1`: RPE corresponding to the lower bound.
- `R2`: RPE corresponding to the upper bound.

Formula:

```text
RPEfinal = R1 + ((P - P1) / (P2 - P1)) * (R2 - R1)
```

### 8.6. HRR-to-RPE Mapping

| Final RPE | %HRR Range |
|---|---|
| 1 | 28% to < 42% |
| 2 | 42% to < 53.2% |
| 3 | 53.2% to < 70% |
| 4 | 70% to < 76.5% |
| 5 | 76.5% to < 83% |
| 6 | 83% to < 87.25% |
| 7 | 87.25% to < 91.5% |
| 8 | 91.5% to < 95.75% |
| 9 | 95.75% to < 100% |
| 10 | Exactly 100% |

The resulting RPE value—whether from manual confirmation or automated estimation—is utilized to compute Training Load and serves as a governing parameter in daily and weekly training adjustments.
