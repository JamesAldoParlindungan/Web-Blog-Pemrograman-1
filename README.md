# Web-Blog-Pemrograman-1
Website biografi digital menggunakan HTML, CSS, dan JavaScript.

# DOKUMENTASI PORTOFOLIO — JAMES ALDO PARLINDUNGAN

Dokumentasi teknis untuk halaman portofolio pribadi James Aldo Parlindungan. Dokumen ini berfokus pada implementasi HTML, CSS, dan JavaScript yang membentuk halaman profil James, termasuk konten, tampilan visual, interaksi, animasi, dan responsivitas.

## Struktur File yang Didokumentasikan

| File | Peran |
|------|-------|
| `template/james.html` | Struktur dan konten halaman portofolio James |
| `style/james.css` | Tampilan visual, layout, warna, tipografi, efek hover, animasi, dan responsive layout |
| `script/james.js` | Interaksi halaman, preloader, cursor khusus, navigasi aktif, reveal, marquee, dan efek lainnya |
| `assets/james.jpeg` | Foto profil yang ditampilkan pada halaman James |

---

# 🔷 BAGIAN 1: JAVASCRIPT (`script/james.js`)

## 1.1 — Daftar Interaksi per Bagian (dengan Line Number)

### 🔹 00. SETUP & DETEKSI PREFERENSI (Line 1–9)

| Interaksi | Fungsi/Variabel | Line | Deskripsi |
|-----------|-----------------|------|-----------|
| Pembungkus script | IIFE | 5 | Menjalankan kode dalam fungsi langsung agar variabel lokal tidak bocor ke global scope |
| Strict mode | `"use strict"` | 6 | Mengaktifkan mode JavaScript yang lebih ketat |
| Deteksi reduced motion | `prefersReduced` | 8 | Membaca preferensi `prefers-reduced-motion: reduce` dari sistem pengguna |
| Deteksi pointer desktop | `isFinePointer` | 9 | Memeriksa media query hover dan pointer presisi untuk efek cursor |

**Cara kerja:**
- Semua logika dibungkus dalam IIFE (`(function () { ... })()`), sehingga variabel dan fungsi tetap berada dalam scope script.
- `prefersReduced` dipakai untuk mengurangi atau melewati animasi tertentu jika pengguna memilih mengurangi gerakan.
- `isFinePointer` membatasi custom cursor dan efek magnetik agar tidak dijalankan pada perangkat yang tidak cocok, misalnya layar sentuh.

---

### 🔹 01. TRANSISI KE HALAMAN UTAMA (Line 11–51)

| Interaksi | Fungsi/Variabel | Line | Deskripsi |
|-----------|-----------------|------|-----------|
| Fungsi transisi | `triggerPageTransition(url)` | 11 | Membuat overlay loading sebelum berpindah URL |
| Cek loader lama | `existing` | 12–13 | Menghapus elemen `#preloader` yang sudah ada agar tidak ada duplikasi |
| Membuat loader | `loader` | 15–22 | Membuat markup loader dengan logo, progress bar, persentase, dan label |
| Referensi progress | `preFill`, `prePct` | 24–25 | Mengambil elemen fill dan counter dari loader yang baru dibuat |
| Timer progress | `run` | 28–38 | Menaikkan counter secara bertahap dan menghentikan interval saat mencapai 100% |
| Target link kembali | `backHomeLink` | 41 | Memilih link header yang mengarah ke `../index.html` |
| Event klik link | `addEventListener("click")` | 43–50 | Menghentikan navigasi langsung dan menjalankan transisi |

**Cara kerja `triggerPageTransition(url)`:**
- Jika ada elemen `#preloader` yang tersisa, elemen tersebut dihapus lebih dulu.
- JavaScript membuat loader berisi logo `JAMES.ALDO`, progress bar, counter persentase, dan teks `LOADING_PORTFOLIO.EXE`.
- Nilai `pct` bertambah 12 setiap 60 ms sampai maksimum 100.
- Lebar `#pre-fill` dan teks `#pre-pct` diperbarui bersamaan.
- Saat persentase mencapai 100%, interval dihentikan. Navigasi ke URL tujuan dilakukan setelah 250 ms, atau tanpa jeda tambahan jika `prefersReduced` aktif.

**Cara kerja link “Kembali ke Beranda”:**
- Selector yang dipakai adalah `header nav > a[href="../index.html"]`.
- `event.preventDefault()` mencegah browser langsung berpindah halaman.
- Jika reduced motion aktif, browser langsung menuju `../index.html`.
- Jika tidak, `triggerPageTransition("../index.html")` dipanggil.

---

### 🔹 02. PRELOADER HALAMAN PROFIL (Line 53–98)

| Interaksi | Fungsi/Variabel | Line | Deskripsi |
|-----------|-----------------|------|-----------|
| Membuat elemen | `preloader` | 56–63 | Membuat overlay loading ketika halaman profil diinisialisasi |
| Progress bar | `preFill` | 65 | Referensi ke elemen `#pre-fill` |
| Counter | `prePct` | 66 | Referensi ke elemen `#pre-pct` |
| Nilai progress | `pct` | 67 | Menyimpan angka progress saat ini |
| Waktu mulai | `startTime` | 68 | Merekam waktu awal menggunakan `performance.now()` |
| Durasi minimum | `MIN_TIME` | 69 | Nilai 850 ms agar loader tidak terlalu cepat menghilang |
| Batas maksimum | `MAX_TIME` | 70 | Nilai 2200 ms sebagai batas keselamatan |
| Update counter | `setPct(v)` | 72–76 | Membatasi nilai sampai 100 dan memperbarui tampilan |
| Interval loading | `tick` | 78–82 | Menaikkan progress setiap 60 ms dengan laju bertahap |
| Penyelesaian loading | `finish()` | 84–95 | Menghentikan interval, mengatur 100%, dan menutup loader |
| Fallback maksimum | `setTimeout(finish, MAX_TIME)` | 97 | Memanggil penyelesaian jika proses loading terlalu lama |
| Event `load` | `window.addEventListener("load")` | 98 | Mengatur progress menjadi 95 jika seluruh window selesai dimuat sebelum counter penuh |

**Cara kerja:**
- Loader dibuat secara dinamis dan ditempatkan di awal `body` menggunakan `prepend()`.
- `setPct(v)` membulatkan nilai ke bilangan bulat, membatasi maksimum 100, lalu memperbarui lebar progress bar dan angka persen.
- Interval menaikkan progress lebih cepat saat nilainya di bawah 70, lebih lambat antara 70–90, dan perlahan setelah 90.
- Ketika progress mencapai 100%, `finish()` menghentikan interval dan menghitung waktu yang sudah berlalu.
- `wait = Math.max(0, MIN_TIME - elapsed)` menjaga durasi minimum loader. Setelah jeda tambahan 120 ms, class `done` ditambahkan ke loader dan class `loaded` ke `body`.
- Setelah transisi selesai, elemen preloader dihapus melalui event `transitionend` dengan opsi `{ once: true }`.
- `startHeroWords()` dipanggil setelah loader mulai ditutup agar animasi kata hero dimulai pada waktu yang sesuai.
- `setTimeout(finish, MAX_TIME)` berfungsi sebagai fallback; pemanggilan `finish()` menghentikan interval yang sama.

**Nilai penting:**
| Konstanta | Nilai | Keterangan |
|-----------|-------|------------|
| `MIN_TIME` | `850` ms | Durasi minimum loader |
| `MAX_TIME` | `2200` ms | Batas maksimum fallback |
| Interval `tick` | `60` ms | Frekuensi update progress |
| Jeda penutup | `120` ms | Jeda tambahan setelah waktu minimum dihitung |

---

### 🔹 03. CUSTOM CURSOR + MAGNETIC CTA (Line 100–153)

| Interaksi | Fungsi/Variabel | Line | Deskripsi |
|-----------|-----------------|------|-----------|
| Kondisi aktivasi | `if (isFinePointer && !prefersReduced)` | 103 | Custom cursor hanya aktif pada pointer presisi dan jika reduced motion tidak aktif |
| Membuat dot | `dot` | 104–105 | Titik kursor kecil dengan class `.cursor-dot` |
| Membuat ring | `ring` | 106–109 | Lingkaran luar dengan label `LIHAT` |
| Posisi pointer | `mx`, `my` | 111 | Menyimpan posisi mouse terakhir |
| Posisi ring | `rx`, `ry` | 112 | Menyimpan posisi ring yang bergerak mengejar pointer |
| Gerak dot | Event `mousemove` | 114–117 | Memindahkan dot ke koordinat pointer |
| Loop ring | `loop()` | 119–124 | Memperbarui posisi ring menggunakan interpolasi setiap frame |
| Selector hover | `HOVER_SEL` | 127 | Menentukan elemen yang memperbesar ring |
| State hover | `is-hover` | 128–141 | Menambahkan atau menghapus class ring saat pointer masuk/keluar target |
| State lihat | `is-view` | 132–135 | Membesarkan ring lebih jauh pada foto profil dan kartu prestasi |
| Target magnet | `#profil a, header nav > a` | 144 | Memilih CTA hero dan link kembali ke beranda |
| Hitung jarak | `dx`, `dy` | 146–148 | Menghitung posisi pointer relatif terhadap pusat elemen |
| Efek magnet | `style.transform` | 149 | Menggeser elemen mengikuti pointer dengan faktor 0.18 |
| Reset magnet | `mouseleave` | 151 | Menghapus transform ketika pointer meninggalkan elemen |

