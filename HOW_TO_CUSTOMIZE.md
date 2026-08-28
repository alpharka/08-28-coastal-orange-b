# How to Customize Website Undangan Digital

Panduan ini menjelaskan cara mengganti isi dan tampilan website undangan digital **Nara & Elang** tanpa mengubah arsitektur utama. Proyek ini adalah frontend React + TypeScript yang menggunakan data undangan terpusat di `client/src/pages/Home.tsx`, stylesheet global di `client/src/index.css`, dan aset visual yang direferensikan melalui URL penyimpanan proyek.

> **Catatan penting:** Nilai di repository saat ini adalah contoh. Ganti seluruh identitas, tanggal, lokasi, rekening, e-wallet, foto, dan copy sebelum membagikan tautan undangan kepada tamu.

## 1. Prasyarat dan struktur proyek

Pastikan Node.js dan pnpm tersedia. Jalankan perintah berikut dari root repository:

```bash
pnpm install
pnpm dev
```

Setelah server berjalan, buka URL lokal yang ditampilkan di terminal. Struktur file yang paling sering disentuh adalah sebagai berikut.

| File | Fungsi | Kapan diedit |
|---|---|---|
| `client/src/pages/Home.tsx` | Data konfigurasi, section halaman, interaksi, countdown, RSVP, lightbox, dan copy rekening | Saat mengganti konten atau perilaku fitur |
| `client/src/index.css` | Palet warna, font, layout, responsive breakpoint, tekstur, dan animasi | Saat mengganti tema visual |
| `client/index.html` | Bahasa dokumen, judul tab, dan metadata dasar | Saat mengganti judul halaman |
| `ideas.md` | Keputusan desain dan arah visual | Saat mengubah identitas desain secara besar |
| `client/public/` | File kecil seperti favicon atau robots.txt | Jangan simpan foto/video besar di sini |

Website ini menggunakan halaman tunggal pada route `/`. Folder `server/` adalah bagian scaffold dan tidak perlu diedit untuk kustomisasi konten frontend.

## 2. Mengganti data undangan

Buka `client/src/pages/Home.tsx`, lalu cari objek `config` di bagian atas file. Semua data utama sebaiknya diganti dari objek ini agar tidak ada nilai yang tertinggal di section lain.

```tsx
const config = {
  couple: "Nama Mempelai 1 & Nama Mempelai 2",
  shortNames: "Nama panggilan 1 dan Nama panggilan 2",
  parents: "Putri/putra dari ...",
  dateLabel: "Sabtu, 14 November 2026",
  eventDate: "2026-11-14T16:00:00+07:00",
  akad: { time: "15.30 WIB", venue: "Nama venue akad" },
  reception: { time: "18.30 – 21.00 WIB", venue: "Nama venue resepsi" },
  address: "Alamat lengkap venue",
  maps: "https://maps.google.com/?q=Nama+Venue",
  calendar: "https://calendar.google.com/calendar/render?...",
  ewallet: "081234567890",
  bank: "1234567890",
  receiver: "Nama penerima",
  bankName: "Nama bank",
};
```

`eventDate` harus menggunakan ISO 8601 dengan offset timezone. Untuk acara di Indonesia bagian barat, gunakan `+07:00`; untuk WITA gunakan `+08:00`, dan untuk WIT gunakan `+09:00`. Countdown membaca nilai ini setiap detik dan tidak akan menampilkan angka negatif setelah acara berlangsung.

Nama orang tua saat ini tersedia di konfigurasi dan dapat digunakan untuk menambahkan copy di section cerita atau footer. Jika ingin menampilkan field tersebut di halaman, tambahkan markup di section yang sesuai; jangan membuat objek konfigurasi kedua.

## 3. Mengatur nama tamu dari URL

Nama tamu dibaca dari query parameter `to`. Contoh URL:

```text
https://undanganteam-eatbrkw8.manus.space/?to=Keluarga%20Budi%20Santoso
```

Spasi pada nama harus di-encode menjadi `%20`. Jika parameter tidak tersedia, halaman menampilkan `Tamu undangan`. Nilai tersebut dirapikan whitespace-nya, dipotong maksimal 70 karakter, dan dirender sebagai teks biasa sehingga tidak diperlakukan sebagai HTML.

