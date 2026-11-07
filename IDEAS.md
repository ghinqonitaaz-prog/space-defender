# IDEAS.md — Brainstorming Pengembangan "Space Defender"

> **Status:** dokumen ide & audit, **belum ada perubahan pada `index.html`**.
> **Baseline:** v0.5.0 (single-file, 400×600, vanilla JS, leaderboard LocalStorage).
> **Tanggal:** 2026-11-07
>
> Cara pakai: pilih **arah** di Bagian 2, lalu ambil ide dari Bagian 3 sesuai tabel prioritas di Bagian 4.
> Setelah dipilih, ide masuk ke `PLAN.md` sebagai Phase 7 dan baru dikerjakan satu paket per commit.

---

## 1. Audit Cepat Kondisi Sekarang

### 1.1 Yang sudah bagus (jangan dirusak)

- Arsitektur 8 blok rapi, mudah dibaca siswa, tetap 1 file tanpa dependency — sesuai `AGENTS.md`.
- Fondasi teknis benar: delta-time, pause + auto-pause, zero-cache, leaderboard tervalidasi, SFX synth, screen shake, partikel.
- Sudah ada 2 jenis musuh + 2 power-up + sistem wave → cukup untuk demo, tapi belum cukup untuk dimainkan berulang.

### 1.2 Celah desain (kenapa cepat membosankan)

| Aspek | Kondisi v0.5.0 | Akibat |
| :--- | :--- | :--- |
| Mobilitas | Hanya kiri–kanan (`index.html:552-557`) | Tidak ada ruang untuk "dodge"; keterampilan terbatas |
| Ancaman | Musuh **hanya menabrak** ke bawah | Pemain tidak pernah "ditembak"; tidak ada tekanan vertikal |
| Variasi spawn | Acak murni (`spawnAlien`, baris 382) | Tidak ada pola/formation yang bisa dibaca dan dikuasai |
| Struktur | Endless, tanpa fase/jeda | Tidak ada puncak (boss) maupun "napas" antar gelombang |
| Progresi | Tidak ada upgrade/unlock | Tidak ada alasan bermain lagi selain skor |
| Umpan balik | Tidak ada combo/angka skor melayang | Kill terasa sama dari awal sampai akhir |
| Kesulitan | Speed cap wave ~13, spawn cap wave ~9 | Setelah wave 10 tidak ada mekanik baru → sulit tapi datar |
| Sosial | Leaderboard lokal per-browser | Tidak bisa lomba antar teman sekelas |

### 1.3 Bug & risiko teknis (perbaiki sebelum menambah fitur)

| # | Tingkat | Lokasi | Masalah | Perbaikan |
| :-: | :--- | :--- | :--- | :--- |
| 1 | 🔴 Tinggi | `updateEntities` baris 583-611 | Dua alien bisa menabrak di **frame yang sama** → 2 nyawa hilang sekaligus | Proses maksimal 1 tabrakan per frame (`break`/`return` setelah `loseLife`) |
| 2 | 🔴 Tinggi | `loseLife` baris 475-485 | Tidak ada *invulnerability frame* → bisa mati beruntun di titik respawn | `invuln = 1.5` detik + kedip + alien di sekitar dibersihkan |
| 3 | 🟠 Sedang | Baris 568-573, 613-622 | **Tunneling**: peluru bergerak 8 px/frame × `s` (maks 3) = 24 px, lebih besar dari tinggi alien 20 px → tembakan bisa menembus musuh saat FPS drop | Sub-step collision (lihat H2) |
| 4 | 🟠 Sedang | `gameOver` → `saveScoreToLeaderboard` | Skor **0 tetap masuk** leaderboard | Simpan hanya jika `score > 0` (atau beri flag `isValid`) |
| 5 | 🟡 Rendah | Baris 704 | Skor seri: yang lebih lama menang urutan | Tambah tie-break `timestamp` terbaru |
| 6 | 🟡 Rendah | `startGame` baris 429-458 | `hudStatus` & `waveBanner` tidak di-reset | Reset eksplisit saat mulai |
| 7 | 🟡 Rendah | `renderLeaderboard` baris 711 | Bergantung pada `table.nextElementSibling` (rapuh kalau HTML diubah) | Pakai `id` pesan kosong |
| 8 | 🟡 Rendah | `draw` baris 888-902 | `shake` saat Game Over tidak terlihat (tidak ada entity yang digambar) | Pindahkan shake ke level canvas/overlay |
| 9 | 🟡 Rendah | Baris 746, 788, 797 | `shadowBlur` dipanggil per objek tiap frame | Bake glow ke offscreen canvas (H5) |
| 10 | 🟢 Info | HUD & overlay | Tidak ada `aria-live`, fokus, atau opsi *reduced motion* | Lihat G3/G6 |

