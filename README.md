# SIM Sekolah — Portal Login & Dashboard Multi-Peran

Aplikasi web statis (HTML + CSS + JS, tanpa perlu server/database) untuk portal
login sekolah dengan dashboard berbeda untuk tiap jabatan:

- Guru BK
- Wakasek Kesiswaan
- Wakasek Kurikulum
- Wakasek Humas
- Wakasek Sarana Prasarana
- Tata Usaha
- Organisasi Siswa: OSIS, Pramuka, PMR, Paskibra
- Kepala Sekolah (memantau semua divisi & organisasi di atas)
- Pengawas Sekolah (memantau semua divisi & organisasi di atas)

## Struktur file

```
index.html                                ← halaman login + seluruh dashboard (HTML/CSS/JS jadi satu)
data/users.json                           ← daftar akun, ROLE_INFO & MONITORING_ROLES untuk login
data/siswa.json                           ← master data siswa: array [{nisn, nama, kelas}, ...]
data/kurikulum.json                       ← data khusus dashboard Wakasek Kurikulum
data/humas.json                           ← data khusus dashboard Wakasek Humas
data/sarpras.json                         ← data khusus dashboard Wakasek Sarpras
data/tata-usaha.json                      ← data khusus dashboard Tata Usaha
data/waksis/guru-bk.json                  ← data khusus dashboard Guru BK
data/waksis/wakasek-kesiswaan.json        ← data khusus dashboard Wakasek Kesiswaan
data/waksis/osis.json                     ← data khusus dashboard OSIS
data/waksis/pramuka.json                  ← data khusus dashboard Pramuka
data/waksis/pmr.json                      ← data khusus dashboard PMR
data/waksis/paskibra.json                 ← data khusus dashboard Paskibra
data/waksis/laporan-organisasi.json       ← daftar organisasi siswa yang dipantau Wakasek Kesiswaan / Kepala Sekolah / Pengawas
```

**Folder `data/waksis/`** mengelompokkan seluruh data yang berhubungan dengan
ruang lingkup Wakasek Kesiswaan: data Guru BK, data organisasi siswa
(OSIS/Pramuka/PMR/Paskibra + daftar organisasinya), dan data Kesiswaan itu
sendiri — mengikuti fakta bahwa Wakasek Kesiswaan-lah yang memantau ketiganya
lewat tab "Laporan BK" dan "Laporan Organisasi" (lihat bagian *Fitur tiap
dashboard* di bawah). Divisi lain (Kurikulum, Humas, Sarpras, Tata Usaha)
tetap di `data/` langsung karena tidak dipantau lintas-tab oleh siapa pun.

**Perubahan dari versi sebelumnya:** semua file di folder `data/` sekarang
adalah file **`.json` murni** (bukan lagi `.js` yang mendefinisikan
`const DATA_XXX = {...}`). `index.html` memuat semuanya lewat `fetch()` di
dalam `<script>` (bukan lagi lewat tag `<script src="data/....js">`).
Keuntungannya: file `.json` bisa dibuka/diedit oleh siapa pun tanpa perlu
mengerti sintaks JavaScript sama sekali (tidak ada `const`, tidak ada
`//` komentar, murni data), dan lebih mudah dihasilkan/diproses oleh
program lain (Python, Google Sheets → JSON, dsb).

`data/siswa.json` sekarang juga sudah berupa array JSON biasa
`[{ "nisn": "...", "nama": "...", "kelas": "..." }, ...]` — format teks
`NISN@Nama@Kelas` dengan pemisah `🚟` pada versi lama sudah tidak dipakai
lagi.

`data/waksis/laporan-organisasi.json` menyimpan field `"dataKey"` (string, misalnya
`"osis"`) alih-alih referensi objek JS langsung seperti versi lama
(`dataVar: DATA_OSIS`), karena JSON tidak bisa menyimpan referensi kode.
`index.html` menyambungkan kembali `dataKey` ini ke data organisasi yang
sesuai setelah semua file JSON selesai dimuat.

⚠️ **Karena sekarang memakai `fetch()`, aplikasi harus diakses lewat
server web** (GitHub Pages, atau server lokal seperti
`python3 -m http.server` lalu buka `http://localhost:8000`). Membuka
`index.html` langsung dengan cara klik dua kali dari File Explorer
(`file://...`) biasanya akan diblokir browser saat memuat file `.json`
karena kebijakan CORS pada skema `file://`.

## Cara deploy ke GitHub Pages

1. Buat repository baru di GitHub (bisa publik atau privat jika punya GitHub Pro/Team/EDU).
2. Upload seluruh isi folder ini (`index.html` dan folder `data/`) ke root repository tersebut.
3. Buka **Settings → Pages** di repository.
4. Pada **Source**, pilih branch `main` dan folder `/ (root)`, lalu **Save**.
5. Tunggu 1–2 menit, GitHub akan memberi URL seperti:
   `https://<username-anda>.github.io/<nama-repo>/`