Untuk membuat tautan tamu secara manual di spreadsheet, gunakan pola berikut:

```text
=BASE_URL&"?to="&ENCODEURL(A2)
```

Pastikan `BASE_URL` tidak diakhiri karakter `?` atau `&` agar query tidak menjadi rangkap.

## 4. Mengganti foto dan aset visual

Array `images` di `Home.tsx` menyimpan enam foto galeri. Setiap item memiliki `src`, `alt`, dan `caption`.

```tsx
{
  src: "/manus-storage/nama-file.webp",
  alt: "Deskripsi foto untuk pembaca layar",
  caption: "03 / judul foto"
}
```

Gunakan foto milik sendiri atau aset yang memiliki izin penggunaan. Untuk aset besar, simpan file asli di luar folder proyek pada `/home/ubuntu/webdev-static-assets/`, lalu unggah menggunakan mekanisme penyimpanan aset WebDev dan gunakan URL hasil unggahan tersebut. Jangan menaruh foto besar di `client/public/` atau `client/src/assets/` karena dapat memperlambat deployment.

Setiap foto harus memiliki `alt` yang deskriptif. Jangan memakai foto yang sama untuk beberapa item galeri kecuali memang disengaja. Key React item galeri dibuat dari indeks dan caption, tetapi URL foto tetap sebaiknya unik agar pengalaman galeri tidak membingungkan.

Hero dan story menggunakan URL berikut di `Home.tsx`:

```tsx
<img src="/manus-storage/coastal-hero_d2f7403f.jpg" alt="..." />
<img src="/manus-storage/coastal-story_a40cda43.jpg" alt="..." />
```

Ganti URL tersebut dengan aset hero/story baru, dan pertahankan overlay gelap jika foto memiliki area terang di belakang teks. Emblem digunakan di cover, header, dan footer. Gunakan file PNG transparan dengan simbol saja, tanpa teks, pada URL berikut:

```tsx
<img src="/manus-storage/coastal-emblem_7b957b6a.png" alt="" />
```

## 5. Mengubah tema visual

Tema saat ini bernama **Coastal Editorial**. Palet utama didefinisikan sebagai custom properties di bagian atas `client/src/index.css`.

| Token | Nilai saat ini | Peran |
|---|---|---|
| `--sand` | `#f4efe7` | Latar kertas hangat |
| `--paper` | `#fbf9f5` | Latar section terang |
| `--espresso` | `#28231f` | Section gelap dan teks utama |
| `--terracotta` | `#c86b4a` | Aksen, CTA, dan garis horizon |
| `--olive` | `#85886d` | Aksen organik tambahan |
| `--line` | `#d9cec1` | Garis editorial |

Jika mengganti tema, ubah token warna terlebih dahulu lalu cek kontras teks terhadap background. Hindari mengganti warna langsung pada banyak komponen karena akan membuat theme sulit dirawat. Font saat ini menggunakan **Cormorant Garamond** untuk display dan **DM Sans** untuk body, dimuat melalui Google Fonts di `index.css`.

Untuk perubahan identitas besar, perbarui juga `ideas.md` agar keputusan desain terdokumentasi. Pertahankan satu arah visual yang konsisten: jangan mencampur gaya coastal, art deco, dan dark romantic dalam satu halaman tanpa alasan desain yang jelas.

## 6. Mengganti copy dan section

Copy utama berada langsung di JSX `Home.tsx`. Cari heading berikut untuk mengganti teks personal:

| Copy saat ini | Lokasi |
|---|---|
| `Dua langkah, satu arah.` | Hero |
| `Berawal dari sebuah kebetulan.` | Cerita |
| `Hari yang kami nantikan.` | Detail acara |
| `Beberapa frame dari perjalanan.` | Galeri |
| `Sampaikan kabar baikmu.` | RSVP |
| `Doa dan tanda kasih.` | Amplop digital |

