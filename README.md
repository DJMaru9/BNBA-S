# BNBA SPPG Bantaeng Gantarangkeke

Aplikasi pendataan penerima manfaat program Makan Bergizi Gratis (MBG) untuk
Satuan Pelayanan Pemenuhan Gizi Bantaeng Gantarangkeke, Badan Gizi Nasional.

Mencakup data siswa sekolah beserta guru dan tenaga kependidikan, serta kelompok 3B
(ibu hamil, ibu menyusui, ibu nifas, dan balita/baduta).

---

## Isi paket

| Berkas | Kegunaan | Diunggah ke GitHub? |
|---|---|---|
| `index.html` | Seluruh aplikasi dalam satu berkas | **Ya** |
| `Code.gs` | Backend Apps Script | **Tidak** — berisi token |
| `template-impor-siswa.csv` | Contoh susunan kolom data siswa | Boleh |
| `template-impor-3b.csv` | Contoh susunan kolom data 3B | Boleh |

---

## Pemasangan

### 1. Spreadsheet dan Apps Script

1. Buat Spreadsheet kosong di Google Drive.
2. Salin ID-nya dari URL — bagian panjang di antara `/d/` dan `/edit`.
3. Buka **Extensions → Apps Script**, hapus isi bawaan, tempel seluruh isi `Code.gs`.
4. Isi `SS_ID` dengan ID tadi.
5. Ganti `TOKEN` dengan kata sandi bebas. Catat, akan dipakai lagi nanti.

### 2. Jalankan setup()

Pilih fungsi `setup` pada dropdown editor, tekan **Run**. Google meminta izin akses —
pilih akun Anda, tekan **Advanced**, lalu **Go to project (unsafe)**. Ini normal untuk
skrip buatan sendiri.

Setelah selesai, Spreadsheet harus berisi tiga sheet baru:
`BNBA_SEKOLAH`, `BNBA_3B`, dan `MASTER_SEKOLAH`.

### 3. Deploy sebagai Web app

**Deploy → New deployment → ikon gerigi → Web app**

| Isian | Nilai |
|---|---|
| Execute as | Me |
| Who has access | Anyone |

Salin **Web app URL** yang berakhiran `/exec`.

> Saat mengubah `Code.gs` di kemudian hari, gunakan
> **Deploy → Manage deployments → Edit → New version**.
> Jangan buat deployment baru, karena URL-nya akan berubah.

### 4. Unggah ke GitHub Pages

1. Buat repository baru, set **Public**.
2. Unggah `index.html` (dan template CSV bila mau). **Jangan unggah `Code.gs`.**
3. **Settings → Pages → Source: Deploy from a branch → main / (root)**.
4. Tunggu 1–2 menit. Alamatnya menjadi `https://<username>.github.io/<repo>/`

### 5. Sambungkan

Buka alamat GitHub Pages, masuk menu **Pengaturan**, tempel Web app URL dan token,
lalu tekan **Simpan & uji sambungan**. Bila berhasil, penanda di sidebar berubah
menjadi *Tersinkron* dan daftar 22 sekolah otomatis terkirim ke `MASTER_SEKOLAH`.

### 6. Opsional: subdomain sendiri

Di panel hosting, tambahkan record **CNAME**: `bnba` → `<username>.github.io`.
Di repository, buat berkas bernama `CNAME` (huruf besar, tanpa ekstensi) berisi satu
baris `bnba.domainanda.com`. Isikan alamat yang sama di **Settings → Pages → Custom
domain**, tunggu sertifikat terbit, lalu centang **Enforce HTTPS**.

---

## Keamanan data

Data BNBA berisi NIK, nama, tanggal lahir, dan alamat anak — data pribadi yang
dilindungi UU Perlindungan Data Pribadi. Beberapa hal yang wajib diperhatikan:

- **Jangan pernah mengunggah `Code.gs` ke repository publik.** Berkas itu memuat
  token dan ID Spreadsheet.