### 1.4 Kurva kesulitan saat ini (kenapa terasa "datar")

`alienSpeed = min(5.5, 1.8 + (wave-1) × 0.3)`, `spawn = max(0.38, 1.05 - (wave-1) × 0.08)`:

| Wave | Speed (px/frame) | px/detik | Waktu lintas layar | Spawn (detik) | Musuh/detik |
| :-: | :-: | :-: | :-: | :-: | :-: |
| 1 | 1,80 | 108 | 5,6 s | 1,05 | 0,95 |
| 5 | 3,00 | 180 | 3,3 s | 0,73 | 1,37 |
| 10 | 4,50 | 270 | 2,2 s | 0,38 | 2,63 |
| 13 | 5,40 | 324 | 1,9 s | 0,38 | 2,63 |
| 15+ | **5,50 (cap)** | 330 | 1,8 s | **0,38 (cap)** | 2,63 |

**Kesimpulan:** setelah wave ±10, angka sudah mentok. Tidak ada ancaman baru — hanya tumpukan musuh yang sama. Rasa "sulit" datang dari kepadatan, bukan dari tantangan baru. Ini alasan utama game terasa selesai di menit ke-3.

---

## 2. Pilih Arah Dulu (strategi, bukan fitur)

| Arah | Inti | Panjang sesi | Effort | Risiko | Cocok jika |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **A. Arcade Murni** | Poles feel + balancing. Fokus: dodge, combo, akurasi, skor | 2–3 menit | Rendah | Cepat mentok variasi | Ingin lomba skor cepat antar siswa |
| **B. Roguelite Ringan** | Antar wave pilih 1 dari 3 kartu upgrade; build berbeda tiap run | 8–15 menit | Sedang | Skor lama tak sebanding | Ingin *replayability* tinggi, siswa belajar sistem upgrade |
| **C. Stage & Boss** | 5 stage bertema, tiap stage berakhir boss dengan pola | 10–20 menit | Tinggi | Konten harus dibuat manual | Ingin game "lengkap" dengan ending |
| **D. Co-op Lokal** | 1 keyboard 2 pemain (WASD + Arrow), nyawa & skor terpisah | 3–5 menit | Sedang | Balance & layar sempit | Ingin dimainkan berdua di lab komputer |

**Rekomendasi saya: A dulu (1 sesi), lalu B dengan boss tiap 5 wave (gabungan B+C ringan).**
Alasannya: fondasi "feel" belum enak dimainkan; menambah kartu upgrade di atas kontrol yang kaku tidak akan terasa. Setelah A selesai, B memberi alasan main ulang tanpa harus membuat 5 stage manual.

---

## 3. Katalog Ide

Format: **ID — nama** · Dampak/Effort (1–5, makin besar makin tinggi) · catatan implementasi.

### A. Feel & Kontrol (fondasi)