**Cara kerja cursor:**
- JavaScript membuat dua elemen: dot dan ring, lalu memasukkannya ke `body`.
- Dot mengikuti pointer secara langsung lewat koordinat `clientX` dan `clientY`.
- Ring menggunakan rumus interpolasi: posisi ring ditambah 16% dari selisih antara pointer dan ring pada setiap frame.
- `requestAnimationFrame()` menjaga gerakan ring tetap diperbarui mengikuti frame browser.
- Saat pointer berada di atas elemen yang cocok dengan `HOVER_SEL`, ring mendapat class `is-hover`.
- Jika target berada di dalam `#profil figure` atau `#prestasi article`, class `is-view` ditambahkan agar ring masuk ke state tampilan khusus.
- Ketika pointer keluar dari seluruh elemen target, kedua class dihapus.

**Selector hover (`HOVER_SEL`):**
```js
"a, button, li, article, figure, h2, h3, #profil figcaption"
```

**Cara kerja magnetic CTA:**
- Efek hanya diterapkan pada link di dalam `#profil` dan link langsung di dalam navigasi header.
- Pusat elemen dihitung melalui `getBoundingClientRect()`.
- Selisih koordinat pointer terhadap pusat elemen dikalikan `0.18`, lalu dipakai untuk `translate(x, y)`.
- Saat `mouseleave`, inline transform dikosongkan sehingga elemen kembali ke posisi stylesheet.

**State visual cursor dari CSS:**
| State | Ukuran ring | Efek |
|-------|-------------|------|
| Default | `38 × 38px` | Outline warna lime |
| `.is-hover` | `76 × 76px` | Latar lime transparan dan border pink |
| `.is-view` | `96 × 96px` | Latar lime, border hitam, label `LIHAT` terlihat |

> ⚠️ CSS menyembunyikan cursor asli pada perangkat `hover:hover` dan `pointer:fine`. JavaScript juga mengecek reduced motion sebelum menjalankan cursor khusus.

---

### 🔹 04. ANIMASI KATA HERO (Line 155–174)

| Interaksi | Fungsi/Variabel | Line | Deskripsi |
|-----------|-----------------|------|-----------|
| Ambil heading | `heroTitle` | 158 | Memilih `#profil h1` |
| Status persiapan | `wordsArmed` | 159 | Mencegah teks hero diproses lebih dari sekali |
| Persiapan kata | `armHeroWords()` | 161–168 | Memecah heading menjadi kata dan membungkus tiap kata dengan span |
| Mulai animasi | `startHeroWords()` | 170–173 | Memastikan animasi berjalan setelah preloader selesai |
| Reduced motion | `if (prefersReduced) armHeroWords()` | 174 | Menyiapkan struktur kata tanpa memanggil animasi awal dari loader |

**Cara kerja:**
- Script memilih heading utama dengan selector `#profil h1`.
- Jika heading tidak ditemukan atau kata sudah disiapkan, fungsi langsung berhenti.
- Isi heading dibersihkan dengan `trim()`, lalu dipecah berdasarkan satu atau lebih whitespace menggunakan `/\s+/`.
- Setiap kata dibungkus dengan `.word` dan span bagian dalam yang memiliki `animation-delay` bertahap.
- Delay tiap kata dihitung dari `0.08 * i + 0.1` detik.
- CSS memulai setiap kata dari `translateY(110%)` lalu menggerakkannya ke posisi normal dengan animasi `wordUp`.
- Jika reduced motion aktif, `startHeroWords()` berhenti tanpa menjalankan langkah animasi; `armHeroWords()` tetap dipanggil saat inisialisasi untuk menyiapkan struktur HTML.

---

### 🔹 05. SCROLL PROGRESS BAR & HEADER STATE (Line 176–197)

| Interaksi | Fungsi/Variabel | Line | Deskripsi |
|-----------|-----------------|------|-----------|
| Membuat progress | `progress` | 179–181 | Membuat indikator scroll `#scroll-progress` |
| Referensi header | `header` | 182 | Mengambil elemen `header` |
| Pengunci frame | `ticking` | 184 | Mencegah update scroll dijalankan berkali-kali dalam satu frame |
| Handler scroll | `onScroll()` | 185–196 | Menjadwalkan pembaruan progress dan header |
| Ukuran halaman | `h` | 189 | Mengambil `document.documentElement` |
| Jarak scroll maksimum | `max` | 190 | Menghitung `scrollHeight - clientHeight` |
| Lebar progress | `progress.style.width` | 191 | Mengubah posisi scroll menjadi persentase lebar |
| State header | `header.classList.toggle()` | 192 | Menambah class `scrolled` saat scroll lebih dari 10px |
| Update active nav | `updateActiveNav()` | 193 | Memanggil placeholder update nav; active nav aktual dikendalikan observer |
| Event scroll | `window.addEventListener()` | 197 | Memanggil handler saat pengguna menggulir halaman |

**Cara kerja:**
- `onScroll()` segera berhenti jika `ticking` masih `true`.
- Jika belum ada update yang menunggu, `ticking` diubah menjadi `true` dan pembaruan dijadwalkan dengan `requestAnimationFrame()`.
- Persentase progress dihitung dari `scrollTop / (scrollHeight - clientHeight) × 100`.
- Jika halaman tidak memiliki area scroll tambahan (`max <= 0`), progress ditetapkan ke 0.
- Header mendapatkan class `scrolled` saat `scrollTop > 10`, sehingga CSS dapat mengganti warna border bawahnya.
- Setelah update selesai, `ticking` direset menjadi `false`.
- Listener scroll memakai `{ passive: true }`.

---

### 🔹 06. ACTIVE NAVIGATION / SCROLLSPY (Line 199–221)

| Interaksi | Fungsi/Variabel | Line | Deskripsi |
|-----------|-----------------|------|-----------|
| Kumpulkan link nav | `navLinks` | 202 | Mengambil link navigasi section dari header |
| Peta section ke link | `sectionMap` | 203–205 | Membuat pemetaan ID section ke elemen link |
| Daftar section | `observed` | 206–208 | Mengubah ID pada link menjadi elemen section yang tersedia |
| Observer | `sectionObserver` | 210–219 | Memantau section yang memasuki area pengamatan viewport |
| Hapus active lama | `classList.remove("active")` | 213 | Menghapus state aktif dari semua link nav |
| Tandai active baru | `link.classList.add("active")` | 214–216 | Menambahkan class aktif ke link yang cocok dengan section terlihat |
| Observer start | `sectionObserver.observe(s)` | 219 | Mulai mengamati setiap section yang ditemukan |
| Placeholder update | `updateActiveNav()` | 221 | Fungsi kosong; observer menangani state aktif |

**Cara kerja:**
- Semua link pada `header nav ul li a` dikumpulkan menjadi array.
- `sectionMap` menggunakan fragmen `href` tanpa karakter `#` sebagai key, misalnya `profil` atau `tentang`.
- ID yang tidak punya elemen section valid akan dibuang dari daftar `observed`.
- `IntersectionObserver` memakai `rootMargin: "-40% 0px -55% 0px"`, sehingga area pengamatan dipersempit ke bagian tengah viewport.
- Saat observer mendeteksi section yang intersect, semua link kehilangan class `active`, lalu link yang sesuai dengan ID target diberi class tersebut.
- Fungsi `updateActiveNav()` sengaja kosong karena komentar kode menyebut observer sebagai pemegang logika navigasi aktif.

---

### 🔹 07. REVEAL SAAT SCROLL (Line 223–240)

| Interaksi | Fungsi/Variabel | Line | Deskripsi |
|-----------|-----------------|------|-----------|
| Kondisi animasi | `if (!prefersReduced)` | 226 | Reveal hanya diinisialisasi jika reduced motion tidak aktif |
| Target reveal | `targets` | 227–229 | Memilih paragraf, heading, article, figure profil, dan div langsung di profil |
| Class awal | `classList.add("reveal")` | 230 | Menambahkan class awal untuk efek slide |
| Observer reveal | `io` | 231–239 | Memantau elemen saat memasuki viewport |
| State terlihat | `classList.add("in")` | 234 | Mengembalikan elemen ke posisi normal |
| Berhenti mengamati | `io.unobserve()` | 235 | Mencegah target yang sudah muncul diproses berulang kali |
| Ambang observer | `threshold: 0.12` | 238 | Sekitar 12% area elemen harus terlihat untuk memicu callback |

