# Feature Master Roadmaps and Implementation Phases

Setiap feature memiliki satu folder master roadmap:

```text
Plan/<feature-id>-<feature-name>/
├── roadmap.md
└── phase-<sequence>-<phase-name>.md
```

Contoh:

```text
Plan/001-onboarding-health-profile/
├── roadmap.md
├── phase-01-health-screening.md
├── phase-02-healthkit-and-profile.md
└── phase-03-weight-goal.md
```

## Rules

1. Satu folder roadmap hanya boleh berisi scope implementasi dari satu folder `docs/Feature/` yang cocok.
2. `roadmap.md` mencatat phase order, dependency, FR/AC coverage, user-review status, implementation status, verification status, dan completion evidence.
3. Setiap phase adalah vertical slice dengan outcome user-visible atau independently verifiable. Jangan membagi seluruh feature menjadi phase horizontal “semua Data”, “semua Domain”, lalu “semua Presentation”.
4. Planner merinci hanya phase berikutnya yang eligible. Future phase tetap summary sampai predecessor selesai dan input-nya stabil.
5. Status phase mengikuti `Draft → Ready for Review → Approved → In Progress → Verification → Complete`.
6. Builder tidak mengubah file di `docs/`. Builder melaporkan evidence; planner yang memverifikasi, menandai phase `Complete`, dan membuka phase berikutnya.
7. Feature downstream hanya dicatat sebagai dependency/output contract. Implementation task-nya harus berada dalam roadmap feature downstream sendiri.
8. Local phase sequence (`01`, `02`, ...) bukan Feature ID dan bukan Git Task ID.

Flat plan lama di `Plan/<id>-<phase-name>.md` dianggap legacy dan tidak boleh diserahkan ke builder lagi sebelum dimigrasikan ke struktur ini.
