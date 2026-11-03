# Panduan Fitur Secondary vs Primary

Disusun untuk guru Secondary di SmartBag. Fokus: fitur yang benar-benar dipakai teacher sehari-hari, dan apa yang berbeda dibanding Primary.

## Ringkasan Cepat dalam Satu Tabel

| Area | Primary | Secondary |
| --- | --- | --- |
| Input nilai | CAT1-CAT4, MYE, FYE -> SA1 & SA2 | TT, BMT, DCA, AA per term -> CA1, CA2 -> FYA |
| Rumus akhir | SA1 = CAT1 + CAT2 + MYE; SA2 = CAT3 + CAT4 + FYE | FYA = CA1 20% + MYE 25% + CA2 20% + FYE 35% |
| Berat komponen | Satu set bobot per level (`/default-grade-weights`) | Berbeda per tier: Sec1-3 vs Sec4-JC (`/secondary-grade-weights`) |
| Menu gradebook | `/gradebook` | `/secondary-gradebook` (layout & tab berbeda) |
| Laporan nilai | Term / Semester, FTP, SPR, SED, LO | Term / Semester versi secondary, GPA, Cambridge, tanpa FTP di modal |
| Ujian Cambridge | Tidak ada | SVE, Threshold, PUM, IGCSE / AS / A Level report |
| Ranking / GPA report | Tidak ada GPA report | GPA roll-up multi-tahun (maks 4 class year) |
| Disiplin | Discipline, Tardiness, Statistics | Secondary Discipline, Yellow & Merit Form, Verify Conduct, Reduction |
| Conduct remark | Teacher form-class `/conduct` | Teacher form-class `/conduct` (sama) + verifikasi admin |
| E-Badges | `/ebadges` (FTP) | `/ebadges` (FTP) dipakai juga, ditambah medali gradebook G/S/B |

## 1. Gradebook Secondary: Napasnya Berbeda

Di Primary, nilai dijumlahkan ke Semester Assessment (SA1 dan SA2). Di Secondary, nilai masuk ke komponen per term, lalu dirangkai menjadi CA1, CA2, dan akhirnya FYA (Final Year Assessment).

- CA1 = gabungan nilai Term 1 dan Term 2 yang sudah berbobot.
- CA2 = gabungan nilai Term 3 dan Term 4 yang sudah berbobot.
- FYA = CA1 x 20% + MYE x 25% + CA2 x 20% + FYE x 35%.
- Keempat bobot FYA wajib berjumlah 100. Admin bisa mengubah; setiap bobot 0-100.

Contoh Primary: SA1 = CAT1 + CAT2 + MYE, SA2 = CAT3 + CAT4 + FYE. Tidak ada CA1/CA2 dan tidak ada FYA gabungan.

## 2. Dua Tier Bobot di Secondary

Bobot komponen Secondary TIDAK sama untuk semua level. Ada dua tier berdasarkan Master Level:

| Tier | Master Level | Komponen berdasarkan Term |
| --- | --- | --- |
| Sec1-3 | ML 7-12 | T1: TT 18.75 + BMT 18.75 + DCA 12.5; T2: TT 18.75 + TT 18.75 + DCA 12.5; T3: TT 25 + BMT 25 + DCA 12.5; T4: AA 25 + DCA 12.5 |
| Sec4-JC | ML 13-17 | T1: TT 25 + BMT 25 + DCA 12.5; T2: TT 25 + DCA 12.5; T3: TT 25 + BMT 25 + DCA 12.5; T4: AA 25 + DCA 12.5 |

Catatan penting: nilai DCA belum dihitung ke CA1/CA2 sampai modul DCA aktif. Kolom DCA tetap tampil di tabel gradebook, jadi jangan kaget kalau angkanya belum mempengaruhi hasil akhir.

## 3. Menu Gradebook yang Dipegang Guru

| Halaman | Untuk |
| --- | --- |
| `/gradebook` | Gradebook Primary (guna primary teacher) |
| `/secondary-gradebook` | Daftar subject Secondary yang diampu |
| `/secondary-gradebook/:subjectYearId` | Input nilai per class, dengan tab Term 1 sampai Term 4 |
| `/ebadges` | FTP / E-Badge form-class |
| `/sve` | Halaman SVE -- hanya relevan untuk ujian sekolah Secondary |

Di halaman detail Secondary, tab Term otomatis mengikuti tanggal hari ini (term yang sedang berjalan disorot). Ada juga tabel khusus untuk non-academic subject. Khusus level Sec4 dan JC2, tab Term 4 punya perilaku tersendiri (misalnya penyesuaian terkait ujian akhir).

## 4. Laporan (Report) yang Bisa Dibuat

Modal generate report menampilkan jenis laporan yang berbeda tergantung term dan level. Untuk Secondary, FTP disembunyikan dari modal (karena FTP dipakai lewat e-badges), dan muncul laporan spesifik Secondary.

