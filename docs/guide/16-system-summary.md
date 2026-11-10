# 16. System Summary

The system implements an adaptive training methodology through an integrated loop of weekly planning, real-time guidance, and post-exercise performance evaluation.

**Onboarding and Final Assessment** encompass Health Screening, Apple Health permission or manual profile entry, weight goal setting, and Training Type assignment based on health conditions and BMI. The HR Zone 2 Activity track provides options for 3–5 sessions per week within weekly duration caps, while the Easy Walking baseline is fixed at 3 sessions per week at 15 minutes per session.

**Initial Training Plan** is generated from onboarding outcomes and the chosen start timing. The option to choose **Today or the same day next week** is displayed only when the chosen start day matches the onboarding day; other days automatically select the next nearest occurrence. Users starting mid-week receive a partial plan for the remaining scheduled days, while users choosing the upcoming week receive a complete plan for all scheduled workout days in that week.

**Weekly Training Plan** defines the scheduled exercise activities across a calendar week, with Monday marking the start of the week and sessions scheduled according to user selections.

**Pre-Exercise Complaint Check** executes before any session begins. If the user reports complaints or injuries, the session is cancelled immediately and recorded as `Rest (Injury/Complaint)`; otherwise, the session proceeds to Warm-up.

**Exercise Guidance** helps users maintain their target duration and physiological intensity during exercise through audio prompts and haptics.

**RPE** quantifies subjective exercise difficulty and is used to compute Training Load and govern duration scaling percentages. Post-session, an automated HRR-based RPE estimate is presented as a default that the user can confirm or edit; once finalized, the system computes Training Load and renders the Exercise Summary.

**Training Load and ACWR-EWMA** evaluate progressive workload trends over time. **ACWR is active starting on Day 8** from the first recorded workout: Days 1–7 are excluded from automated decisions, Days 8–27 apply EWMA with Bias Correction, and Day 28 onwards applies standard EWMA without Bias Correction.

**Daily Adjustment** prescribes session adjustments based on ACWR (when available) and the **Session RPE of the session just finished**.

**Weekly Adjustment** is executed immediately after the final scheduled session of the current week ends. Starting on Day 8, the system evaluates ACWR, Planned vs. Actual Frequency, and non-completion causes, then applies the **Weekly Average RPE mapped to the RPE Scaling table** to determine duration adjustments (Increase or Decrease) for the next calendar week's plan. Prior to Day 8, the upcoming plan continues using the onboarding baseline.

**Recurring Plan Generation** builds the next week's plan immediately upon completion of the weekly evaluation, ensuring the upcoming plan is ready prior to the start of the next week.

Together, these components form a continuous adaptive training loop responsive to the user's performance and physiological feedback, helping individuals with excess body weight cultivate consistent, sustainable, and medically sound exercise habits.