6. Buka URL tersebut — halaman login akan langsung muncul.

## Akun demo (ganti sebelum dipakai sungguhan)

Semua akun didefinisikan di `data/users.json`:

| Username    | Password       | Peran                  |
|-------------|----------------|-------------------------|
| gurubk      | bk123          | Guru BK                 |
| wakesis     | wakesis123     | Wakasek Kesiswaan       |
| kurikulum   | kurikulum123   | Wakasek Kurikulum       |
| humas       | humas123       | Wakasek Humas           |
| sarpras     | sarpras123     | Wakasek Sarpras         |
| tu          | tu123          | Tata Usaha              |
| osis        | osis123        | Ketua OSIS              |
| pramuka     | pramuka123     | Pradana (Ketua Pramuka) |
| pmr         | pmr123         | Ketua PMR               |
| paskibra    | paskibra123    | Komandan Paskibra       |
| kepsek      | kepsek123      | Kepala Sekolah          |
| pengawas    | pengawas123    | Pengawas Sekolah        |

Untuk mengganti nama sekolah, buka `index.html`, cari objek `CONFIG` di
bagian `<script>` paling bawah, dan ubah `schoolName`.

Untuk mengubah data dummy (kasus BK, kegiatan, anggota organisasi, dsb.),
cukup buka file `.json` yang sesuai di folder `data/` dan edit langsung —
tidak perlu menyentuh `index.html` sama sekali.

## Fitur tiap dashboard

- **Guru BK**: ringkasan kasus, data kasus BK (bisa tambah kasus baru), jadwal konseling.
- **Wakasek Kesiswaan**: ringkasan, pelanggaran siswa (bisa tambah), prestasi siswa (bisa tambah), daftar
  ekstrakurikuler, **Laporan BK** (baca data Guru BK, read-only), **Laporan Organisasi** (baca data 4 organisasi
  siswa, read-only), dan **Admin BK & Organisasi** — panel untuk membuat akun login (username & password) baru
  bagi Guru BK atau salah satu organisasi siswa (lihat penjelasan lengkap di bawah).
- **Wakasek Kurikulum**: ringkasan, jadwal ujian (bisa tambah agenda), capaian kurikulum, daftar mata pelajaran.
- **Wakasek Humas**: ringkasan, kegiatan (bisa tambah), kerjasama mitra, publikasi (bisa tambah).
- **Wakasek Sarpras**: ringkasan aset, inventaris, pengajuan perbaikan (bisa tambah), kondisi ruang.
- **Tata Usaha**: ringkasan, surat masuk (bisa tambah), surat keluar (bisa tambah), administrasi siswa/mutasi (bisa tambah), keuangan & pembayaran siswa (bisa tambah).
- **OSIS / Pramuka / PMR / Paskibra** (organisasi siswa): setiap organisasi punya dashboard sendiri dengan tab
  Ringkasan, Anggota/Pengurus (bisa tambah), Program Kerja (bisa tambah), Kegiatan (bisa tambah),
  Keuangan/Kas organisasi (bisa tambah pemasukan-pengeluaran, saldo dihitung otomatis), dan
  Laporan Pertanggungjawaban/LPJ (bisa tambah laporan berkala) — inilah bentuk pertanggungjawaban
  organisasi ke Pembina, Wakasek Kesiswaan, dan pimpinan sekolah.
- **Kepala Sekolah & Pengawas Sekolah**: ringkasan lintas semua divisi & organisasi di atas (read-only) ditambah halaman
  **Catatan Pemantauan** untuk menuliskan arahan/pembinaan yang tersimpan dan bisa dilihat bersama.

Data baru yang ditambahkan lewat form "+ Tambah ..." langsung ikut muncul di
dashboard Kepala Sekolah dan Pengawas Sekolah, karena keduanya membaca sumber
data yang sama.

## Admin BK & Organisasi (fitur Wakasek Kesiswaan)

Tab **"Admin BK & Organisasi"** di dashboard Wakasek Kesiswaan memungkinkan
Wakasek Kesiswaan membuat akun login baru untuk Guru BK atau salah satu
organisasi siswa (OSIS/Pramuka/PMR/Paskibra), tanpa perlu mengedit
`data/users.json` secara manual:

1. Isi form "Buat akun Guru BK / Organisasi": pilih peran (Guru BK / OSIS /
   Pramuka / PMR / Paskibra), nama lengkap, username, dan password.
2. Klik "Simpan data". Aplikasi otomatis menolak jika username kosong,
   password kosong, atau username sudah dipakai akun lain (baik akun bawaan
   maupun akun buatan sebelumnya).
3. Akun baru langsung muncul di tabel "Akun yang dibuat lewat panel ini"
   (bisa diedit/dihapus lagi dari tabel tersebut), dan **langsung bisa
   dipakai untuk login** oleh pemiliknya di halaman login.
4. Akun bawaan/demo (yang sudah ada sejak awal di `data/users.json`)
   ditampilkan terpisah di tabel "Akun bawaan (demo)" sebagai referensi,
   dan tidak bisa diubah lewat panel ini.

