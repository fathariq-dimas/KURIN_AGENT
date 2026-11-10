# 4. Weekly Training Plan

The Weekly Training Plan is a training schedule structured for one week based on the user's capabilities and workout performance.

The plan encompasses training type, weekly frequency, duration per session, and workout schedule. The initial plan is created based on user selections during onboarding, and subsequently updated based on training evaluation results from the current week.

### 4.1. Training Frequency

For the **HR Zone 2 Activity** track, the application provides three weekly frequency options:

- 3 times per week.
- 4 times per week.
- 5 times per week.

For the **Easy Walking** track, the initial frequency is set to **3 times per week**. Users select their workout days during onboarding. Frequency can be adjusted through the weekly evaluation process.

### 4.2. Training Duration

Each session is governed by the following duration rules:

- Minimum workout duration is **15 minutes per session**.
- Maximum total workout duration is **300 minutes per week**.
- Maximum duration per session is determined by weekly training frequency.
- During onboarding, **HR Zone 2 Activity** users select a session duration in the range of **15 to (300 / frequency) minutes**.
- During onboarding, the initial duration for **Easy Walking** users is fixed at **15 minutes per session**.

| Frequency | Maximum Duration per Session | Maximum Weekly Total |
|---|---:|---:|
| 3 times | 100 minutes | 300 minutes |
| 4 times | 75 minutes | 300 minutes |
| 5 times | 60 minutes | 300 minutes |

The 300-minute limit is a planning ceiling, not a mandatory quota that users must achieve.

### 4.3. Training Plan Adjustment

Training Plan adjustments are categorized into three primary decisions:

- **Increase:** Increase session duration or adjust training frequency upward.
- **Maintain:** Keep current duration and frequency unchanged.
- **Decrease:** Reduce session duration or adjust training frequency downward.

In addition to these three decisions, the system includes a **Rest** decision, which prescribes rest days based on training evaluation metrics.

### 4.4. Weekly Calendar and Plan Generation

The system uses **Monday as the first day of the week**, spanning **Monday through Sunday**. Scheduled workout days follow the user's onboarding selections, meaning the first workout day does not have to fall on Monday.

There are two plan generation mechanisms:

1. **Initial Training Plan (Onboarding):** Created from the onboarding baseline for the user's first training week, which may be a partial or full week.
2. **Recurring Weekly Training Plan:** Once the final scheduled session of the current week is completed, the system runs the Weekly Adjustment and generates the Training Plan for the subsequent calendar week.

### 4.5. Start Day Selection During Onboarding

During onboarding, the user selects their weekly workout days and chooses which day to begin the program from among those selected days.

Start date determination follows these rules:

1. **If the chosen start day is the same day of the week as onboarding**, the system presents two options: **Start today** or **Start on the same day next week**.
2. **If the chosen start day is different from the onboarding day**, the system automatically uses the **next nearest occurrence** of that chosen day. There is no additional prompt to defer another week.
3. The system never selects past dates. If the chosen day is only available in the following calendar week, that date is selected automatically.
4. If the determined start date falls in **the next week**, the Initial Training Plan is generated for the **entire next week** (Monday–Sunday), including all user-selected workout days for that week, even if some selected days precede the chosen start date.
5. If the user begins during the **current week**, the Initial Training Plan only includes selected workout days remaining between the start date and the end of the week.

**Example: Onboarding takes place on Thursday; selected schedule is Monday, Thursday, and Saturday.**

| Selected Start Day | Selection Mechanism | Initial Training Plan Coverage |
|---|---|---|
| Thursday, **Today** option | User chooses to start Thursday during onboarding | Thursday and Saturday of this week |
| Thursday, **Next Week** option | User chooses Thursday next week | Monday, Thursday, and Saturday of next week |
| Saturday | Automatically nearest Saturday (this week) | Saturday of this week |
| Monday | Automatically nearest Monday (next week) | Monday, Thursday, and Saturday of next week |

**Note:** When selecting "Next Week", the chosen day serves as an anchor for the **starting week of the program**, not as a cutoff that discards earlier workout days within that same week. For example, selecting Thursday next week still yields a plan that includes Monday next week if Monday is part of the chosen schedule.

### 4.6. Initial Plan Coverage and Examples

**A. Starting in the current week (partial week)**

- Onboarding takes place on Thursday.
- The user selects workout days **Monday, Thursday, Saturday** and chooses to start **Today (Thursday)**.
- Monday's session has already passed, so this week's Initial Training Plan only includes **Thursday and Saturday**.
- The Initial Training Plan applies the onboarding baseline.
- After the final **Saturday** session is completed, the system evaluates the partial week (Planned Frequency = 2 sessions) and generates the Training Plan for **Monday, Thursday, and Saturday of the following week**.

**B. Starting on another upcoming day in the same week**

- Onboarding takes place on Thursday.
- The user selects workout days **Monday, Thursday, Saturday** and selects **Saturday** as the start day.
- Because Saturday is not the onboarding day, there is no choice between this week or next week: the system assigns the **nearest Saturday** (this week).
- The Initial Training Plan contains only **Saturday** for this partial week.

**C. Starting in the next calendar week (full week)**

- Onboarding takes place on Thursday.
- The user selects workout days **Monday, Thursday, Saturday** and selects **Monday** as the start day.
- The system assigns the **nearest Monday**, which is Monday next week.
- The Initial Training Plan covers **Monday, Thursday, and Saturday** of next week.

**D. Choosing the onboarding day, but starting next week**

- Onboarding takes place on Thursday.
- The user selects workout days **Tuesday, Thursday, Saturday**, then chooses **Thursday next week**.
- The Initial Training Plan covers **Tuesday, Thursday, and Saturday** of next week, not just Thursday and Saturday.

For evaluating the first week's frequency, **Planned Frequency** always reflects the exact count of sessions scheduled in the Initial Training Plan (e.g., 1 or 2 sessions in a partial week), rather than the full nominal frequency chosen during onboarding.

### 4.7. Recurring Weekly Generation Trigger

After the onboarding phase, subsequent Training Plans are generated **immediately upon completion of the last planned session of the current week**.

The flow:

1. The user executes scheduled workout sessions during the current week.
2. At the end of the final session for that week, the system calculates workout metrics and updates training load.
3. The system checks the total count of recorded calendar days since Training Load tracking began.
4. If history has reached **at least 8 days**, the system executes the Weekly Evaluation using **ACWR**, comparison of **Planned Frequency vs. Actual Frequency**, **reasons for missed sessions**, and **Weekly Average RPE from that week's sessions**. ACWR calculation utilizes Bias Correction from days 8 through 27, and standard EWMA without Bias Correction starting on day 28.
5. The evaluation outcome determines an **Increase**, **Maintain**, **Decrease**, or **Rest** decision. If the decision requires a duration adjustment, the scaling magnitude follows the **RPE Scaling table (Section 11.1)** using **Weekly Average RPE** as the input, not the last session's RPE.
6. If history is **only 1–7 days**, ACWR-based decisions are not yet executed. The plan for the following week is generated using the onboarding baseline without automated ACWR adjustments.
7. The system creates the Training Plan for the next calendar week based on the evaluation result or the prevailing baseline.

**Example:** If sessions are scheduled for Monday, Thursday, and Saturday, generating next week's plan occurs **right after Saturday's session ends**, not on Sunday or the following Monday.
