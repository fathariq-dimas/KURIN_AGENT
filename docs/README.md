# Kurin Documentation

Folder `docs/` memisahkan dokumentasi feature dari rencana implementasi.

```text
docs/
├── Feature/
│   └── <id>-<feature-name>/
│       ├── requirement.md
│       └── spec.md
└── Plan/
    ├── master-plan.md             # Product workflow, portfolio index/status, and dependency map
    └── <feature-id>-<feature-name>/
        ├── roadmap.md
        └── phase-<sequence>-<phase-name>.md
```

Dokumentasi diturunkan dari `Kurin_Exercise_Guidance_v1.6.md`.

Lihat [`Plan/master-plan.md`](Plan/master-plan.md) untuk post-onboarding product workflow, tiga tab utama, indeks
seluruh feature, status dokumentasi, cross-feature data flow, dan dependency map. File tersebut bukan implementation
roadmap lintas-feature dan tidak menggantikan approval gate per feature.

## Post-Onboarding Navigation

Berdasarkan keputusan user 2026-11-11, setelah onboarding selesai aplikasi membuka `TabView` dengan tiga tab:

| Tab | Feature ownership | Portfolio purpose |
|---|---|---|
| **Plan** | Feature `002` | Menampilkan weekly plan dan hasil authoritative RPE/load/ACWR serta daily/weekly adjustment. Kalkulasi tetap dimiliki Features `004`–`007`. |
| **Exercise** | Feature `003` entry | Menampilkan selected/next scheduled workout, merekam exercise session, dan mengoordinasikan Apple Watch audio/haptic guidance. |
| **Progress** | Feature `008` | Menampilkan persistent session history/detail serta chart Training Load, calories, steps, dan distance berdasarkan Kurin sessions untuk grouping week/month/year. |

Session yang dipilih dari Plan masuk ke workflow Exercise. Setelah session selesai dan hasil RPE serta Training Load
final, Plan menerima adjustment refresh sedangkan Progress menerima finalized session record untuk history dan chart.

## Features

| ID | Feature | Folder |
|---:|---|---|
| `001` | Integrated onboarding: screening, profile, goal, training assignment, schedule, dan Initial Training Plan | `Feature/001-onboarding-health-profile/` |
| `002` | Training Plan Dashboard | `Feature/002-training-plan-dashboard/` |
| `003` | Exercise session tracking and guidance | `Feature/003-exercise-session-guidance/` |
| `004` | RPE estimation dan confirmation | `Feature/004-rpe-estimation/` |
| `005` | Training load dan workload metrics | `Feature/005-training-load-workload/` |
| `006` | Daily training adjustment | `Feature/006-daily-training-adjustment/` |
| `007` | Weekly training adjustment | `Feature/007-weekly-training-adjustment/` |
| `008` | Training progress and session history | `Feature/008-training-progress-session-history/` |

Setiap folder feature berisi:

- `requirement.md`: tujuan, functional requirements, acceptance criteria, dan batas scope.
- `spec.md`: rancangan teknis, model data, aturan bisnis, state/decision flow, dan skenario pengujian.

Training Type Assignment dan pembuatan Initial Training Plan tetap dimiliki oleh Feature `001`; keduanya bukan
feature standalone. Berdasarkan keputusan user 2026-11-11, active feature IDs disusun ulang secara berurutan dan
Training Plan Dashboard menjadi Feature `002` yang ditampilkan segera setelah onboarding selesai. Keputusan user
yang sama menetapkan Feature `008` untuk persistent Progress history dan chart; requirement/spec Feature `008` belum
boleh dianggap dibuat atau disetujui hanya karena portfolio scope-nya tercatat di sini.

## Plans

`Plan/master-plan.md` adalah portfolio-level index untuk melihat seluruh stable feature ID, responsibility,
dependency, product workflow, cross-feature data flow, dan documentation status dalam satu tempat. File ini tidak
berisi implementation phase dan tidak dapat digunakan untuk melewati urutan approval `requirement.md` → `spec.md`
→ roadmap/phase per feature.

Setiap feature memiliki tepat satu folder master roadmap yang cocok dengan folder feature-nya:

```text
Plan/<feature-id>-<feature-name>/
├── roadmap.md
└── phase-<sequence>-<phase-name>.md
```

`roadmap.md` merangkum scope satu feature, urutan phase, dependency, FR/AC coverage, status review user, status implementasi, status verifikasi, dan evidence completion. Setiap file phase menjelaskan satu vertical slice yang menghasilkan outcome user-visible atau independently verifiable.

Planner hanya merinci phase berikutnya yang eligible. Phase harus direview dan disetujui user sebelum diserahkan ke builder. Setelah builder melaporkan evidence, planner memverifikasi dan menandai phase `Complete` sebelum membuka phase berikutnya.

Satu roadmap tidak boleh memuat implementation task milik feature lain. Feature downstream hanya dicatat sebagai dependency atau output contract sampai feature tersebut memiliki roadmap sendiri. Local phase sequence bukan Feature ID atau Git Task ID.

Jangan membuat plan kosong, menduplikasi requirement/spec, atau menggunakan flat file `Plan/<id>-<phase-name>.md` baru. Flat plan lama harus dimigrasikan ke struktur roadmap sebelum handoff berikutnya.

## Dependency Order

```text
001-onboarding-health-profile
  → 002-training-plan-dashboard
    → 003-exercise-session-guidance
      → 004-rpe-estimation
        → 005-training-load-workload
          → (006-daily-training-adjustment and 007-weekly-training-adjustment)
            → 002-training-plan-dashboard

Finalized outputs from 003, 004, and 005
  → 008-training-progress-session-history
```

Feature 002 pertama kali menampilkan Initial Training Plan dari Feature 001. Setelah sesi berjalan, output Daily
Adjustment dan Weekly Adjustment kembali memperbarui Feature 002. Feature 002 tidak mengambil alih session
execution, RPE, Training Load/ACWR, daily adjustment, atau weekly adjustment. Feature 008 mengonsumsi finalized
Kurin session records untuk history dan visualization tanpa mengubah adaptive Training Plan.
