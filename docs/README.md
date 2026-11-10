# Kurin Documentation

Folder `docs/` memisahkan dokumentasi feature dari rencana implementasi.

```text
docs/
├── Feature/
│   └── <id>-<feature-name>/
│       ├── requirement.md
│       └── spec.md
└── Plan/
    └── <feature-id>-<feature-name>/
        ├── roadmap.md
        └── phase-<sequence>-<phase-name>.md
```

Dokumentasi diturunkan dari `Kurin_Exercise_Guidance_v1.6.md`.

## Features

| ID | Feature | Folder |
|---:|---|---|
| `001` | Integrated onboarding: screening, profile, goal, training assignment, schedule, dan Initial Training Plan | `Feature/001-onboarding-health-profile/` |
| `002` | Reserved — Training Type Assignment absorbed into Feature 001 | No standalone feature folder or implementation |
| `003` | Reserved — initial Weekly Training Plan generation absorbed into Feature 001 | No standalone feature folder or implementation |
| `004` | Exercise session guidance | `Feature/004-exercise-session-guidance/` |
| `005` | RPE estimation dan confirmation | `Feature/005-rpe-estimation/` |
| `006` | Training load dan workload metrics | `Feature/006-training-load-workload/` |
| `007` | Daily training adjustment | `Feature/007-daily-training-adjustment/` |
| `008` | Weekly training adjustment | `Feature/008-weekly-training-adjustment/` |

Setiap folder feature berisi:

- `requirement.md`: tujuan, functional requirements, acceptance criteria, dan batas scope.
- `spec.md`: rancangan teknis, model data, aturan bisnis, state/decision flow, dan skenario pengujian.

Feature IDs `002` dan `003` tetap dicatat untuk menjaga stabilitas referensi historis, tetapi tidak memiliki
roadmap, model, service, UI, atau test terpisah. Seluruh Training Type Assignment dan pembuatan Initial Training
Plan saat onboarding dimiliki oleh Feature `001`. Perencanaan mingguan berulang setelah onboarding tetap merupakan
perilaku downstream yang dijelaskan oleh feature adjustment terkait, bukan Feature `003` terpisah.

## Plans

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
  [includes reserved responsibilities 002 and 003]
  → 004-exercise-session-guidance
    → 005-rpe-estimation
      → 006-training-load-workload
        → 007-daily-training-adjustment
        → 008-weekly-training-adjustment
```
