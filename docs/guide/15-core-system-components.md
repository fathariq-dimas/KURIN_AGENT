# 15. Core System Components

Based on the overall architecture and operational flow, the application consists of the following primary functional components:

| Component | Responsibility |
|---|---|
| User Assessment | Determines the Training Type based on Health Screening results and BMI, establishing initial frequency and duration limits |
| Health Screening & Profile Setup | Records diagnoses, active symptoms, and injuries; presents Health Disclaimer when required; requests Apple Health permissions; provides default and manual profile pathways |
| Weight Goal Setup | Captures target body weight, provides recommended ranges based on normal BMI, and displays the Weight Goal Overview |
| Onboarding Schedule Setup | Records selected training days, handles start day selection, and manages today vs. next week logic when the start day matches the onboarding day |
| Initial Training Plan Generator | Synthesizes the baseline plan for a partial or full week based on onboarding choices |
| Weekly Training Plan Generator | Creates the subsequent week's plan immediately after the last scheduled session of the current week concludes |
| Training Calendar | Renders the schedule and completion status of workout sessions across Monday–Sunday calendar boundaries |
| Exercise Session Manager | Orchestrates workout phases, durations, countdowns, and completion states |
| Pre-Exercise Complaint Check | Screens for physical complaints before a session starts, cancels the session upon a YES response, and records `Rest (Injury/Complaint)` for weekly frequency evaluation |
| Exercise Guidance Engine | Delivers real-time synchronized audio prompts and haptic feedback |
| Heart Rate Monitoring | Monitors real-time heart rate and HR zones throughout the workout |
| RPE Evaluation | Computes automated HRR-based RPE estimates as defaults, receives manual confirmations/edits, and calculates Weekly Average RPE for weekly scaling |
| Training Load Calculator | Computes Session and Daily Training Load once the final RPE is established |
| Exercise Summary | Presents post-workout statistics, current session load, and historical Training Load logs after RPE and load computations finalize |
| EWMA Calculator | Computes Acute (7-day) and Chronic (28-day) EWMA; applies Bias Correction on days 8–27 and transitions to uncorrected EWMA from Day 28 onward |
| ACWR Calculator | Computes the Acute:Chronic Workload Ratio starting on Day 8, applying phase-specific EWMA formulations |
| Daily Adjustment Engine | Adjusts the upcoming workout session using ACWR and the Session RPE of the session just finished |
| Weekly Adjustment Engine | Evaluates ACWR, frequency adherence, and non-completion causes, then applies Weekly Average RPE to determine duration scaling magnitude |
