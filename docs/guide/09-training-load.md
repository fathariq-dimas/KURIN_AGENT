# 9. Training Load

Training Load quantifies the cumulative physiological load experienced by the user during an exercise session.

The calculation uses the Session-RPE method, multiplying the session RPE by the actual session duration.

### 9.1. Training Load Calculation

```text
Training Load = RPE * Session Duration
```

Example:

- Session Duration: 30 minutes.
- RPE: 4.
- Training Load: 120 AU.

AU (*arbitrary unit*) is the standardized unit used to express training load.

Training Load is computed **immediately after the final RPE is confirmed** and **before the Exercise Summary is displayed**, using the actual recorded session duration.

### 9.2. Daily Training Load

The Training Loads from all sessions completed on the same calendar date are summed to produce the Daily Training Load:

```text
Daily Training Load = sum(Training Load of all sessions on that day)
```

Daily Training Load provides the direct daily input for calculating Acute Training Load and Chronic Training Load.