| ID | Ide | D/E | Implementasi |
| :-- | :--- | :-: | :--- |
| A1 | **Gerak vertikal** (↑↓ / W S) + di mobile: **drag kapal** dengan jari di area bawah | 5/2 | Tambah batas `player.y` (60 s/d `H-40`); mobile pakai `pointermove` → target posisi, lerp. Tombol ▲▼ jadi cadangan |
| A2 | **Dash / evade** (Shift atau double-tap arah, cooldown 2 s) | 4/2 | Dorong `player.x` + `vx` selama 0,15 s, `invuln` selama dash, efek afterimage |
| A3 | **I-frame setelah kena** (1,5 s, kapal berkedip) | 5/1 | `invuln` timer; saat `invuln > 0`, lewati deteksi tabrakan; gambar kapal hanya saat `Math.floor(time*20)%2` |
| A4 | **Hitstop + flash + recoil** | 3/1 | Saat kill: `hitstop = 0.05` (dt di-nol-kan), `flash = 0.12` (rect putih alpha), kamera shake kecil |
| A5 | **Combo multiplier** (x1–x8, jendela 2,5 s) | 5/2 | `combo++` per kill, reset jika timer habis; skor `points × mult`; tampilkan besar di HUD |
| A6 | **Bomb / special meter** (X atau tombol) | 4/2 | Isi meter dari kill; saat penuh, ledakan layar + hapus peluru musuh; tombol ke-4 di mobile |
| A7 | **Floating score text** ("+20", "x4!") | 4/1 | Array `texts[]`, naik & fade 0,8 s |
| A8 | **Overheat senjata** (opsional, menambah risiko) | 2/2 | Panas naik saat menembak, jeda 1 s jika penuh — *hanya jika ingin nuansa berbeda* |

### B. Musuh & Tantangan

| ID | Ide | D/E | Implementasi |
| :-- | :--- | :-: | :--- |
| B1 | **Spitter** — musuh berhenti di y≈140 lalu menembak ke arah pemain | 5/2 | Array `enemyBullets[]`; pola: 1 peluru aim, lalu 3 spread; garis "charge" 0,5 s sebelum menembak |
| B2 | **Formation** — musuh muncul dalam formasi V/baris, bukan acak | 4/2 | Fungsi `spawnWaveFormation(type, count)`; tetapkan `baseX` berjarak |
| B3 | **Kamikaze diver** — mengunci `player.x`, lalu melesat | 3/2 | Fase: hover 0,6 s → `vx` menuju pemain → `vy` naik |
| B4 | **Asteroid** — rintangan netral, bisa ditembak, pecah jadi 2 | 3/2 | Entity baru, rotasi, 2 hit; tidak menembak balik |
| B5 | **Shielded alien** — butuh 2 hit, percikan saat armor pecah | 3/1 | `hp = 2`, gambar ring armor, suara berbeda |
| B6 | **Miniboss "Hunter"** di tengah wave | 3/3 | HP 20 + wave×2, pola lurus + dash |
| B7 | **Boss tiap 5 wave** (2–3 fase, weak point, health bar) | 5/4 | Entity `boss` dengan `phase`, `attackTimer`; fase 2 di 66% HP, fase 3 di 33%; panggil minion saat 50% |
| B8 | **Wave modifier** (kabut nebula, hujan meteor, EMP mematikan triple) | 4/3 | Pilih acak tiap wave, tampilkan ikon + teks di banner |
| B9 | **Turret di tepi atas** — menembak lurus ke bawah | 3/2 | Entity statis, hanya muncul wave ≥4 |
| B10 | **Mode kesulitan** (Santai / Normal / Gauntlet) | 3/1 | Objek `DIFFICULTY` berisi `lives`, `speedMul`, `spawnMul`, `scoreMul`; simpan pilihan di LocalStorage |

### C. Struktur Run & Progresi