**Selector target reveal:**
```css
section > p, section > h2, section > article, #profil figure, #profil > div
```

**Cara kerja:**
- Target dipilih dari struktur HTML yang spesifik, bukan semua elemen halaman.
- Setiap target diberi class `.reveal`, yang pada CSS memindahkan elemen 34px ke bawah.
- Saat 12% elemen terlihat, class `.in` ditambahkan untuk mengembalikannya ke posisi `translateY(0)`.
- Setelah muncul, elemen berhenti diamati dengan `unobserve()`.
- Tidak ada pengaturan opacity pada efek ini; sesuai komentar kode, reveal memakai gerakan slide saja.

---

### 🔹 08. MARQUEE / STRIP TEKS BERJALAN (Line 242–255)

| Interaksi | Fungsi/Variabel | Line | Deskripsi |
|-----------|-----------------|------|-----------|
| Daftar frasa | `phrases` | 245–248 | Menyimpan kata kunci yang ditampilkan pada strip berjalan |
| Membuat container | `marquee` | 249–251 | Membuat elemen `.marquee` dan menandainya `aria-hidden="true"` |
| Membentuk sequence | `seq` | 252 | Mengubah setiap frasa menjadi elemen `<span>` |
| Menggandakan sequence | `marquee.innerHTML` | 253 | Memasukkan dua salinan sequence ke `.marquee-track` |
| Menentukan posisi | `hero` | 254 | Mengambil section `#profil` |
| Menyisipkan marquee | `insertAdjacentElement()` | 255 | Menempatkan marquee tepat setelah hero |

**Isi marquee:**
`DATA SCIENCE` · `PYTHON` · `SQL` · `MACHINE LEARNING` · `PCA` · `GPR` · `DJANGO` · `PANDAS` · `NUMPY` · `ANALISIS DATA` · `★`

**Cara kerja:**
- Frasa disimpan sebagai array JavaScript.
- Setiap item diubah menjadi span, kemudian seluruh item digabung menjadi string HTML.
- Sequence digandakan dua kali untuk membentuk strip yang dapat bergerak secara berulang.
- Container diberi `aria-hidden="true"` karena strip ini berfungsi sebagai elemen dekoratif.
- Marquee disisipkan setelah `#profil`, bukan ditulis langsung di file HTML.
- CSS menjalankan animasi horizontal selama 18 detik dan menghentikannya sementara saat pointer hover di atas marquee.

---

### 🔹 09. NOMOR SECTION OTOMATIS (Line 257–264)

| Interaksi | Fungsi/Variabel | Line | Deskripsi |
|-----------|-----------------|------|-----------|
| Ambil section | `sections` | 260 | Mengambil semua `main section` |
| Loop urutan | `forEach((s, i) => ...)` | 261 | Memproses section berdasarkan urutan DOM |
| Cari heading | `h2` | 262 | Mencari heading `h2` di tiap section |
| Set nomor | `data-index` | 263 | Menetapkan teks seperti `01 /`, `02 /`, dan seterusnya |

**Cara kerja:**
- JavaScript mengambil seluruh section di dalam `main`.
- Nomor berasal dari indeks urutan section, ditambah satu, dengan awalan `0`.
- Jika section tidak memiliki `h2`, penomoran untuk section tersebut dilewati.
- CSS pseudo-element `section > h2::before` membaca atribut `data-index` menggunakan `content: attr(data-index)`.

---

### 🔹 10. EASTER EGG — KETIK `DATA` (Line 266–281)

| Interaksi | Fungsi/Variabel | Line | Deskripsi |
|-----------|-----------------|------|-----------|
| Buffer input | `buffer` | 269 | Menyimpan empat karakter terakhir yang diketik |
| Event keyboard | `keydown` | 270 | Mendengarkan input keyboard di window |
| Filter karakter | `e.key.length !== 1` | 271 | Mengabaikan tombol yang bukan karakter tunggal |
| Simpan karakter | `buffer = ...slice(-4)` | 272 | Mengubah input menjadi huruf kapital dan menyimpan maksimal empat karakter |
| Cek pemicu | `buffer === "DATA"` | 273 | Menjalankan easter egg ketika urutan karakter tepat `DATA` |
| Ubah warna | `setProperty()` | 274–275 | Menukar nilai warna `--acid` dan `--pink` |
| Reset warna | `setTimeout()` | 276–279 | Mengembalikan warna setelah 2500 ms |

**Cara kerja:**
- Script hanya menerima `e.key` yang panjangnya satu karakter.
- Input diubah ke huruf kapital sehingga `data`, `Data`, dan `DATA` sama-sama bisa memicu easter egg.
- Buffer hanya menyimpan empat karakter terakhir.
- Ketika buffer sama dengan `DATA`, `--acid` berubah menjadi cyan (`#00e5ff`) dan `--pink` berubah menjadi lime (`#c8ff00`).
- Setelah 2,5 detik, nilai `--acid` dikembalikan ke `#c8ff00` dan `--pink` ke `#ff2e88`.
- Listener tidak memfilter fokus input/textarea; pemicu mengikuti rangkaian tombol karakter yang diterima oleh `window`.

---

### 🔹 11. SMOOTH ANCHOR NAVIGATION (Line 283–296)

| Interaksi | Fungsi/Variabel | Line | Deskripsi |
|-----------|-----------------|------|-----------|
| Pilih anchor | `querySelectorAll('a[href^="#"]')` | 286 | Memilih semua link yang `href`-nya dimulai dengan `#` |
| Event klik | `addEventListener("click")` | 287 | Menangani klik anchor |
| Ambil ID | `getAttribute("href")` | 288 | Membaca tujuan navigasi dari atribut `href` |
| Validasi ID | `id.length < 2` | 289 | Melewati link dengan `href="#"` |
| Cari target | `document.querySelector(id)` | 290–291 | Mencari elemen tujuan; berhenti jika tidak ditemukan |
| Cegah default | `event.preventDefault()` | 292 | Menghentikan lompatan anchor standar browser |
| Hitung posisi | `top` | 293 | Menghitung posisi target dan mengurangi offset 84px |
| Scroll | `window.scrollTo()` | 294 | Menggulir ke target dengan behavior sesuai reduced motion |

**Cara kerja:**
- Semua anchor internal di halaman ditangani oleh satu listener per link.
- Link dengan `href="#"` dilewati karena panjang ID kurang dari dua karakter.
- Jika target tidak ditemukan, script membiarkan event berjalan tanpa intervensi tambahan.
- Posisi scroll dihitung dari posisi elemen relatif terhadap viewport ditambah `window.scrollY`, lalu dikurangi 84px.
- Perilaku scroll menggunakan `smooth` kecuali jika reduced motion aktif, saat itu behavior menjadi `auto`.

---

### 🔹 12. INISIALISASI SCROLL PERTAMA (Line 298)

| Fungsi | Line | Deskripsi |
|--------|------|-----------|
| `onScroll()` | 298 | Menjalankan perhitungan awal untuk progress bar dan state header |

**Cara kerja:**
- Setelah semua deklarasi dan listener dibuat, `onScroll()` dipanggil satu kali.
- Ini membuat progress bar dan class header dapat langsung mencerminkan posisi halaman tanpa menunggu pengguna melakukan scroll pertama.

---

## 1.2 — Ringkasan Interaksi per Bagian HTML

| Bagian HTML | Interaksi yang Berlaku |
|-------------|------------------------|
| **Header / Navigasi** | Link kembali ke beranda memakai transisi halaman; nav section mendapat class `active` melalui `IntersectionObserver`; header berubah border saat scroll melewati 10px |
| **Profil / Hero** | Preloader, animasi kata pada `h1`, custom cursor, efek magnet pada link CTA, reveal saat scroll, dan marquee setelah hero |
| **Tentang Saya** | Heading/paragraf masuk melalui reveal; link anchor mengarah ke section dengan smooth scroll |
| **Mengapa Memilih Sains Data** | Latar sekunder dan paragraf dengan aksen border pink; konten ikut reveal saat scroll |
| **Fokus Pengembangan** | Kartu fokus dengan hover transform dan shadow; reveal saat section masuk viewport |
| **Keahlian dan Teknologi** | Kartu kategori dan list teknologi memiliki state hover; reveal berjalan untuk article |
| **Riwayat Pendidikan** | Artikel pendidikan memiliki hover geser horizontal; elemen article ikut reveal |
| **Prestasi** | Kartu prestasi memiliki ornamen bintang CSS; artikel dapat memicu state `is-view` pada cursor |
| **Media Sosial dan Kontak** | Kartu kontak punya hover border/shadow; link menggunakan underline animasi |
| **Footer** | Link kembali ke atas memakai smooth anchor; hover pada link berubah warna dan shadow |
| **Global** | Preloader, progress bar, scrollspy, custom cursor desktop, reveal, marquee, nomor section, easter egg `DATA`, smooth anchor |

