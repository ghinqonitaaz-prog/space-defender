# PLAN.md — Universal 2D Arcade Game Roadmap (Untuk Siswa)

Dokumen ini adalah rencana kerja universal dan log perkembangan proyek pembuatan game arcade 2D dalam 1 file `index.html`. Dokumen ini diperbarui secara otomatis oleh AI setiap kali ada perubahan kode sebelum push ke GitHub.

---

## 1. Ikhtisar Game

- **Judul Game**: Space Defender
- **Genre**: 2D Arcade Game (Shoot 'em Up)
- **Format**: Single-File HTML (`index.html`) tanpa dependensi eksternal
- **Platform**: Web Browser (Desktop & Mobile Responsive)
- **Audio**: Web Audio API Sintetis (dapat dimatikan, tersimpan di `arcade_muted`)
- **Penyimpanan**: LocalStorage (`arcade_leaderboard`, `arcade_last_username`, `arcade_muted`, `arcade_ghost_v1`, `arcade_ghost_enabled`)
- **Kebijakan Caching**: Zero-Cache (`no-cache, no-store, must-revalidate`)
- **Deployment**: GitHub Pages (Branch `main`, path `/`)

---

## 2. Fase Pengembangan Universal

### Phase 0: Brainstorming & Desain Game
- [x] Siswa menentukan ide & tema game (Space Defender).
- [x] Menentukan kontrol permainan (Keyboard + tombol sentuh).
- [x] Menentukan aturan menang/kalah dan sistem penambahan skor.
- [x] Menentukan gaya visual (Retro Neon / Space).

### Phase 1: Setup Single-File `index.html`
- [x] Buat kerangka HTML5 dengan meta tag **Zero-Cache**.
- [x] Siapkan kanvas responsif dengan rasio layar tetap (400 × 600).
- [x] Susun struktur kode modular 8 blok di JavaScript.
- [x] Siapkan game loop utama (`requestAnimationFrame` + delta time).

### Phase 2: Username & Leaderboard LocalStorage
- [x] Buat modal input username dengan validasi nama (1–15 karakter, anti-XSS).
- [x] Simpan dan baca username terakhir dari `arcade_last_username`.
- [x] Buat fungsi penyimpanan dan penyortiran Top 10 skor di `arcade_leaderboard`.
- [x] Buat tampilan antarmuka (UI) tabel peringkat skor di menu game.

### Phase 3: Mekanik Inti Permainan (Core Gameplay)
- [x] Sistem status game (`MENU`, `PLAYING`, `PAUSED`, `GAMEOVER`).
- [x] Karakter pemain (pergerakan dan batas layar).
- [x] Rintangan / musuh (kecepatan, variasi, dan pola kemunculan).
- [x] Deteksi tabrakan (Collision Detection) dan perhitungan skor.

### Phase 4: Efek Suara & Visual Juice
- [x] Efek suara arcade sintetis dengan Web Audio API (Start, Tembak, Ledakan, Power-up, Game Over).
- [x] Efek ledakan partikel sederhana saat tabrakan/poin didapat.
- [x] Efek getar layar (screen shake) saat kehilangan nyawa dan game over.

### Phase 5: Publikasi GitHub Pages
- [x] Inisialisasi git dan branch `main`.
- [x] Buat repository GitHub publik via GitHub CLI (`gh repo create <repo> --public --source=. --remote=origin --push`).
- [x] Aktifkan GitHub Pages (branch `main`, root `/`).
- [x] Kompatibilitas Google AI Studio: sediakan snippet perintah terminal siap salin.
- [x] Pantau status workflow build di tab Actions (`gh run list`).
- [x] Tes live demo dan pastikan pembaruan langsung tampil (Zero-Cache test).

### Phase 6: Peningkatan Gameplay (Iterasi Lanjutan)
- [x] Perbaiki layout CSS (body/canvas terpusat, overlay dapat di-scroll, kontrol sentuh hanya di perangkat sentuh).
- [x] Normalisasi delta-time agar kecepatan game konsisten di monitor 60/120/144 Hz dan saat frame drop.
- [x] Tambah musuh **UFO** yang bergerak zig-zag dengan skor lebih tinggi.
- [x] Tambah **power-up** Shield (kebal 6 detik) dan Triple Shot (tembakan 3 arah, 8 detik).
- [x] Tambah **sistem wave** dengan banner pengumuman dan peningkatan kecepatan/spawn musuh.
- [x] Tambah pause via tombol HUD, tombol `P`, `ESC`, serta auto-pause saat tab tidak aktif.
- [x] Tambah toggle SFX (`M` / tombol HUD) yang tersimpan di `arcade_muted`.
- [x] Tampilkan Rekor (best score) di HUD, highlight skor baru, dan badge "Rekor Baru".
- [ ] Iterasi berikutnya (ide siswa): boss wave, combo multiplier, atau tema visual baru.

### Phase 7: Ghost Replay & Pesawat Tempur (v0.6.0)
- [x] Rekam gerak pemain + status tembakan run terbaik (sampel 10×/detik) selama bermain.
- [x] Simpan ghost ke `arcade_ghost_v1` hanya jika skor run baru mengalahkan ghost sebelumnya.
- [x] Putar ulang ghost sebagai jet transparan + label `GHOST <skor>` selama run berikutnya.
- [x] Toggle ghost lewat tombol HUD atau tombol `G`, preferensi disimpan di `arcade_ghost_enabled`.
- [x] Tampilkan info ghost terbaik (nama, skor, wave) di menu utama.
- [x] Desain ulang pemain menjadi jet tempur: badan logam, sayap swept, kanopi, rudal, afterburner, lampu navigasi.
- [x] Efek *banking* saat bermanuver, trail mesin, dan partikel asap/nyala.
- [x] Uji replay lintas reload browser (LocalStorage) dan pastikan tidak ada error runtime.

---

## 3. Matriks Tugas & Status Pelaksanaan

| ID | Tugas / Fitur | Fase | Status | Pelaksana | Catatan |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **T-00** | Pembuatan template `AGENTS.md` & `PLAN.md` untuk Siswa | Fondasi | `DONE` | AI | Template standar single model siap |
| **T-01** | Brainstorming Ide & Konsep Game Spesifik | Phase 0 | `DONE` | Siswa + AI | Dipilih: **Space Defender** (Shoot 'em Up) |
| **T-02** | Pembuatan Skeleton `index.html` & Meta Zero-Cache | Phase 1 | `DONE` | AI | 8 blok modular lengkap terpasang |
| **T-03** | Modul Input Username & Leaderboard Top 10 | Phase 2 | `DONE` | AI | LocalStorage CRUD & UI tabel |
| **T-04** | Logika Pergerakan, Rintangan & Tabrakan | Phase 3 | `DONE` | AI | Core loop & kalkulasi tabrakan AABB |
| **T-05** | Efek Suara Synth Web Audio API & Partikel | Phase 4 | `DONE` | AI | Audio sintetis, partikel, screen shake |
| **T-06** | Deployment GitHub Pages via GitHub CLI (`gh`) | Phase 5 | `DONE` | AI | Live di `https://ghinqonitaaz-prog.github.io/space-defender/` |
| **T-07** | Perbaikan layout CSS & overlay responsif | Phase 6 | `DONE` | AI | Body flex center, kartu menu scrollable di layar kecil |
| **T-08** | Delta-time normalization pada game loop | Phase 6 | `DONE` | AI | Kecepatan konsisten di semua refresh rate |
| **T-09** | Musuh UFO zig-zag + skor 50 | Phase 6 | `DONE` | AI | Variasi musuh kedua |
| **T-10** | Power-up Shield & Triple Shot | Phase 6 | `DONE` | AI | Drop dari UFO (70%) dan alien biasa (9%) |
| **T-11** | Sistem Wave & peningkatan kesulitan | Phase 6 | `DONE` | AI | Tiap 10 kill, banner + speed naik |
| **T-12** | Pause (tombol/P/ESC/auto) & toggle SFX | Phase 6 | `DONE` | AI | Preferensi mute tersimpan |
| **T-13** | HUD Rekor, highlight skor baru & badge rekor | Phase 6 | `DONE` | AI | `getBestScore()` + `lastSavedId` |
| **T-14** | Ghost replay (rekam, simpan, putar ulang run terbaik) | Phase 7 | `DONE` | AI | `arcade_ghost_v1`, sampel 10 Hz, toggle `G` |
| **T-15** | Desain ulang pesawat tempur + efeks visual | Phase 7 | `DONE` | AI | Jet vektor, banking, afterburner, trail mesin |

*Status: `TODO` | `IN-PROGRESS` | `DONE`*

---

## 4. Changelog & Riwayat Revisi

| Versi | Tanggal | Pelaksana | Ringkasan Perubahan | Referensi Commit |
| :--- | :--- | :--- | :--- | :--- |
| **v0.6.0** | 2026-11-07 | AI | Ghost replay (rekam & putar ulang run terbaik, simpan di `arcade_ghost_v1`, toggle `G`, info ghost di menu) dan desain ulang pemain menjadi jet tempur (sayap swept, kanopi, rudal, afterburner, lampu navigasi, banking, trail mesin) | *(commit ini)* |
| **v0.5.0** | 2026-10-31 | AI | Perbaikan layout CSS, delta-time normalization, musuh UFO, power-up Shield & Triple Shot, sistem wave, pause (tombol/P/ESC/auto), toggle SFX, HUD rekor, highlight skor baru, dan badge REKOR BARU | *(commit ini)* |
| **v0.4.0** | 2026-10-31 | AI | Deployment ke GitHub Pages: inisialisasi git, commit, push ke `ghinqonitaaz-prog/space-defender` (public), aktifkan GitHub Pages branch `main` path `/`, situs live di `https://ghinqonitaaz-prog.github.io/space-defender/` | `841e2ad` |
| **v0.3.0** | 2026-10-24 | AI | Implementasi game **Space Defender** lengkap dalam `index.html`: 8 blok modular, Web Audio API, LocalStorage leaderboard, tombol sentuh mobile, partikel ledakan, dan loop utama | `551fc22` |
| **v0.2.2** | 2026-10-24 | AI | Menambahkan panduan alur integrasi khusus Google AI Studio & penegasan repo wajib Public | `07475fb` |
| **v0.2.1** | 2026-10-24 | AI | Menambahkan panduan detail aktivasi GitHub Pages (CLI gh api & Web UI visual) serta troubleshooting | `3aaf671` |
| **v0.2.0** | 2026-10-24 | AI | Konfigurasi ulang ke Single Model AI untuk siswa, modularisasi 8 blok kode, dan penyesuaian matriks tugas | `2954bb0` |
| **v0.1.0** | 2026-10-24 | AI | Inisialisasi awal file sistem `AGENTS.md` dan universal blueprint `PLAN.md` | `47aa88e` |