| ID | Ide | D/E | Implementasi |
| :-- | :--- | :-: | :--- |
| C1 | **Wave clear** — kuota musuh terbatas, lalu jeda 2 s + bonus poin | 4/2 | `waveQuota = min(26, 8 + wave*2)`; spawn berhenti saat kuota habis; banner "WAVE CLEAR +250" |
| C2 | **Kurva kesulitan baru** (lihat §7) | 4/1 | Ganti 2 konstanta + `checkWave` |
| C3 | **Level senjata 1–4** (turun 1 level saat kena) | 4/2 | Level 1: 1 peluru; 2: 2 peluru; 3: triple; 4: triple + piercing; drop "G" dari musuh |
| C4 | **Variasi power-up**: Rapid Fire, Magnet, Extra Life (langka), Score×2 | 4/2 | Tambah `type` di `maybeDropPowerup`, ikon & warna berbeda |
| C5 | **Kartu upgrade antar wave** (pilih 1 dari 3) | 5/4 | Overlay baru `uiUpgrade`; efek contoh: +1 max HP, +15% fire rate, peluru tembus, side gun, magnet, isi bomb |
| C6 | **Recap Game Over** (wave, kill, akurasi, durasi) | 4/1 | Catat `shotsFired`/`shotsHit`; tampilkan tabel kecil di overlay Game Over |
| C7 | **Overtime** setelah boss terakhir: skor ×2 selamanya | 2/1 | Flag `overtime`, kalikan skor |
| C8 | **Redesain skor**: base + combo + akurasi + wave bonus + no-hit bonus | 4/2 | Rumus di `destroyAlien`/`checkWave`; tampilkan rincian di recap |

### D. Meta & Retensi

| ID | Ide | D/E | Implementasi |
| :-- | :--- | :-: | :--- |
| D1 | **Achievements** ("Wave 10", "Tanpa kena", "5000 poin") + toast | 3/2 | Key `arcade_achievements` (array id); toast pojok kanan atas |
| D2 | **Unlock skin/trail kapal** dari milestone | 3/2 | `arcade_unlocks`; gambar varian warna di `drawPlayer` |
| D3 | **Daily challenge** (seed dari tanggal, pola sama untuk semua) | 4/3 | RNG deterministik (H3); board terpisah `arcade_daily_YYYYMMDD` |
| D4 | **Halaman statistik** (total run, total kill, best wave, total waktu) | 3/1 | Key `arcade_stats`; tampil di menu |
| D5 | **Season/weekly reset** untuk leaderboard lokal | 2/1 | Simpan `weekKey`; arsipkan board lama |

### E. Presentasi & Audio

| ID | Ide | D/E | Implementasi |
| :-- | :--- | :-: | :--- |
| E1 | **Musik synth** (bass arpeggio + hi-hat), tempo naik per wave | 4/3 | Sequencer `setInterval`/lookahead dengan `beep()`; toggle terpisah dari SFX |
| E2 | **Tema tiap 5 wave** (Deep Space → Nebula → War Zone) | 4/2 | Ganti `BG_GRADIENT` + palet musuh; transisi fade 0,5 s |
| E3 | **Parallax nebula** (3 lapis bintang + kabut) | 3/1 | Array lapis dengan kecepatan & alpha berbeda |
| E4 | **Baked glow sprite** (perf mobile) | 3/2 | Render tiap tipe ke `document.createElement('canvas')` sekali, lalu `drawImage` |
| E5 | **Toggle CRT/scanline** | 2/1 | Overlay CSS `repeating-linear-gradient` |
| E6 | **Transisi layar** (fade/wipe menu ↔ game) | 2/1 | Class CSS `fade` + `transition` |
| E7 | **Slow-mo saat boss mati / nyawa terakhir** | 2/1 | `dt *= 0.35` selama 0,8 s |
| E8 | **Haptics** `navigator.vibrate(30)` saat kena | 1/1 | Cek `navigator.vibrate` (iOS tidak mendukung) |

### F. Sosial & Leaderboard

| ID | Ide | D/E | Implementasi |
| :-- | :--- | :-: | :--- |
| F1 | **Board per kesulitan** + tab filter | 3/1 | Key `arcade_leaderboard_<mode>` |
| F2 | **Share kartu skor** (canvas → PNG / `navigator.share`) | 3/2 | Gambar 600×400 berisi nama, skor, wave; tombol "Bagikan" |
| F3 | **Board daily challenge** | 3/1 | Bergantung D3 |
| F4 | **Leaderboard online kelas** | 4/5 | Lihat tabel trade-off di bawah |
| F5 | **Ghost replay** (rekam input, putar ulang) | 2/5 | Butuh RNG deterministik + rekaman frame; proyek lanjutan |

**Pilihan leaderboard online (jujur soal konsekuensinya):**