---

## 1.3 — Fitur-Fitur Lainnya

### ⏳ Preloader & Transisi Halaman

- Preloader halaman dibuat dari JavaScript dan memakai ID `preloader`.
- Logo yang ditampilkan adalah `JAMES.ALDO` dengan label `LOADING_PORTFOLIO.EXE`.
- Progress bar memiliki elemen fill `#pre-fill` dan counter `#pre-pct`.
- Progress naik dengan interval 60 ms; kecepatannya melambat setelah 70% dan 90%.
- Preloader ditutup dengan class `.done`, lalu `body` mendapat class `.loaded`.
- Transisi link ke halaman utama menggunakan fungsi terpisah `triggerPageTransition(url)`.
- Durasi CSS penutupan preloader adalah 0,45 detik dengan easing `cubic-bezier(.85,0,.15,1)`.

### 🖱️ Custom Cursor

- **Dot:** 8 × 8px, warna lime, `mix-blend-mode: difference`.
- **Ring default:** 38 × 38px dengan border 2px lime.
- **Hover:** ring menjadi 76 × 76px, latar lime transparan, border pink.
- **View:** ring menjadi 96 × 96px, latar lime dan border hitam; label `LIHAT` ditampilkan.
- **Interpolasi ring:** faktor 0.16 pada tiap frame.
- **Target pembesaran:** link, button, list item, article, figure, h2, h3, dan figcaption profil.
- **Magnet:** link CTA di hero dan link kembali beranda bergerak berdasarkan pointer dengan faktor 0.18.
- Efek hanya dijalankan saat pointer presisi tersedia dan reduced motion tidak aktif.

### 📈 Scroll Progress & Header State

- Bar `#scroll-progress` berada di bagian atas layar dengan tinggi 4px.
- Lebarnya mengikuti rasio jarak scroll saat ini terhadap jarak scroll maksimum.
- Header mendapat class `.scrolled` ketika posisi scroll lebih dari 10px.
- Update diproses melalui `requestAnimationFrame()` dan flag `ticking`.

### 🧭 Active Navigation / Scrollspy

- Pemetaan section dan link dibangun dari `href` pada daftar navigasi header.
- `IntersectionObserver` memakai `rootMargin: -40% 0px -55% 0px`.
- Link yang cocok mendapat class `.active`.
- CSS menampilkan bracket `[` dan `]` saat link hover atau aktif.

### 🪄 Reveal Saat Scroll

- Target: `section > p`, `section > h2`, `section > article`, `#profil figure`, dan `#profil > div`.
- Elemen mulai dari `translateY(34px)`.
- Setelah masuk viewport, class `.in` mengubah transform ke `translateY(0)`.
- Threshold observer adalah `0.12` dan target yang sudah muncul tidak diamati lagi.
- Efek tidak menggunakan fade opacity.

### ➡️ Marquee Bidang Minat

- Menampilkan kata kunci bidang dan teknologi: Data Science, Python, SQL, Machine Learning, PCA, GPR, Django, pandas, NumPy, Analisis Data, serta simbol bintang.
- Sequence frasa dibuat dua kali untuk membentuk loop visual.
- Animasi CSS berlangsung 18 detik, linear, infinite.
- Animasi berhenti sementara ketika pointer berada di atas marquee.

### 🔢 Nomor Section Otomatis

- Setiap `main section` dihitung berdasarkan urutan DOM.
- Heading `h2` menerima atribut `data-index` berformat `01 /`, `02 /`, dan seterusnya.
- CSS menampilkan nilai itu melalui pseudo-element `::before`.

### 🌈 Easter Egg `DATA`

- Ketik huruf `D`, `A`, `T`, `A` secara berurutan di keyboard.
- Warna `--acid` berubah menjadi cyan dan `--pink` berubah menjadi lime.
- Setelah 2,5 detik, nilai awal dikembalikan.

### ♿ Reduced Motion

- JavaScript mendeteksi `prefers-reduced-motion: reduce`.
- Custom cursor dan reveal tidak diinisialisasi ketika reduced motion aktif.
- Animasi kata hero tidak dimulai melalui `startHeroWords()` pada mode ini.
- Smooth anchor menggunakan `behavior: "auto"`.
- CSS mempersingkat durasi animasi dan transisi menjadi `0.01ms`, serta menonaktifkan smooth scrolling.

---

# 🔷 BAGIAN 2: CSS (`style/james.css`)

## 2.1 — Daftar Bagian & Styling

### 📌 00. FONT IMPORT & TOKENS (`:root`) (Line 1–31)

**Import font — Line 6:**
- `Archivo Black`: font display untuk heading yang tebal.
- `Space Grotesk`: font teks utama.
- `JetBrains Mono`: font monospace untuk label dan navigasi.
- Semua font dimuat melalui Google Fonts dengan `@import`.

**Daftar token:**

| Token | Nilai | Keterangan |
|-------|-------|------------|
| `--bg` | `#0b0b0b` | Latar utama hitam gelap |
| `--bg-2` | `#111111` | Latar sekunder untuk beberapa section dan card |
| `--ink` | `#f4f4f0` | Warna teks utama / putih tulang |
| `--ink-dim` | `#8a8a84` | Warna teks sekunder |
| `--acid` | `#c8ff00` | Aksen lime utama |
| `--pink` | `#ff2e88` | Aksen pink |
| `--cyan` | `#00e5ff` | Aksen cyan |
| `--violet` | `#8b5cff` | Aksen violet |
| `--border` | `#2a2a2a` | Warna border standar |
| `--border-hi` | `#f4f4f0` | Border dengan kontras tinggi |
| `--font-display` | `'Archivo Black', sans-serif` | Font heading/display |
| `--font-body` | `'Space Grotesk', sans-serif` | Font body |
| `--font-mono` | `'JetBrains Mono', monospace` | Font monospace |
| `--shadow-hard` | `8px 8px 0 var(--acid)` | Hard shadow lime tanpa blur |
| `--shadow-pink` | `8px 8px 0 var(--pink)` | Hard shadow pink tanpa blur |
| `--shadow-none` | `0 0 0 transparent` | Nilai shadow tanpa efek |
| `--nav-h` | `72px` | Tinggi default header |
| `--gutter` | `clamp(1.25rem, 4vw, 4rem)` | Jarak sisi yang responsif |

**Cara kerja:**
- Token berada di `:root`, sehingga dapat dipakai di seluruh stylesheet.
- Warna aksen juga diubah sementara oleh easter egg JavaScript melalui `document.documentElement.style.setProperty()`.
- `--nav-h` digunakan untuk tinggi header dan jarak scroll anchor.
- `--gutter` menyesuaikan padding horizontal mengikuti ukuran viewport.

---

### 📌 01. RESET & BASE (Line 33–86)

**Reset universal — Line 34–38:**
- Semua elemen dan pseudo-element mendapat `margin: 0`, `padding: 0`, dan `box-sizing: border-box`.
- `border-box` memastikan ukuran elemen memperhitungkan padding dan border.

**HTML dan body — Line 39–55:**
- `html` menggunakan `scroll-behavior: smooth` dan `scroll-padding-top: calc(var(--nav-h) + 16px)`.
- `body` menggunakan latar `--bg`, teks `--ink`, font `--font-body`, ukuran `1.0625rem`, dan line-height `1.65`.
- `overflow-x: hidden` mencegah konten meluber horizontal.
- Background terdiri dari dua linear-gradient untuk grid blueprint 56px × 56px.

**Noise overlay — Line 56–65:**
- `body::after` menutupi viewport dengan `position: fixed` dan `inset: 0`.
- Overlay memakai SVG data URI berisi filter `feTurbulence` untuk tekstur noise.
- `opacity: .05` menjaga tekstur tetap tipis.
- `pointer-events: none` membuat overlay tidak menghalangi klik.
- `z-index: 9990` menempatkannya di lapisan atas, di bawah beberapa elemen UI yang memakai z-index lebih tinggi.

**Seleksi teks dan scrollbar — Line 66–76:**
- Teks terpilih mendapat background lime dan teks hitam.
- Scrollbar WebKit memiliki lebar 12px dan background hitam.
- Thumb scrollbar berwarna lime dengan border hitam 3px.

**Elemen dasar — Line 78–80:**
- Gambar dibatasi `max-width: 100%` dan ditampilkan sebagai block.
- Link mewarisi warna teks (`color: inherit`).
- List menggunakan `list-style: none`.

