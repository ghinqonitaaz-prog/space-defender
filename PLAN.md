# PLAN.md — Universal 2D Arcade Game Roadmap (Untuk Siswa)

Dokumen ini adalah rencana kerja universal dan log perkembangan proyek pembuatan game arcade 2D dalam 1 file `index.html`. Dokumen ini diperbarui secara otomatis oleh AI setiap kali ada perubahan kode sebelum push ke GitHub.

---

## 1. Ikhtisar Game

- **Judul Game**: Space Defender
- **Genre**: 2D Arcade Game
- **Format**: Single-File HTML (`index.html`) tanpa dependensi eksternal
- **Platform**: Web Browser (Desktop & Mobile Responsive)
- **Audio**: Web Audio API Sintetis
- **Penyimpanan**: LocalStorage (`arcade_leaderboard`, `arcade_last_username`)
- **Kebijakan Caching**: Zero-Cache (`no-cache, no-store, must-revalidate`)
- **Deployment**: GitHub Pages (Branch `main`, path `/`)

---

## 2. Fase Pengembangan Universal

### Phase 0: Brainstorming & Desain Game
- [ ] Siswa menentukan ide & tema game (misal: Space Defender, Flappy Arcade, Catch & Dodge, Brick Breaker, Snake).
- [ ] Menentukan kontrol permainan (Keyboard, Mouse, atau Sentuh).
- [ ] Menentukan aturan menang/kalah dan sistem penambahan skor.
- [ ] Menentukan gaya visual (Retro Pixel, Neon Glow, atau Minimalis).

### Phase 1: Setup Single-File `index.html`
- [ ] Buat kerangka HTML5 dengan meta tag **Zero-Cache**.
- [ ] Siapkan kanvas responsif dengan rasio layar tetap.
- [ ] Susun struktur kode modular 8 blok di JavaScript.
- [ ] Siapkan game loop utama (`requestAnimationFrame`).

### Phase 2: Username & Leaderboard LocalStorage
- [ ] Buat modal input username dengan validasi nama (1–15 karakter, anti-XSS).
- [ ] Simpan dan baca username terakhir dari `arcade_last_username`.
- [ ] Buat fungsi penyimpanan dan penyortiran Top 10 skor di `arcade_leaderboard`.
- [ ] Buat tampilan antarmuka (UI) tabel peringkat skor di menu game.

### Phase 3: Mekanik Inti Permainan (Core Gameplay)
- [ ] Sistem status game (`MENU`, `PLAYING`, `PAUSED`, `GAMEOVER`).
- [ ] Karakter pemain (pergerakan dan batas layar).
- [ ] Rintangan / musuh (kecepatan, variasi, dan pola kemunculan).
- [ ] Deteksi tabrakan (Collision Detection) dan perhitungan skor.

### Phase 4: Efek Suara & Visual Juice
- [ ] Efek suara arcade sintetis dengan Web Audio API (Start, Gerak, Tabrakan, Game Over).
- [ ] Efek ledakan partikel sederhana saat tabrakan/poin didapat.
- [ ] Efek getar layar (screen shake) saat game over.

### Phase 5: Publikasi GitHub Pages
- [ ] Inisialisasi git dan branch `main`.
- [ ] Buat repository GitHub publik via GitHub CLI (`gh repo create <repo> --public --source=. --remote=origin --push`). *Wajib berstatus Public agar GitHub Pages gratis dapat aktif.*
- [ ] Aktifkan GitHub Pages (Metode CLI `gh api` atau manual via Web UI Settings > Pages branch `main` root `/`).
- [ ] Kompatibilitas Google AI Studio: Sediakan snippet perintah terminal siap salin jika proyek dilanjutkan lewat antarmuka web.
- [ ] Pantau status workflow build di tab Actions (`gh run list`).
- [ ] Tes live demo (`https://<username>.github.io/<repo>/`) dan pastikan pembaruan langsung tampil tanpa hambatan cache (Zero-Cache test).

---

## 3. Matriks Tugas & Status Pelaksanaan

| ID | Tugas / Fitur | Fase | Status | Pelaksana | Catatan |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **T-00** | Pembuatan template `AGENTS.md` & `PLAN.md` untuk Siswa | Fondasi | `DONE` | AI | Template standar single model siap |
| **T-01** | Brainstorming Ide & Konsep Game Spesifik | Phase 0 | `DONE` | Siswa + AI | Dipilih: **Space Defender** (Shoot 'em Up) |
| **T-02** | Pembuatan Skeleton `index.html` & Meta Zero-Cache | Phase 1 | `DONE` | AI | 8 blok modular lengkap terpasang |
| **T-03** | Modul Input Username & Leaderboard Top 10 | Phase 2 | `DONE` | AI | LocalStorage CRUD & UI tabel |
| **T-04** | Logika Pergerakan, Rintangan & Tabrakan | Phase 3 | `DONE` | AI | Core loop & kalkulasi tabrakan AABB |
| **T-05** | Efek Suara Synth Web Audio API & Partikel | Phase 4 | `DONE` | AI | Audio sintetis & efek partikel ledakan |
| **T-06** | Deployment GitHub Pages via GitHub CLI (`gh`) | Phase 5 | `TODO` | AI | Publikasi live & verifikasi zero-cache |

*Status: `TODO` | `IN-PROGRESS` | `DONE`*

---

## 4. Changelog & Riwayat Revisi

| Versi | Tanggal | Pelaksana | Ringkasan Perubahan | Referensi Commit |
| :--- | :--- | :--- | :--- | :--- |
| **v0.3.0** | 2026-10-24 | AI | Implementasi game **Space Defender** lengkap dalam `index.html`: 8 blok modular, Web Audio API, LocalStorage leaderboard, tombol sentuh mobile, partikel ledakan, dan loop utama | *(belum di-commit)* |
| **v0.2.2** | 2026-10-24 | AI | Menambahkan panduan alur integrasi khusus Google AI Studio & penegasan repo wajib Public | `07475fb` |
| **v0.2.1** | 2026-10-24 | AI | Menambahkan panduan detail aktivasi GitHub Pages (CLI gh api & Web UI visual) serta troubleshooting | `3aaf671` |
| **v0.2.0** | 2026-10-24 | AI | Konfigurasi ulang ke Single Model AI untuk siswa, modularisasi 8 blok kode, dan penyesuaian matriks tugas | `2954bb0` |
| **v0.1.0** | 2026-10-24 | AI | Inisialisasi awal file sistem `AGENTS.md` dan universal blueprint `PLAN.md` | `47aa88e` |