| Opsi | Biaya | Backend | Risiko | Verdict |
| :--- | :--- | :--- | :--- | :--- |
| Lokal (sekarang) | Gratis | Tidak | Per-browser | ✅ tetap jadi default |
| Share kartu gambar | Gratis | Tidak | Tidak kompetitif | ✅ tambahkan (F2) |
| Kode skor manual (nama+skor) | Gratis | Tidak | Mudah dipalsukan | ⚠️ hanya untuk lomba offline |
| GitHub Gist/Issue API | Gratis | Semi | Token di client = celah penyalahgunaan | ❌ tidak disarankan |
| Supabase/Firebase | Gratis (tier) | Ya | Key publik, perlu aturan akses | ⚠️ proyek lanjutan terpisah |
| Google Apps Script Web App | Gratis | Ya | Setup tambahan | ✅ paling masuk akal untuk "board kelas" |

### G. UX & Aksesibilitas

| ID | Ide | D/E | Implementasi |
| :-- | :--- | :-: | :--- |
| G1 | **Tutorial first-run** (3 langkah bergambar di canvas) | 4/1 | Flag `arcade_tutorial_done`; overlay sederhana |
| G2 | **Pause = pusat pengaturan** (SFX, musik, motion, kesulitan) | 4/2 | Perluas `uiPause` |
| G3 | **Reduced motion + palet colorblind** | 3/2 | Cek `prefers-reduced-motion`, ganti palet merah/hijau |
| G4 | **Touch lebih nyaman**: drag kapal, tombol lebih besar, safe-area | 4/2 | `env(safe-area-inset-bottom)`, `pointerdown` di canvas |
| G5 | **Enter = mulai, R = ulang** | 1/1 | Tambah di handler keyboard |
| G6 | **ARIA**: `aria-live` untuk skor, `role="dialog"` overlay | 2/1 | Atribut HTML saja |

### H. Teknis (tanpa melanggar aturan 1 file)

| ID | Ide | D/E | Implementasi |
| :-- | :--- | :-: | :--- |
| H1 | **Objek `TUNING`** untuk semua angka balance | 3/1 | Satu objek di blok 1; semua konstanta dibaca dari sini |
| H2 | **Fixed timestep + sub-step collision** | 3/2 | Lihat snippet di bawah; mengatasi bug #3 |
| H3 | **RNG deterministik (mulberry32)** | 2/1 | Untuk daily challenge & replay |
| H4 | **Pooling & cap partikel** (maks 300) | 2/2 | Array pool, hindari alokasi per frame |
| H5 | **Sprite cache** offscreen | 3/2 | Lihat E4 |
| H6 | **Versi skema save + migrasi** | 2/1 | `arcade_schema: 2`, bungkus `JSON.parse` dengan validasi |
| H7 | **Debug overlay FPS/entity** (tombol `~`) | 1/1 | `requestAnimationFrame` counter |
| H8 | **PWA/offline** | 3/3 | Butuh service worker = file tambahan; **melanggar aturan 1 file** → tunda/skip |

**Snippet kunci:**

```js
// H2 — sub-step collision (mencegah peluru menembus musuh saat FPS drop)
const dist = Math.max(Math.abs(b.vy * s), Math.abs(b.vx * s));
const steps = Math.max(1, Math.ceil(dist / 8));
for (let k = 0; k < steps; k++) {
  b.x += (b.vx * s) / steps;
  b.y -= (b.vy * s) / steps;
  if (hitsAnyAlien(b)) { /* hancurkan */ break; }
}
```

```js
// H3 — RNG deterministik untuk daily challenge
function mulberry32(a) {
  return function () {
    a |= 0; a = a + 0x6D2B79F5 | 0;
    let t = Math.imul(a ^ a >>> 15, 1 | a);
    t = t + Math.imul(t ^ t >>> 7, 61 | t) ^ t;
    return ((t ^ t >>> 14) >>> 0) / 4294967296;
  };
}
const dailySeed = Number(new Date().toISOString().slice(0, 10).replace(/-/g, ''));
const rng = mulberry32(dailySeed);
```