**Kursor desktop — Line 82–85:**
- Pada `@media (hover:hover) and (pointer:fine)`, cursor asli disembunyikan untuk body, link, button, article, dan figure.
- Elemen cursor custom disediakan di bagian CSS berikutnya.

---

### 📌 02. PRELOADER (Line 87–136)

**Container `#preloader` — Line 88–100:**
- `position: fixed` dan `inset: 0` menutupi seluruh viewport.
- `z-index: 10000` menempatkan loader di lapisan tertinggi.
- Background hitam; konten disusun vertikal di tengah dengan flexbox.
- Jarak antar elemen `18px`.
- Transisi transform berlangsung `0.45s` menggunakan `cubic-bezier(.85,0,.15,1)`.
- State `.done` menggeser loader ke atas dengan `translateY(-101%)`.

**Logo — Line 101–108:**
- Menggunakan font display, ukuran responsif `clamp(1.6rem, 5vw, 3rem)`, dan letter-spacing `.04em`.
- Logo memakai uppercase.
- Span di dalam logo berwarna lime.

**Progress bar — Line 109–121:**
- Lebar `min(320px, 60vw)` dan tinggi 14px.
- Border putih 2px, padding 2px.
- Elemen `i` mengisi tinggi penuh dan memulai dari lebar 0%.
- Fill berwarna lime dan lebar berubah dengan transisi linear 0.12s.

**Counter — Line 122–127:**
- Menggunakan font monospace, ukuran `.8rem`, warna teks sekunder, dan letter-spacing `.2em`.

**Tag loading — Line 128–136:**
- Ditempatkan absolut di bawah 24px.
- Menggunakan font monospace kecil, warna sekunder, letter-spacing `.3em`, dan uppercase.

---

### 📌 03. CUSTOM CURSOR (Line 138–184)

**Base `.cursor-dot, .cursor-ring` — Line 139–150:**
- Kedua elemen memakai `position: fixed`, `top: 0`, `left: 0`, `z-index: 9999`, `pointer-events: none`, dan bentuk lingkaran.
- Default display `none`; di perangkat hover/pointer presisi diubah menjadi `block`.
- Transform dasar memusatkan elemen terhadap koordinat yang diberikan JavaScript.

**Dot — Line 151–155:**
- Ukuran 8 × 8px.
- Background lime.
- `mix-blend-mode: difference` membuat warna tampil kontras terhadap latar di bawahnya.

**Ring — Line 156–171:**
- Ukuran default 38 × 38px dengan border lime 2px.
- Transisi width, height, background, dan border-color berlangsung 0.25s.
- `.is-hover`: 76 × 76px, background lime transparan 12%, border pink.
- `.is-view`: 96 × 96px, background lime solid, border hitam.

**Label — Line 172–184:**
- Diposisikan memenuhi ring menggunakan `inset: 0`.
- Flexbox menempatkan teks di tengah.
- Font monospace tebal, ukuran `.65rem`, letter-spacing `.15em`, warna hitam.
- Opacity awal 0 dan berubah menjadi 1 saat ring memiliki class `.is-view`.

---

### 📌 04. SCROLL PROGRESS (Line 186–194)

**`#scroll-progress`:**
- Posisi fixed di sisi atas kiri layar.
- Tinggi 4px, lebar awal 0%.
- Background lime dan `z-index: 9995`.
- Lebar diubah oleh JavaScript sesuai persentase scroll.

---

### 📌 05. MARQUEE (Line 196–221)

**Container `.marquee` — Line 197–205:**
- `overflow: hidden` memotong teks yang keluar dari area.
- Border atas dan bawah 2px menggunakan warna `--border`.
- Background lime, teks hitam, padding vertikal `.55rem`, dan `white-space: nowrap`.

**Track — Line 206–217:**
- `inline-flex` dengan gap 2.5rem.
- Font monospace tebal, ukuran `.95rem`, letter-spacing `.12em`, uppercase.
- Animasi `marquee` berlangsung 18 detik, linear, infinite.
- `will-change: transform` memberi petunjuk bahwa transform akan berubah.
- Saat marquee di-hover, animasi berhenti sementara melalui `animation-play-state: paused`.

**Keyframes — Line 218–221:**
- Mulai dari `translateX(0)`.
- Berakhir di `translateX(-50%)`, sesuai dua sequence frasa yang dibuat oleh JavaScript.

---

### 📌 06. HEADER & NAVIGASI (Line 223–310)

**Header — Line 224–234:**
- `position: sticky`, `top: 0`, dan `z-index: 100` menjaga header tetap terlihat saat scroll.
- Tinggi mengikuti `--nav-h`.
- Background hitam transparan 88% dengan `backdrop-filter: blur(10px)`.
- Border bawah 2px warna border.
- Saat class `.scrolled` aktif, warna border bawah berubah menjadi lime.

**Navigasi — Line 235–242:**
- Mengisi tinggi header.
- Menggunakan flexbox untuk menyusun link beranda dan daftar navigasi di kedua sisi.
- Jarak antarkomponen 1rem.
- Padding horizontal memakai `--gutter`.

**Link kembali ke beranda — Line 243–266:**
- Font monospace `.78rem`, tebal, uppercase, letter-spacing `.12em`.
- Border 2px putih, padding `.5rem .9rem`, background `--bg`.
- Hard shadow default `4px 4px 0 var(--acid)`.
- Hover: background lime, teks hitam, geser `(-2px, -2px)`, shadow menjadi `6px 6px` pink.
- Active: bergeser `(2px, 2px)` dan shadow hilang.

**Daftar navigasi — Line 267–310:**
- `ul` memakai flexbox, jarak `.25rem`, dan horizontal overflow untuk menampung item.
- Scrollbar daftar disembunyikan.
- Link menggunakan font monospace `.72rem`, tebal, uppercase, dan warna awal `--ink-dim`.
- Pseudo-element `::before` menambahkan bracket `[` dan `::after` menambahkan `]`.
- Bracket awalnya transparan, berwarna lime, dan muncul saat hover atau class `.active` aktif.
- Link hover/aktif menggunakan warna `--ink`.

---

### 📌 07. LAYOUT & HEADING SECTION (Line 312–361)

**Main dan section — Line 313–320:**
- `main` memakai `position: relative` dan `z-index: 1`.
- Setiap section memiliki padding vertikal responsif `clamp(4rem, 9vw, 7.5rem)` dan padding horizontal `--gutter`.
- Lebar maksimum section 1240px dan margin horizontal otomatis.
- Section yang bersebelahan dipisahkan border atas 2px.

**Heading section — Line 323–355:**
- Font display, ukuran `clamp(2rem, 6vw, 4rem)`, uppercase, line-height 1.05.
- Heading ditampilkan sebagai inline-block agar underline dapat mengikuti lebar heading.
- `::before` menampilkan nomor section dari `data-index`.
- Nomor menggunakan font monospace, ukuran `.8rem`, warna lime, dan posisi di atas heading.
- `::after` adalah garis lime setinggi 6px yang dimulai dari scaleX(0).
- Saat heading di-hover, garis melebar menjadi scaleX(1).

**Paragraf langsung di section — Line 357–361:**
- Lebar maksimum 68 karakter (`68ch`).
- Warna `--ink-dim` dan margin bawah 1.1rem.

---

### 📌 08. HERO / PROFIL (Line 363–520)

**Layout `#profil` — Line 364–372:**
- Grid dua kolom: foto dengan lebar minimum 260px dan maksimum 380px, lalu kolom konten yang mengisi ruang tersisa.
- Konten vertikal disejajarkan ke tengah.
- Gap responsif `clamp(2rem, 5vw, 4.5rem)`.
- Tinggi minimum `calc(100vh - var(--nav-h))`.
- Padding atas responsif.

**Frame foto — Line 373–408:**
- Figure memakai border putih 3px, background sekunder, dan hard shadow lime.
- Hover menggeser frame `(-6px, -6px)`, memutar `-1.5deg`, lalu mengganti shadow menjadi `14px 14px` pink.
- `::before` menampilkan label `IMG_001.JPG` dengan background lime.
- `::after` menampilkan label `DATA/SCIENCE` pada kanan bawah foto dengan background pink.

**Foto dan caption — Line 409–434:**
- Gambar memenuhi lebar frame, menggunakan rasio 1:1 dan `object-fit: cover`.
- Filter awal `grayscale(1) contrast(1.15)`.
- Saat figure di-hover, foto menjadi berwarna dengan `grayscale(0) contrast(1.05)`.
- Caption memiliki border atas putih 3px, padding, font monospace, uppercase, dan flexbox.
- Pseudo-element `●` berwarna lime dan berkedip melalui keyframes `blink` selama 1.4 detik.