Gunakan kalimat yang spesifik tentang pasangan. Hindari filler seperti “Welcome to our website” atau “Get started today”. Copy dapat tetap puitis, tetapi informasi tanggal, waktu, venue, dan tindakan tombol harus konkret.

## 7. Mengatur Google Maps dan Google Calendar

`config.maps` adalah URL yang dibuka pada tab baru melalui tombol **Lihat lokasi**. Gunakan URL lokasi venue yang benar dan uji di browser desktop serta mobile.

`config.calendar` adalah URL Google Calendar dengan parameter event. Pastikan judul, tanggal mulai, tanggal selesai, timezone, deskripsi, dan lokasi sudah benar. Format tanggal pada URL Google Calendar menggunakan `YYYYMMDDTHHMMSSZ` untuk UTC atau format dengan timezone yang sesuai. Contoh URL dapat dibuat dari template berikut:

```text
https://calendar.google.com/calendar/render?action=TEMPLATE&text=Judul&dates=20261114T083000Z/20261114T140000Z&details=Deskripsi&location=Alamat
```

Karakter khusus pada judul, deskripsi, dan lokasi harus di-encode. Setelah mengubah URL, klik tombol dari halaman undangan dan pastikan form event Google Calendar terisi sesuai waktu lokal.

## 8. Mengganti musik latar

Kontrol musik visual sudah tersedia melalui tombol floating, tetapi URL audio belum ditambahkan sebagai aset final. Untuk mengaktifkan audio sebenarnya, tambahkan elemen audio yang dikontrol oleh state `playing` dan gunakan file instrumental berlisensi.

Contoh pola implementasi:

```tsx
const audioRef = useRef<HTMLAudioElement>(null);

useEffect(() => {
  if (!audioRef.current) return;
  audioRef.current.loop = true;
  audioRef.current.volume = 0.25;
  if (playing) audioRef.current.play().catch(() => setPlaying(false));
  else audioRef.current.pause();
}, [playing]);

<audio ref={audioRef} src="/manus-storage/lagu-instrumental.mp3" preload="none" />
```

Playback dimulai setelah pengguna menekan **Buka undangan**, bukan melalui autoplay paksa. Jika browser menolak playback, tombol harus tetap menampilkan status yang dapat dicoba kembali oleh pengguna. Jangan menggunakan lagu yang tidak memiliki izin publikasi.

## 9. Mengatur RSVP dan buku tamu

Form RSVP meminta nama, status kehadiran, dan pesan. Validasi saat ini mengharuskan nama dan pesan tidak kosong. Setelah submit, pesan dimasukkan ke state dan `localStorage` browser dengan key `nara-elang-guestbook`.

Konsekuensinya, data RSVP pada versi frontend-only **belum terkirim ke server** dan hanya terlihat pada browser/perangkat tempat form dikirim. Ini cocok untuk preview atau undangan sederhana, tetapi tidak cukup untuk rekap tamu lintas perangkat.

Untuk RSVP terpusat, gunakan backend/database dan ubah fungsi `submit`. Jangan memasukkan seed message, testimonial, rating, atau review buatan sebagai isi awal buku tamu. Empty state yang benar adalah:

> Pesan ucapanmu akan muncul di sini setelah dikirim.

Jika menambahkan backend pada masa depan, pertahankan loading state, success state, error state, validasi server-side, rate limit, dan sanitasi pesan sebelum ditampilkan.

## 10. Mengatur amplop digital

Ganti field `ewallet`, `bank`, `receiver`, dan `bankName` pada `config`. QR code saat ini dibuat dari payload e-wallet melalui layanan QR eksternal:

```tsx
https://api.qrserver.com/v1/create-qr-code/?size=180x180&data=...
```

Uji QR code menggunakan lebih dari satu perangkat sebelum publikasi. Tombol salin menggunakan Clipboard API dan fallback textarea. Label sukses ditampilkan melalui toast. Pastikan rekening dan e-wallet sudah benar sebelum menyebarkan tautan; data pembayaran tidak boleh dibiarkan sebagai nilai contoh.

## 11. Responsive dan aksesibilitas