```js
// A5 — combo
comboTimer = Math.max(0, comboTimer - dt);
if (comboTimer === 0) combo = 0;
// saat kill:
combo++; comboTimer = 2.5;
const mult = Math.min(8, 1 + Math.floor(combo / 5));
score += points * mult;
```

---

## 4. Matriks Prioritas

Dampak (D) dan Effort (E) skala 1–5. **Rasio = D/E** — makin tinggi, makin layak dikerjakan lebih dulu.

| Prioritas | ID | Ide | D | E | Rasio |
| :-: | :-- | :--- | :-: | :-: | :-: |
| 1 | A3 | I-frame setelah kena | 5 | 1 | **5,0** |
| 2 | A1 | Gerak vertikal + drag mobile | 5 | 2 | **2,5** |
| 3 | A5 | Combo multiplier | 5 | 2 | **2,5** |
| 4 | B1 | Musuh penembak (Spitter) | 5 | 2 | **2,5** |
| 5 | C6 | Recap Game Over | 4 | 1 | **4,0** |
| 6 | C2 | Kurva kesulitan baru | 4 | 1 | **4,0** |
| 7 | G1 | Tutorial first-run | 4 | 1 | **4,0** |
| 8 | A6 | Bomb/special meter | 4 | 2 | **2,0** |
| 9 | C1 | Wave clear + bonus | 4 | 2 | **2,0** |
| 10 | C3 | Level senjata | 4 | 2 | **2,0** |
| 11 | B7 | Boss tiap 5 wave | 5 | 4 | 1,25 |
| 12 | C5 | Kartu upgrade | 5 | 4 | 1,25 |
| 13 | E1 | Musik synth | 4 | 3 | 1,33 |
| 14 | D3 | Daily challenge | 4 | 3 | 1,33 |
| 15 | F4 | Leaderboard online | 4 | 5 | 0,8 |

---

## 5. Rencana Rilis Bertahap (usulan)

### v0.6.0 — "Feel & Fairness" (1 sesi, tanpa fitur besar)
- [ ] Fix bug #1, #2, #3 (multi-life, i-frame, tunneling)
- [ ] A1 gerak vertikal + drag mobile, A3 i-frame, A4 hitstop/flash
- [ ] A5 combo + A7 floating text
- [ ] C6 recap Game Over, HUD progress kill ke wave berikutnya
- [ ] G1 tutorial singkat, G5 tombol Enter/R
- [ ] H1 `TUNING` + C2 kurva baru
- **Target rasa:** 1 run 2–3 menit terasa adil, setiap kena ada sebabnya, skor naik karena main bagus.

### v0.7.0 — "Ancaman Nyata"
- [ ] B1 Spitter + `enemyBullets`, B2 formation, B3 diver, B4 asteroid
- [ ] A6 bomb, C1 wave clear + bonus
- [ ] B10 mode kesulitan
- **Target rasa:** pemain harus bergerak di 2 sumbu dan menghindari tembakan, bukan hanya menembak ke atas.

### v0.8.0 — "Boss & Build"
- [ ] B7 boss tiap 5 wave (2–3 fase, health bar)
- [ ] C3 level senjata, C4 variasi power-up, C5 kartu upgrade
- [ ] D1 achievements, D2 unlock skin, D4 statistik
- **Target rasa:** ada puncak, ada pilihan build, ada alasan main lagi.

### v0.9.0 — "Presentasi"
- [ ] E1 musik, E2 tema per 5 wave, E3 parallax, E4/E5 performa & CRT
- [ ] G2 pengaturan, G3 reduced motion/colorblind, F1 board per mode
- [ ] F2 share kartu skor

### v1.0.0 — "Kompetisi"
- [ ] D3 daily challenge + F3 board harian
- [ ] D5 season lokal
- [ ] (opsional) F4 board kelas via Apps Script sebagai proyek terpisah

---

## 6. Quick Wins (masing-masing ≤ 30 menit)

