# Kurin Documentation & Planning Templates (`.agents/template/`)

Folder ini berisi **formatting template non-authoritative** untuk dokumentasi dan pull request Kurin. Template hanya menyediakan heading, tabel kosong, placeholder generik, dan visible Markdown section-definition callouts. Template tidak memuat requirement, model, nilai batas, formula, state, interface, test case, atau keputusan produk yang boleh disalin sebagai fakta.

> **Penting:** Teks di dalam HTML comments adalah instruksi authoring, bukan konten dokumen. Semua placeholder dan instruction comments wajib diganti atau dihapus sebelum dokumen dipublikasikan. File di folder ini tidak pernah menjadi input eksekusi langsung untuk **@builder**.

Setiap template menjelaskan setiap section melalui visible Markdown callout
`Section Definition` atau `Subsection Definition` yang diletakkan tepat sebelum
konten terkait dan dapat dibaca langsung di Markdown Preview. Definisi tersebut
menjawab empat hal:

1. **Meaning** — arti dan fungsi section dalam dokumen.
2. **Include** — informasi minimum yang harus ditulis.
3. **Exclude** — informasi yang tidak boleh dicampurkan ke section tersebut.
4. **Completion** — kondisi yang membuat section dianggap lengkap, termasuk
   cara menyatakan `Not applicable` bila section memang tidak relevan.

Visible definition callout adalah authoring guidance, bukan bagian dari dokumen
final. Author wajib menghapus setiap callout setelah section diisi dan sebelum
dokumen dipublikasikan atau diserahkan ke agent berikutnya.

---

## 1. Daftar Template

| Template | File | Author Utama | Konsumen Utama | Deskripsi |
| :--- | :--- | :---: | :---: | :--- |
| **Requirements** | [`requirement.md`](requirement.md) | @planner | @builder & Stakeholders | Struktur untuk menulis requirement konkret yang seluruh isinya diturunkan dari sumber otoritatif. |
| **Technical Spec** | [`spec.md`](spec.md) | @planner | @builder | Struktur untuk menerjemahkan requirement yang telah disetujui menjadi keputusan teknis yang traceable. |
| **Master Roadmap** | [`roadmap.md`](roadmap.md) | @planner | User, @planner, @builder | Struktur status dan phase order untuk tepat satu feature di `docs/Plan/<feature-id>-<feature-slug>/roadmap.md`. |
| **Implementation Phase** | [`plan.md`](plan.md) | @planner | @builder | Struktur vertical-slice plan untuk satu phase yang telah direview user. |
| **Pull Request** | [`pull-request.md`](pull-request.md) | @builder / Developer | Reviewer | Struktur untuk melaporkan perubahan dan bukti verifikasi yang benar-benar terjadi. |

---

## 2. Cara Penggunaan untuk @planner

Ketika merencanakan feature baru:

1. Salin template requirements dan spec ke folder feature target:
   ```bash
   mkdir -p docs/Feature/<XXX>-<feature-slug>
   cp .agents/template/requirement.md docs/Feature/<XXX>-<feature-slug>/requirement.md
   cp .agents/template/spec.md docs/Feature/<XXX>-<feature-slug>/spec.md
   ```
2. Isi dokumen hanya dari product source, keputusan user yang eksplisit, feature docs yang telah disetujui, dan `.agents/rules/` yang relevan.
3. Jika sumber tidak menetapkan suatu nilai atau perilaku, catat sebagai open decision; jangan mengisi berdasarkan contoh, kebiasaan, atau general knowledge.
4. Hapus seluruh visible section-definition callouts dan HTML instruction comments, lalu ganti semua placeholder `<...>` sebelum dokumen dinyatakan siap.
5. Pastikan `requirement.md` dan `spec.md` lolos quality gate masing-masing.
6. Gunakan [`roadmap.md`](roadmap.md) untuk membuat master roadmap feature di `docs/Plan/<feature-id>-<feature-slug>/roadmap.md`.
7. Gunakan [`plan.md`](plan.md) untuk merinci hanya phase berikutnya yang eligible di `docs/Plan/<feature-id>-<feature-slug>/phase-<sequence>-<phase-slug>.md`.
8. Dapatkan approval user atas phase tersebut. Serahkan hanya phase yang approved kepada `@builder`; future phase tetap summary sampai predecessor selesai.

---

## 3. Cara Penggunaan untuk @builder

1. Baca hanya dokumen konkret di `docs/Feature/` dan `docs/Plan/` yang dirujuk oleh handoff.
2. Perlakukan dokumen konkret tersebut sebagai **immutable source of truth**.
3. Jangan membaca atau menggunakan `.agents/template/` sebagai sumber requirement atau keputusan implementasi.
4. Tolak handoff jika dokumen masih memuat visible section-definition callouts, HTML instruction comments, placeholder `<...>`, atau blocking open decision.

---

## 4. Istilah Lintas Template

| Istilah | Definisi |
| :--- | :--- |
| **Source** | Dokumen, section, project rule, atau keputusan user eksplisit yang secara otoritatif mendukung suatu pernyataan. |
| **Traceability** | Hubungan eksplisit dari source ke requirement, spec, plan, implementasi, dan bukti verifikasi. |
| **Open decision** | Pertanyaan yang belum memiliki jawaban otoritatif dan dapat mengubah requirement, perilaku, model data, formula, interface, atau cara verifikasi. Open decision bukan section wajib berisi masalah; tulis `None` bila tidak ada. |
| **Blocking open decision** | Open decision yang harus diselesaikan sebelum dokumen dapat disetujui atau diserahkan ke @builder karena implementasi aman tidak dapat dipilih tanpa menebak. |
| **Dependency** | Input, contract, feature, service, framework, atau perubahan lain yang diperlukan atau dikonsumsi oleh scope saat ini. Dependency sudah diketahui; open decision belum diputuskan. |
| **Acceptance criterion (AC)** | Outcome observable dan independently verifiable yang membuktikan satu atau lebih functional requirement terpenuhi. |
| **Evidence** | Output aktual dari test, build, review, inspection, atau artefak lain yang dapat diperiksa ulang; bukan rencana atau klaim tanpa bukti. |
| **Not applicable (N/A)** | Section tidak relevan terhadap scope dan disertai alasan konkret. Jangan menggunakan N/A untuk menyembunyikan informasi yang belum diketahui; gunakan open decision. |