**Label status hero — Line 436–449:**
- Paragraf pertama dalam div hero dibuat inline-block.
- Font monospace tebal, uppercase, background lime, teks hitam.
- Diputar `-1.5deg` dan diberi shadow pink 5px.

**Heading utama — Line 450–469:**
- Font display dengan ukuran `clamp(2.6rem, 7.5vw, 5.6rem)`.
- Uppercase dan line-height `.98`.
- Memiliki dua lapisan text-shadow: lime dan pink transparan.
- Kata yang dibungkus `.word` menggunakan `overflow: hidden`.
- Span bagian dalam dimulai dari `translateY(110%)` dan memakai animasi `wordUp` selama `.7s` dengan delay dari JavaScript.

**Deskripsi — Line 470–484:**
- Paragraf setelah label status memakai warna sekunder, maksimum 52ch, ukuran 1.1rem, dan border kiri lime 4px.
- Paragraf terakhir (CTA) menjadi flex container dengan wrapping dan gap 1rem; border dan padding kiri deskripsi direset.

**CTA hero — Line 485–520:**
- Link menggunakan font monospace tebal, uppercase, border putih 3px, dan transisi transform/shadow/background/color.
- CTA pertama: background lime, teks hitam, shadow putih.
- Hover CTA pertama: background pink, geser `(-3px, -3px)`, shadow lime lebih besar.
- CTA terakhir: transparan dengan teks putih dan shadow pink.
- Hover CTA terakhir: background putih, teks hitam, shadow pink lebih besar.
- Active state menggeser tombol `(3px, 3px)` dan menghilangkan shadow.

---

### 📌 09. TENTANG & ALASAN MEMILIH SAINS DATA (Line 522–537)

**Paragraf tentang/alasan — Line 523–526:**
- `#tentang p` dan `#alasan p` memakai ukuran font 1.15rem dan lebar maksimum 75ch.

**Section alasan — Line 527–537:**
- `#alasan` menggunakan background `--bg-2` dan lebar penuh (`max-width: none`).
- Border atas dan bawah 2px warna `--border`.
- Anak langsung diberi lebar maksimum 1240px dan margin horizontal otomatis.
- Paragraf memiliki border kiri pink 4px dan padding kiri 1.2rem.

---

### 📌 10. FOKUS PENGEMBANGAN (Line 539–575)

**Grid fokus — Line 540–545:**
- Tiga kolom dengan `repeat(3, minmax(0, 1fr))`.
- Gap antar kartu 1rem dan margin atas 2rem.

**Kartu fokus — Line 546–557:**
- Tinggi minimum 220px, padding 1.5rem, border 2px, background sekunder.
- Hover menggeser kartu `(-4px, -4px)`, border menjadi warna teks utama, dan shadow 7px pink.
- Transisi mencakup transform, border-color, dan box-shadow.

**Nomor, heading, paragraf — Line 558–575:**
- `.focus-number` tampil sebagai block dengan margin bawah 1.4rem, warna lime, font monospace, ukuran `.7rem`.
- Heading kartu memakai font display, ukuran 1.15rem, uppercase.
- Deskripsi menggunakan warna `--ink-dim` dan ukuran `.95rem`.

---

### 📌 11. KEAHLIAN & TEKNOLOGI (Line 577–656)

**Kartu kategori — Line 578–590:**
- Article memiliki border 2px, background sekunder, padding 1.6rem 1.8rem, dan margin bawah 1.4rem.
- `position: relative` menjadi konteks bagi konten yang mungkin diposisikan relatif.
- Hover menggeser kartu `(-4px, -4px)`, border menjadi `--ink`, dan shadow menjadi 8px lime.

**Heading kategori — Line 591–606:**
- Font monospace, ukuran 1rem, tebal, uppercase, letter-spacing `.08em`.
- Menggunakan flexbox dengan gap `.7rem`.
- Pseudo-element `::before` menampilkan karakter `>` berwarna lime.

**Deskripsi kategori — Line 607–611:**
- Paragraf langsung memakai font monospace `.85rem` dan warna sekunder.

**List teknologi — Line 612–630:**
- List memakai flexbox, wrapping, dan gap `.7rem`.
- Item memiliki border 2px, padding `.5rem 1rem`, font monospace `.85rem`, dan background utama.
- Hover mengubah background menjadi lime, teks menjadi hitam, border menjadi lime, dan memutar item `-1.5deg`.

**Item khusus yang mengandung paragraf — Line 631–643:**
- Selector `li:has(p)` memperlebar item agar dapat mengakomodasi penjelasan PCA dan GPR.
- Item tersebut memakai `flex: 1 1 260px`, padding lebih besar, dan background utama.
- Hover khusus tidak mengubah background, mempertahankan warna teks, mengubah border menjadi pink, dan menonaktifkan transform.

**Strong dan paragraf pada item khusus — Line 644–656:**
- `strong` ditampilkan sebagai block dengan font monospace, warna lime, ukuran `.9rem`, dan margin bawah `.4rem`.
- Paragraf memakai font body, warna sekunder, dan ukuran `.92rem`.

---

### 📌 12. PENDIDIKAN (Line 658–729)

**Kartu pendidikan — Line 659–674:**
- Border 2px, border kiri lime 6px, background sekunder, padding 1.8rem 2rem.
- Margin bawah 1.5rem.
- Grid dua kolom: `1fr auto`, dengan gap `.4rem 2rem`.
- Hover menggeser kartu ke kanan 8px, border menjadi warna teks utama, dan shadow negatif ke kiri dengan warna pink.

**Nama institusi — Line 675–680:**
- Font display dengan ukuran responsif `clamp(1.1rem, 2.5vw, 1.5rem)`.
- Uppercase dan ditempatkan di kolom pertama.

**Periode pendidikan — Line 681–689:**
- Paragraf pertama mendapat kolom kedua dan baris pertama.
- Font monospace `.8rem`, warna lime, letter-spacing `.1em`, dan `white-space: nowrap`.

**Jenjang/program — Line 690–698:**
- Paragraf kedua melebar ke seluruh kolom.
- Font monospace tebal, uppercase, warna pink, dan letter-spacing `.15em`.

**Deskripsi dan label mata pelajaran — Line 699–711:**
- Paragraf ketiga menggunakan warna sekunder dan membentang di semua kolom.
- `h4` menggunakan font monospace `.75rem`, uppercase, warna sekunder, dan margin atas `.6rem`.

**Tag mata pelajaran — Line 712–729:**
- List memakai flexbox dan wrapping.
- Tag memiliki font monospace `.8rem`, border 2px, padding `.35rem .8rem`, dan warna teks sekunder.
- Hover mengubah border dan teks menjadi lime.

---

### 📌 13. PRESTASI (Line 731–770)

**Kartu prestasi — Line 732–739:**
- Border 2px warna border, background sekunder, padding 1.8rem 2rem, dan margin bawah 1.4rem.
- `position: relative` dan `overflow: hidden` membatasi ornamen bintang.

**Ornamen bintang — Line 740–753:**
- `::before` menampilkan simbol `★` di kanan atas.
- Ukuran font 5.5rem, warna lime, opacity awal `.08`, dan rotasi 15 derajat.
- Saat kartu di-hover, opacity menjadi `.22` dan bintang berubah ke rotasi 0 derajat dengan scale 1.15.

**Heading prestasi — Line 754–762:**
- Font display dengan ukuran responsif `clamp(1.05rem, 2.4vw, 1.4rem)`.
- Warna lime, uppercase, margin bawah `.7rem`.
- Saat article di-hover, heading bergeser ke kanan 6px.

**Deskripsi dan label penyelenggara — Line 763–770:**
- Paragraf memakai warna sekunder dan lebar maksimum 70ch.
- `strong` memakai font monospace kecil, uppercase, letter-spacing `.15em`, dan warna pink.

---

### 📌 14. KONTAK & MEDIA SOSIAL (Line 772–828)

**Kartu kontak — Line 773–789:**
- Border 2px, background sekunder, padding 1.5rem 1.8rem, dan margin bawah 1.2rem.
- Flexbox menyelaraskan label dan tautan, dengan wrapping agar konten dapat turun baris.
- Hover menggeser kartu `(-4px, -4px)`, memberi hard shadow 8px cyan, dan mengubah border ke warna teks utama.

**Heading kontak — Line 790–804:**
- Font monospace tebal, uppercase, letter-spacing `.2em`, dan warna sekunder.
- Flexbox menempatkan simbol sebelum judul.
- Pseudo-element `::before` menambahkan `//` berwarna lime.

**Tautan kontak — Line 805–828:**
- Font monospace tebal dengan ukuran responsif `clamp(.9rem, 2.5vw, 1.15rem)`.
- `word-break: break-all` membantu alamat panjang tetap muat.
- Pseudo-element `::after` menjadi underline lime setinggi 3px.
- Garis dimulai dari `scaleX(0)` dengan origin kanan.
- Saat hover, warna tautan menjadi lime dan garis melebar ke `scaleX(1)` dari kiri.