1. I-frame 1,2 s + kapal berkedip.
2. Hentikan loop alien setelah 1 nyawa hilang (fix bug #1).
3. Floating text "+20" saat kill.
4. Combo counter di HUD.
5. Reset `hudStatus`/`waveBanner` di `startGame`.
6. Tampilkan "7/20" (kill menuju wave berikutnya) di HUD.
7. Jangan simpan skor 0 ke leaderboard.
8. Bonus poin saat wave clear.
9. Recap wave/kill/akurasi di Game Over.
10. Tombol "Main Lagi" di menu pause.
11. Enter = mulai, R = restart.
12. Flash merah singkat saat kehilangan nyawa.
13. `preventDefault` contextmenu & `pointercancel` pada tombol sentuh.
14. Bunyi peringatan saat nyawa = 1.
15. Garis "rekor terbaik" di layar game.

---

## 7. Usulan Angka Tuning

| Parameter | Sekarang | Usulan | Alasan |
| :--- | :--- | :--- | :--- |
| Kecepatan horizontal | 5 px/frame | 5,5 | Sedikit lebih responsif |
| Kecepatan vertikal | — | 4,5 | Vertikal tidak boleh lebih cepat dari horizontal |
| I-frame | 0 s | 1,5 s | Cegah kematian beruntun |
| Nyawa | 3 | 3 (Normal) / 5 (Santai) / 2 (Gauntlet) | Mode kesulitan |
| Combo window | — | 2,5 s | Cukup ketat tapi adil |
| Combo maks | — | x8 | Batasi inflasi skor |
| Skor grunt / UFO | 20 / 50 | 25 / 60 | Kompensasi combo |
| Skor Spitter / Diver / Asteroid | — | 40 / 30 / 15 | Baru |
| Kuota wave | 10 kill, kumulatif | 8 + wave×2 (maks 26) | Ritme jelas per wave |
| Kecepatan musuh | 1,8 + 0,3/wave (cap 5,5) | 1,6 + 0,22/wave (cap 4,6) | Naik lebih halus |
| Interval spawn | 1,05 − 0,08/wave (min 0,38) | maks(0,45; 1,1 − 0,06×wave) | Tidak menumpuk berlebihan |
| Drop power-up | 9% / 70% | 8% / 60% / 100% boss | Lebih terkontrol |
| Bomb | — | 30 kill atau 2.500 poin | Satu kali per run awal |
| HP boss | — | 60 + wave×8 | Skala dengan progres |

---

## 8. Yang Sebaiknya Dihindari

- ❌ Menambah 5 mekanik sekaligus tanpa sesi *tuning* — hasilnya sulit dinilai.
- ❌ Framework/bundler/CDN eksternal — melanggar aturan single-file & zero-cache.
- ❌ Leaderboard online dengan token/secret di sisi client.
- ❌ Mengubah skema skor tanpa penjelasan — skor lama jadi tidak sebanding. Solusi: beri versi (`v: 2`) atau reset season.
- ❌ Efek `shadowBlur` berlebihan di banyak objek — berat di HP kelas bawah.
- ❌ PWA/service worker — butuh file tambahan, di luar aturan 1 file.
- ❌ Menyalin mekanik game lain tanpa menyesuaikan kontrol mobile.

---

## 9. Keputusan yang Perlu Diambil (oleh siswa/guru)

1. Arah utama: **A**, **B**, **C**, atau **D**? (rekomendasi: A → B)
2. Target pengalaman: lomba skor 3 menit, atau sesi santai 10–15 menit?
3. Apakah butuh leaderboard antar siswa (online), atau cukup share kartu skor?
4. Tema visual: tetap neon biru, atau berubah per 5 wave?
5. Apakah skor lama perlu dipertahankan saat sistem skor berubah?

---

## 10. Langkah Berikutnya

1. Pilih arah (Bagian 2) + 3–5 ide dari Bagian 4.
2. Ide terpilih dipindahkan ke `PLAN.md` sebagai **Phase 7** dengan checkbox.
3. Implementasi per paket (v0.6 → v0.9), setiap paket: kode → `PLAN.md` → commit → push → tes live.
4. Setelah tiap paket, mainkan 3 run dan catat: wave tercapai, skor, dan apakah "kena" terasa adil.