- **Jangan membagikan token** lewat tangkapan layar, grup WhatsApp, atau catatan
  bersama. Siapa pun yang tahu URL `/exec` dan token bisa membaca seluruh data.
- Token tersimpan di peramban masing-masing perangkat. Pada komputer bersama,
  kosongkan lewat **Pengaturan → Kosongkan cache lokal** setelah selesai.
- Bila seluruh operator punya akun Google, pertimbangkan mengganti *Who has access*
  menjadi **Anyone with Google account**.

---

## Menyiapkan berkas impor

Baris pertama harus berisi nama kolom. Aplikasi mencocokkan nama kolom secara
otomatis dan menampilkan pemetaannya untuk diperiksa sebelum diunggah.

### Kolom data siswa

| Kolom | Wajib | Catatan |
|---|---|---|
| Nama Lengkap | **Ya** | Baris tanpa nama tidak diimpor |
| NIK | Sangat disarankan | Dipakai memeriksa data ganda |
| NISN | — | Hanya untuk siswa |
| Jenis Kelamin | — | `L` / `P` / `Laki-laki` / `Perempuan` |
| Tanggal Lahir | — | Bebas format; umur dihitung otomatis |
| Nama Sekolah | **Ya** | Harus sama persis dengan daftar di master |
| Kelas | — | `A`, `B` untuk TK; `1`–`12` untuk lainnya |
| Peran | — | Kosong dianggap Siswa |
| Desa | — | Kaloling atau Tombolo |

Kolom **Jenjang** dan **Porsi** tidak perlu diisi — keduanya terisi otomatis dari
master sekolah dan kelas.

### Istilah peran yang dikenali

Tidak membedakan huruf besar-kecil.

- **Siswa** — siswa, murid, peserta didik, santri, pelajar
- **Guru** — guru, pendidik, tenaga pendidik, guru kelas, wali kelas, kepala sekolah,
  kepsek, kamad, ustadz/ustadzah, pembina
- **Tendik** — tendik, tenaga kependidikan, TU, tata usaha, operator, admin,
  pustakawan, laboran, penjaga sekolah, satpam, bendahara

Istilah di luar daftar itu, misalnya "Honorer" atau "PNS", sengaja **tidak ditebak**.
Barisnya tetap terimpor dengan peran kosong dan dilaporkan sebelum diunggah, supaya
tidak diam-diam salah masuk hitungan porsi.

Bila berkas guru dan siswa Anda terpisah, gunakan pilihan
**Seluruh berkas ini: Guru / Tendik / Siswa** pada layar impor.

### Kolom data 3B

Kategori yang dikenali: `Bumil`, `Busui`, `Bufas`, `Balita/Baduta`.

---

## Aturan pembagian porsi

| Jenjang | Siswa | Guru & tendik |
|---|---|---|
| PAUD/TK | seluruhnya **porsi kecil** | **porsi kecil** |
| SD/MI | kelas 1–3 kecil, kelas 4–6 besar | porsi besar |
| SMP/MTs | seluruhnya porsi besar | porsi besar |
| SMA/MA | seluruhnya porsi besar | porsi besar |

Porsi terisi otomatis saat impor maupun saat menambah data lewat formulir.

---

## Catatan penggunaan

- Menu **Rincian per sekolah** dibuka dengan mengklik kartu *Penerima manfaat terdata*
  di dashboard, atau lewat sidebar.
- Kolom **Sasaran** di daftar sekolah bersifat opsional. Isi lewat **Kelola master**
  hanya bila Anda ingin pembanding target; biarkan kosong bila tidak perlu.
- Unduhan PDF dibuat lewat dialog cetak peramban. Pilih tujuan **Save as PDF** dan
  aktifkan **Background graphics** agar warna tabel ikut tercetak.
- Aplikasi menyimpan salinan data di peramban agar tabel tetap cepat. Tekan
  **Muat ulang** untuk menyegarkan dari Google Sheets.