| Term | Jenis laporan yang muncul (Secondary) |
| --- | --- |
| Term 1 dan 3 | REPORT_TERM (versi Secondary; tanpa FTP) |
| Term 2 | REPORT_SEMESTER, GPA (jika level Secondary/College); CAMBRIDGE_ASLEVEL untuk JC2 |
| Term 4 | REPORT_SEMESTER, GPA, LEAPS, FTP_EVALUATION; CAMBRIDGE_IGCSE untuk Sec4 & JC1; CAMBRIDGE_ALEVEL untuk JC2 |

Perbedaan dengan Primary: Primary masih menampilkan FTP di modal, punya laporan SPR dan SED (walaupun saat ini di-comment di UI), dan tidak punya GPA maupun Cambridge.

- Laporan Learning Outcome (LO) bisa menggantikan REPORT_TERM / REPORT_SEMESTER jika grading guide level memakai tipe LEARNING_OUTCOME. Ini berlaku di Primary maupun Secondary.
- GPA hanya ada di Secondary/College. Isinya akumulasi sampai 4 tahun ajaran terakhir.
- Untuk subject Cambridge, GPA mengambil nilai PUM prediksi (as_predicted_pum saat Term 2, predicted_pum saat term lain).
- Laporan Cambridge (IGCSE, AS Level, A Level) memakai template dan threshold tersendiri, lihat bagian berikut.

## 5. Cambridge, SVE, dan Threshold (Khas Secondary)

Ini bagian yang tidak ada di Primary sama sekali. Guru Secondary yang mengajar Cambridge akan memakai SVE (School-based Validated Examination).

- SVE: guru input nilai per paper/komponen ujian, upload dokumen, isi item analysis (kunci jawaban), dan melihat jadwal ujian.
- Threshold: batas nilai huruf (grade boundary) untuk IGCSE/A Level dan AS Prelim. Alurnya DRAFT lalu ACTIVE. Guru tidak mengubah threshold; admin yang mengatur.
- PUM: nilai persentase terstandarisasi Cambridge, dihitung dari ambang batas nilai.
- Predicted PUM: untuk GPA Cambridge dipakai max(PUM prelim, FYA) sesuai aturan yang ditetapkan.
- Kunci jadwal SVE: ada deadline lock; setelah terkunci, penggantian pengawas/edit jadwal diblokir admin.

Pesan praktis untuk guru: kalau ada ujian Cambridge, pastikan nilai per paper diinput di SVE, bukan di gradebook biasa. Gradebook biasa tetap dipakai untuk nilai harian (TT, BMT, DCA, AA).

## 6. Conduct, Disiplin, dan Kesejahteraan

| Hal | Primary | Secondary |
| --- | --- | --- |
| Isi conduct | Guru form-class `/conduct`, remark per siswa | Sama: guru form-class `/conduct` |
| Disiplin admin | `/discipline`, `/tardiness`, `/discipline-statistics` | Secondary Discipline Category, Secondary Discipline, Yellow Form, Merit Form, Reduction |
| Yellow report | Tidak ada | Bisa dibaca per siswa (aggregasi form kuning); ada juga Merit report |
| Verify conduct | Tidak ada | Admin Secondary Verify Conduct |

Yang penting diingat guru Secondary: form disiplin (kuning/merit) dikelola admin Secondary, bukan di menu Primary. Guru tetap punya jalur conduct di form-class, plus e-badges.

## 7. Hal-hal yang Sering Ditanyakan Guru

**Q: Kenapa tidak ada FTP di modal report saya padahal dulu ada?**
A: Untuk level Secondary/JC, FTP disembunyikan dari modal. FTP diisi melalui menu E-Badges (`/ebadges`), bukan menghasilkan report terpisah di modal.

**Q: Kenapa nilai DCA tidak mengubah CA1/CA2 saya?**
A: DCA memang belum masuk perhitungan sampai modul DCA aktif backend. Kolomnya tetap tampil.

**Q: Apakah rumus Primary dan Secondary bisa disamakan?**
A: Tidak. Primary memakai SA1/SA2 (CAT+MYE/FYE). Secondary memakai CA1/CA2 lalu FYA dengan bobot 20/25/20/35. Gunakan halaman secondary-gradebook bila mengajar Secondary.

**Q: Untuk ujian Cambridge, input nilai di mana?**
A: Di menu SVE. Threshold dan PUM diatur admin; guru cukup input nilai paper dan item analysis.

---

Sumber: `bbs-wiki` (secondary-gradebook, gradebooks, report-types, reports, spr-sed, secondary-gpa, cambridge, sve, thresholds, prelims, discipline, secondary-discipline, medals) dan `bbs/client-teacher`. Angka bobot mengikuti dokumen `SECONDARY_GRADEBOOK.md`; rumus moderation dan lainnya hanya ditampilkan bila tercatat, tidak dikarang.