---

### 📌 15. FOOTER (Line 830–870)

**Container footer — Line 831–837:**
- Border atas 3px lime, background hitam, padding responsif melalui `--gutter`, teks rata tengah, dan `position: relative`.

**Paragraf footer — Line 838–850:**
- Font monospace `.8rem`, letter-spacing `.12em`, warna sekunder, margin bawah `.8rem`.
- Elemen `time` berwarna lime.
- Paragraf kedua memakai uppercase, letter-spacing `.3em`, dan ukuran `.7rem`.

**Link footer — Line 851–870:**
- Ditampilkan sebagai inline-block dengan margin atas 1rem.
- Font monospace tebal, uppercase, border 2px warna teks utama.
- Hover: background lime, teks hitam, border lime, transform `(-2px, -2px)`, dan shadow 4px pink.

---

### 📌 16. REVEAL SLIDE (Line 872–877)

**`.reveal`:**
- State awal memakai `transform: translateY(34px)`.
- Transisi transform berlangsung `.6s` dengan easing `cubic-bezier(.2,.8,.2,1)`.
- Class `.in` mengubah transform menjadi `translateY(0)`.
- Tidak ada opacity fade pada aturan reveal ini.

---

### 📌 17. RESPONSIVE (Line 879–892)

**Breakpoint 900px — Line 880–883:**
- `#profil` berubah menjadi satu kolom (`grid-template-columns: 1fr`).
- Teks tetap rata kiri.
- Foto profil dibatasi `max-width: 340px`.

**Breakpoint 640px — Line 884–892:**
- `--nav-h` diturunkan menjadi 64px.
- Link kembali ke beranda memakai font lebih kecil, padding lebih rapat, dan shadow 3px lime.
- Grid fokus menjadi satu kolom.
- Kartu fokus tidak lagi memakai tinggi minimum.
- Kartu pendidikan menjadi satu kolom.
- Paragraf periode pendidikan kembali ke kolom pertama dan alur baris normal.
- Kartu kontak berubah menjadi flex column dengan gap `.4rem`.

---

### 📌 18. REDUCED MOTION (Line 894–901)

**`@media (prefers-reduced-motion: reduce)`:**
- Semua elemen dan pseudo-element memiliki `animation-duration: .01ms !important`.
- Semua transition dipersingkat menjadi `.01ms !important`.
- `html` menggunakan `scroll-behavior: auto`.
- Aturan ini membantu pengguna yang memilih pengurangan gerakan menghindari animasi panjang.

---

## 2.2 — Font yang Digunakan

| Font | Peran | Fallback |
|------|-------|----------|
| **Archivo Black** | Heading besar, judul section, nama institusi, dan heading prestasi | `sans-serif` |
| **Space Grotesk** | Paragraf dan teks utama | `sans-serif` |
| **JetBrains Mono** | Navigasi, label, metadata, tombol, dan teks teknis | `monospace` |

Font diimpor melalui Google Fonts pada baris 6 `style/james.css`.

---

## 2.3 — Warna Dasar

| Nama | Hex | Penggunaan |
|------|-----|-----------|
| Background utama | `#0b0b0b` | Body dan latar dasar |
| Background sekunder | `#111111` | Kartu fokus, pendidikan, prestasi, dan kontak |
| Teks utama | `#f4f4f0` | Heading, teks utama, dan border kontras |
| Teks sekunder | `#8a8a84` | Paragraf dan label pendukung |
| Lime / acid | `#c8ff00` | Aksen utama, progress, CTA, dan detail visual |
| Pink | `#ff2e88` | Shadow dan aksen kontras |
| Cyan | `#00e5ff` | Aksen shadow pada kartu kontak dan easter egg |
| Violet | `#8b5cff` | Token aksen tambahan yang tersedia di root |
| Border | `#2a2a2a` | Garis pembatas standar |
| Border kontras | `#f4f4f0` | Garis yang perlu terlihat jelas |

> Catatan: easter egg mengubah `--acid` dan `--pink` sementara. Token `--cyan` dan `--violet` tetap tersedia sebagai token warna, meskipun tidak semua token dipakai pada setiap section.

---

## 2.4 — Hard Shadow Pattern

Portofolio menggunakan shadow tanpa blur untuk menghasilkan aksen visual yang tegas dan kontras.

| Token / Contoh | Nilai | Penggunaan |
|----------------|-------|-----------|
| `--shadow-hard` | `8px 8px 0 var(--acid)` | Shadow utama berwarna lime |
| `--shadow-pink` | `8px 8px 0 var(--pink)` | Shadow aksen pink |
| Header link | `4px 4px 0 var(--acid)` | Tombol kembali ke beranda |
| Card fokus | `7px 7px 0 var(--pink)` saat hover | Efek hover kartu fokus |
| Card keahlian | `8px 8px 0 var(--acid)` saat hover | Efek hover kartu keahlian |
| Card kontak | `8px 8px 0 var(--cyan)` saat hover | Efek hover kartu kontak |

**Karakteristik:**
- Offset shadow terlihat jelas karena blur bernilai `0`.
- Warna shadow berasal dari token CSS, sehingga selaras dengan palet.
- Beberapa komponen bergerak sedikit saat hover bersamaan dengan perubahan shadow.

---

## 2.5 — Border & Aksen Visual

| Pattern | Penggunaan |
|---------|-----------|
| `2px solid var(--border)` | Kartu fokus, keahlian, prestasi, dan kontak |
| `3px solid var(--ink)` | Frame foto profil |
| `6px solid var(--acid)` pada sisi kiri | Penanda kartu pendidikan |
| `2px solid var(--ink)` | Tombol kembali ke beranda dan link footer |
| Pseudo-element `[` dan `]` | Penanda hover/active navigasi |
| Pseudo-element `//` | Prefix heading kartu kontak |
| Pseudo-element `★` | Ornamen kartu prestasi |
| Pseudo-element `>` | Prefix heading kartu keahlian |

---

## 2.6 — Animasi CSS

| Animasi | Baris | Efek |
|---------|-------|------|
| `marquee` | 218–221 | Track bergerak horizontal dari 0 ke -50% |
| `blink` | 434 | Titik caption foto berkedip dengan `steps(1)` setiap 1.4 detik |
| `wordUp` | 469 | Kata hero bergerak dari bawah ke posisi normal |
| Reveal | 872–877 | Elemen slide vertikal 34px ke posisi normal |
| Transisi preloader | 98–100 | Loader bergeser ke atas saat class `.done` aktif |

---

# 🔷 BAGIAN 3: HTML (`template/james.html`)

## 3.1 — Metadata & Koneksi File (Line 1–17)

| Elemen | Line | Deskripsi |
|--------|------|-----------|
| `<!DOCTYPE html>` | 1 | Mendeklarasikan dokumen HTML5 |
| `<html lang="id">` | 2 | Menandai bahasa utama halaman sebagai Bahasa Indonesia |
| `meta charset` | 5 | Menetapkan encoding UTF-8 |
| `meta viewport` | 6 | Menyesuaikan viewport dengan lebar perangkat |
| `meta author` | 8 | Mencantumkan James Aldo Parlindungan sebagai penulis |
| `meta description` | 9–10 | Menjelaskan isi dan fokus portofolio |
| `<title>` | 12 | Menetapkan judul tab browser |
| Link CSS | 14 | Memuat `../style/james.css` |

JavaScript dimuat pada line 479, menjelang akhir body, melalui `../script/james.js`.

---

## 3.2 — HEADER & NAVIGASI (Line 19–36)

| Elemen | Line | Deskripsi |
|--------|------|-----------|
| `<header id="beranda">` | 20 | Header halaman dan target anchor untuk kembali ke atas |
| `<nav aria-label="Navigasi utama">` | 22 | Menandai area navigasi utama untuk aksesibilitas |
| Link beranda | 23 | Mengarah ke `../index.html` |
| Navigasi Profil | 26 | Anchor ke `#profil` |
| Navigasi Tentang Saya | 27 | Anchor ke `#tentang` |
| Navigasi Fokus | 28 | Anchor ke `#fokus` |
| Navigasi Keahlian | 29 | Anchor ke `#keahlian` |
| Navigasi Pendidikan | 30 | Anchor ke `#pendidikan` |
| Navigasi Prestasi | 31 | Anchor ke `#prestasi` |
| Navigasi Media Sosial | 32 | Anchor ke `#kontak` |

**Catatan:** `#alasan` tersedia sebagai section HTML, tetapi tidak mempunyai link tersendiri di daftar navigasi header.

---

## 3.3 — DAFTAR SECTION HTML