Website dirancang mobile-first dan memiliki breakpoint utama pada `700px`. Pada desktop tersedia header dengan anchor navigation. Pada mobile tersedia sticky bottom navigation agar tombol utama tidak tersembunyi di balik menu hamburger.

Saat menambah elemen, selalu berikan label tombol yang jelas, `alt` pada gambar, label form yang berhubungan dengan input, fokus keyboard yang terlihat, dan `aria-label` untuk ikon-only button. Lightbox mendukung `Escape`, `ArrowLeft`, dan `ArrowRight`, serta mengunci scroll body ketika terbuka.

Sebelum publikasi, uji lebar sekitar 320px, 375px, 768px, dan 1280px. Uji juga dengan keyboard saja dan dengan `prefers-reduced-motion: reduce` pada browser DevTools. Jangan menambahkan fixed element baru tanpa memastikan ia tidak menutupi tombol atau isi penting.

## 12. Pemeriksaan lokal sebelum commit

Jalankan pemeriksaan berikut dari root repository:

```bash
pnpm check
pnpm build
```

`pnpm check` memastikan TypeScript tidak memiliki error. `pnpm build` menjalankan Vite production build dan bundling server scaffold. Peringatan ukuran chunk atau resolusi URL storage dapat muncul sebagai warning build, tetapi error TypeScript atau kegagalan build harus diperbaiki sebelum commit.

Checklist fungsional minimum:

| Skenario | Hasil yang diharapkan |
|---|---|
| Buka `/` | Cover tampil dengan fallback `Tamu undangan` |
| Buka `/?to=Nama%20Tamu` | Nama tamu tampil sebagai teks aman |
| Klik Buka undangan | Cover slide-up dan isi dapat di-scroll |
| Klik navigasi | Scroll menuju section yang benar |
| Buka foto | Lightbox tampil; Escape dan tombol panah bekerja |
| Submit RSVP kosong | Muncul validasi tanpa reload |
| Submit RSVP valid | Pesan masuk ke buku tamu lokal |
| Klik salin rekening | Nilai masuk clipboard/toast sukses tampil |
| Klik Maps/Calendar | Tab baru terbuka dengan URL yang sesuai |
| Mobile | Bottom navigation tidak menutupi tombol utama |

## 13. Commit dan sinkronisasi GitHub

Periksa perubahan sebelum commit:

```bash
git status
git diff -- HOW_TO_CUSTOMIZE.md client/src/pages/Home.tsx client/src/index.css
```

Buat commit dengan pesan yang deskriptif:

```bash
git add HOW_TO_CUSTOMIZE.md todo.md
git commit -m "docs: add customization guide for wedding invitation"
```

Repository GitHub yang terhubung adalah repository privat `alpharka/08-28-coastal-orange-b`. Untuk melihat status remote:

```bash
git remote -v
git branch --show-current
```

Dorong perubahan ke branch aktif dengan:

```bash
git push user_github HEAD
```

Jika repository meminta branch utama secara eksplisit, gunakan `git push user_github main` setelah memastikan branch lokal memang `main`. Jangan menggunakan force push kecuali sudah ada konfirmasi eksplisit karena dapat menimpa pekerjaan remote.

## 14. Publikasi melalui Manus

Setelah perubahan tersimpan pada checkpoint proyek, gunakan tombol **Publish** di panel Manus. Domain proyek saat ini adalah:

```text
https://undanganteam-eatbrkw8.manus.space
```

Gunakan query parameter `to` untuk membuat tautan personal per tamu. Sebelum membagikan, lakukan smoke test pada domain publik, khususnya aset hero/gallery, Google Calendar, Maps, QR code, RSVP, dan tombol salin.

## 15. Ringkasan file yang biasanya diubah

Untuk kustomisasi normal, perubahan cukup dilakukan pada `client/src/pages/Home.tsx`, `client/src/index.css`, `client/index.html`, dan aset yang diunggah. Hindari mengubah `server/index.ts`, konfigurasi authentication, atau file generated `dist/` untuk kebutuhan isi undangan. Bila kebutuhan berubah menjadi RSVP terpusat atau dashboard admin, rencanakan upgrade ke project full-stack sebelum menambahkan endpoint atau database.