⚠️ **Cara kerja di baliknya (penting dipahami):** karena aplikasi ini statis
tanpa server/database, akun baru **tidak benar-benar dituliskan ke dalam
file `data/users.json`**. Akun tersebut disimpan di `localStorage` browser
(kunci `extra_waksis_admin_users`), lalu digabungkan dengan `data/users.json`
setiap kali ada yang mencoba login. Artinya:

- Akun baru hanya bisa dipakai login **dari browser/perangkat yang sama**
  tempat Wakasek Kesiswaan membuatnya (atau perangkat lain yang datanya
  disalin secara manual). Ini adalah keterbatasan yang sama seperti seluruh
  data tambahan lain di aplikasi ini (lihat bagian *Keterbatasan* di bawah).
- Jika ingin akun benar-benar tersimpan permanen di `data/users.json` dan
  bisa dipakai login dari perangkat mana pun, tambahkan akun tersebut secara
  manual ke file `data/users.json` (lalu commit/upload ulang ke GitHub Pages),
  atau hubungkan aplikasi ini ke backend/database sungguhan.

## Menambah organisasi siswa baru (mis. Rohis, KIR, dsb.)

Karena dashboard organisasi dibangun secara generik dari `data/laporan-organisasi.json`,
menambah organisasi baru tidak perlu mengubah kode dashboard sama sekali:

1. Buat file baru, misalnya `data/waksis/rohis.json`, dengan struktur field
   yang sama seperti `data/waksis/osis.json` (`namaOrganisasi`, `periode`,
   `pembina`, `anggota`, `programKerja`, `kegiatan`, `keuangan`, `lpj`).
2. Tambahkan akun login untuk organisasi tersebut di `data/users.json`
   (isi `role` dengan id organisasi, misalnya `"rohis"`, di dalam array `users`)
   — atau, lebih mudah, biarkan Wakasek Kesiswaan membuatkan akunnya sendiri
   lewat tab **Admin BK & Organisasi** setelah langkah 4 di bawah selesai
   (organisasi baru otomatis muncul di pilihan "Dibuatkan untuk").
3. Tambahkan tampilan role-nya di objek `roleInfo` (juga di `data/users.json`).
4. Tambahkan satu entri baru di array `data/waksis/laporan-organisasi.json`, misalnya:
   ```json
   { "id": "rohis", "roleKey": "rohis", "label": "Rohis", "color": "#0891b2", "dataKey": "rohis" }
   ```
5. Di `index.html`, tambahkan `"data/waksis/rohis.json"` pada daftar `DATA_FILES`
   dan pada bagian `Promise.all([...])` / `ORG_DATA_BY_KEY` di dekat awal
   `<script>` paling bawah, mengikuti pola organisasi yang sudah ada
   (osis/pramuka/pmr/paskibra), supaya file barunya ikut diambil lewat
   `fetch()` dan `dataKey: "rohis"` bisa disambungkan ke datanya.

Setelah itu, organisasi baru otomatis punya dashboard lengkap (Ringkasan,
Anggota, Program Kerja, Kegiatan, Keuangan, LPJ) dan otomatis muncul di
tab "Laporan Organisasi" milik Wakasek Kesiswaan serta tab "Organisasi Siswa"
milik Kepala Sekolah/Pengawas Sekolah.

## ⚠️ Keterbatasan penting (baca sebelum dipakai sungguhan)

Ini adalah aplikasi **statis** tanpa backend/database sungguhan, karena
dihosting di GitHub Pages. Konsekuensinya:

1. **Login bukan otentikasi aman.** Daftar username/password ada di
   `data/users.json` yang bisa dibaca siapa pun yang membuka source code
   halaman. Jangan gunakan password yang juga dipakai di sistem lain, dan
   jangan anggap ini setara sistem login sungguhan untuk data sensitif.
2. **Data yang ditambahkan lewat form disimpan di `localStorage` browser
   masing-masing perangkat.** Artinya data yang ditambahkan Guru BK di
   komputernya sendiri **tidak otomatis muncul** di komputer Kepala Sekolah,
   kecuali mereka membuka aplikasi dari perangkat/browser yang sama.
   Ini termasuk **akun baru yang dibuat lewat panel "Admin BK & Organisasi"**
   — akun tersebut hanya bisa dipakai login dari browser tempat akun itu
   dibuat, bukan dari perangkat lain manapun, kecuali ditambahkan manual
   ke `data/users.json`. Untuk data (dan akun) yang benar-benar tersinkron
   antar pengguna dan perangkat, aplikasi ini perlu dihubungkan ke backend +
   database sungguhan (misalnya Google Sheets API, Firebase, atau server sendiri).
3. Cocok dipakai untuk: demo internal, prototipe, atau penggunaan di satu
   perangkat/laboratorium bersama. Untuk penggunaan sekolah nyata dengan
   banyak pengguna di berbagai lokasi, disarankan menambahkan backend.