| Bagian | ID | Baris | Isi utama |
|--------|----|-------|-----------|
| Profil utama | `#profil` | 42–72 | Foto, label Data Science Student, nama, deskripsi, dan CTA |
| Tentang Saya | `#tentang` | 76–95 | Pengenalan dan minat di bidang sains data |
| Mengapa Memilih Sains Data? | `#alasan` | 99–114 | Alasan memilih Data Science |
| Fokus Pengembangan | `#fokus` | 118–159 | Tiga kartu: Analisis Data, Machine Learning, Solusi Berbasis Data |
| Keahlian dan Teknologi | `#keahlian` | 163–280 | Bahasa pemrograman, framework, library, metode, database, dan tools |
| Riwayat Pendidikan | `#pendidikan` | 284–340 | SMA Negeri 2 Sampit dan Universitas Logistik dan Bisnis Internasional |
| Prestasi | `#prestasi` | 344–390 | Prestasi turnamen basket dan Mobile Legends |
| Media Sosial dan Kontak | `#kontak` | 394–456 | Email, Instagram, dan LinkedIn |
| Footer | — | 462–477 | Hak cipta, tagline, dan link kembali ke atas |

---

## 3.4 — PROFIL UTAMA (Line 39–72)

- `<main>` dimulai pada line 40.
- Section profil memakai ID `profil`.
- `<figure>` menampung foto `../assets/james.jpeg` dengan teks alternatif `Foto profil James Aldo Parlindungan`.
- Atribut `width` dan `height` pada gambar masing-masing bernilai 300.
- `<figcaption>` menampilkan nama pemilik portofolio.
- Label `DATA SCIENCE STUDENT` ditempatkan sebelum heading `h1`.
- Deskripsi memperkenalkan status mahasiswa Sains Data dan minat pada teknologi, pemrograman, analisis data, serta solusi berbasis data.
- CTA `Kenali Saya` mengarah ke `#tentang`; CTA `Hubungi Saya` mengarah ke `#kontak`.

---

## 3.5 — TENTANG SAYA (Line 75–95)

- Heading section adalah `Tentang Saya`.
- Dua paragraf menjelaskan identitas sebagai mahasiswa Sains Data di Universitas Logistik dan Bisnis Internasional.
- Isi juga menyoroti minat pada pemrograman, analisis data, metode komputasi, Python, visualisasi, analisis statistik, dan machine learning.

---

## 3.6 — ALASAN MEMILIH SAINS DATA (Line 98–114)

- Heading section adalah `Mengapa Memilih Sains Data?`.
- Paragraf membahas ketertarikan pada teknologi dan analisis data, pemecahan masalah, pengambilan keputusan, berpikir kritis, pemrograman, serta peluang karier.
- Section ini memiliki ID `alasan` dan styling latar sekunder tersendiri di CSS.

---

## 3.7 — FOKUS PENGEMBANGAN (Line 117–159)

Terdapat tiga kartu di `.focus-grid`:

| Nomor | Judul | Isi |
|-------|-------|-----|
| `01 / ANALISIS` | Analisis Data | Python, pandas, NumPy, dan dasar analisis statistik |
| `02 / PEMODELAN` | Machine Learning | PCA dan Gaussian Process Regression untuk memahami pola dan prediksi |
| `03 / PENGEMBANGAN` | Solusi Berbasis Data | Pemrograman dan teknologi web untuk membangun solusi pendukung pemanfaatan data |

Setiap kartu menggunakan `<article>`, `<span class="focus-number">`, `<h3>`, dan `<p>`.

---

## 3.8 — KEAHLIAN DAN TEKNOLOGI (Line 162–280)

| Kelompok | Baris | Isi |
|----------|-------|-----|
| Pengantar | 165–170 | Penjelasan bahwa daftar berisi teknologi dan perangkat yang sedang dipelajari |
| Bahasa pemrograman dan web technologies | 173–183 | Python dan SQL |
| Framework | 186–195 | Django |
| Library Python | 198–209 | pandas, NumPy, Matplotlib |
| Algoritma, Machine Learning, dan Deep Learning | 212–237 | Principal Component Analysis (PCA) dan Gaussian Process Regression (GPR) beserta deskripsi singkat |
| Deep Learning Framework | 240–250 | Menjelaskan bahwa belum ada framework deep learning yang dicantumkan |
| Database dan teknologi backend | 253–263 | MySQL dan PostgreSQL |
| Tools dan development environment | 266–278 | Visual Studio Code, Jupyter Notebook, Google Colab, GitHub |

---

## 3.9 — RIWAYAT PENDIDIKAN (Line 283–340)

**SMA Negeri 2 Sampit — Line 289–316:**
- Periode 2022–2025.
- Jenjang ditulis sebagai Sekolah Menengah Atas.
- Deskripsi mencantumkan lokasi Sampit, Kalimantan Tengah.
- Mata pelajaran yang dicantumkan: Matematika, Geografi, Ekonomi, dan Informatika.

**Universitas Logistik dan Bisnis Internasional — Line 319–338:**
- Periode 2025–Sekarang.
- Program Studi S1 Sains Data.
- Deskripsi menyebut pengolahan data, pemrograman, analisis statistik, dan metode komputasi untuk mendukung pengambilan keputusan.

---

## 3.10 — PRESTASI (Line 343–390)

| Prestasi | Baris | Keterangan |
|----------|-------|------------|
| Juara 2 Tournament Basketball 5x5 | 355–370 | Turnamen bola basket yang diselenggarakan GIBS School Banjarmasin |
| Juara 2 Tournament Mobile Legends Antar SMA | 373–388 | Turnamen antarsekolah menengah atas yang diselenggarakan UNDA University |

Setiap prestasi memakai heading `h3`, paragraf penjelasan, dan label penyelenggara di dalam elemen `strong`.

---

## 3.11 — MEDIA SOSIAL DAN KONTAK (Line 393–456)

| Jenis | Baris | Implementasi |
|-------|-------|--------------|
| Email | 405–416 | Link `mailto:jamesaldoparl24@gmail.com` |
| Instagram | 419–435 | Link ke profil `justnn616`, dibuka pada tab baru dengan `rel="noopener noreferrer"` dan `aria-label` |
| LinkedIn | 438–454 | Link ke profil James, dibuka pada tab baru dengan `rel="noopener noreferrer"` dan `aria-label` |

---

## 3.12 — FOOTER & SCRIPT (Line 458–482)

- Elemen `main` ditutup pada line 458.
- Footer dimulai pada line 462.
- Copyright memakai elemen `<time datetime="2026">2026</time>`.
- Tagline footer: `Data Science Student | Research & Technology`.
- Link `Kembali ke Atas` mengarah ke `#beranda`.
- Script `../script/james.js` dimuat pada line 479 sebelum `body` ditutup.

---

# BAGIAN 4: HUBUNGAN ANTARFILE & CARA MENJALANKAN

## 4.1 — Peran File

| File | Peran |
|------|-------|
| `template/james.html` | Struktur konten dan elemen halaman portofolio James |
| `style/james.css` | Visual, layout, animasi CSS, dan responsive halaman James |
| `script/james.js` | Interaksi dan perilaku dinamis halaman James |
| `assets/james.jpeg` | Foto profil yang digunakan di halaman James |
| `index.html` | Halaman luar yang dapat menjadi jalur menuju profil; bukan fokus dokumentasi desain ini |

## 4.2 — Alur Kerja Halaman James

1. Browser membaca struktur dari `template/james.html`.
2. HTML memuat `../style/james.css` untuk styling.
3. HTML menampilkan foto dari `../assets/james.jpeg`.
4. Menjelang akhir dokumen, `../script/james.js` dijalankan.
5. JavaScript membuat preloader, progress bar, marquee, cursor khusus (jika didukung), serta listener interaksi.
6. Saat pengguna scroll, progress bar dan state header diperbarui; observer mengelola navigasi aktif dan reveal.
7. Klik anchor internal menjalankan smooth scroll dengan offset 84px, kecuali reduced motion aktif.

## 4.3 — Cara Menjalankan

1. Pertahankan struktur direktori agar path relatif tidak berubah.
2. Buka `index.html` melalui browser atau server lokal.
3. Pilih profil James dari menu Profil atau kartu portofolio.
4. Uji navigasi section, CTA, progress bar, reveal, marquee, hover card, dan easter egg `DATA`.
5. Untuk memeriksa custom cursor dan efek magnet, gunakan desktop dengan mouse dan pastikan preferensi reduced motion tidak aktif.

---

*Cakupan utama dokumentasi adalah `script/james.js`, `style/james.css`, dan `template/james.html`. Deskripsi fitur dimaksudkan untuk menjelaskan implementasi halaman James, bukan halaman profil orang lain. Nomor baris mengikuti salinan sumber yang digunakan saat dokumentasi disusun.*
