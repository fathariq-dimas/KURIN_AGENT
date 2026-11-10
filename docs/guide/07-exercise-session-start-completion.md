# 7. Exercise Session Start & Completion

Exercise sessions are executed based on the prescribed duration defined in the Training Plan.

When the target session duration is reached, the system delivers an audio notification.

The system supports pre-exercise readiness checks as well as multiple session completion pathways.

### 7.1. Pre-Exercise Complaint Check

When the user selects a scheduled workout on the Home screen and taps **Start Session**, the system displays a prompt asking whether the user is experiencing any physical complaints, such as an injury.

- **YES:** The session is immediately cancelled without proceeding to Warm-up, and the session status is recorded as **`Rest (Injury/Complaint)`**.
- **NO:** The session proceeds to Warm-up, followed by Actual Training and Cool-down according to the assigned Training Type.

The `Rest (Injury/Complaint)` status records an uncompleted session and serves as diagnostic causal data during the weekly frequency evaluation (Section 13.3).

### 7.2. Automatic Completion

A session can complete automatically once the entire planned workout duration has elapsed.

### 7.3. Manual Completion

The user can manually stop and complete a session at any point.

### 7.4. Continue Exercise

When the planned workout duration is reached, the user may choose to continue exercising if they wish to extend their activity.

### 7.5. Post-Exercise Summary

After the session concludes, the system **first computes a default RPE estimate based on Heart Rate Reserve (%HRR)**, displays an RPE confirmation popup for the user to confirm or adjust, and then calculates **Training Load = final RPE × actual duration (in minutes)**.

**The Exercise Summary screen is displayed only after the final RPE and Training Load have been established.** The summary includes workout statistics, the current session's Training Load, and historical Training Load logs from previous sessions.
