# Website Monitoring Perizinan DPMPTSP Kota Tegal

## Isi paket
- `index.html` — frontend statis untuk GitHub Pages.
- `Code.gs` — backend API Google Apps Script, termasuk fungsi notifikasi server-side.
- `assets/logo-kota-tegal.png` — logo Kota Tegal.
- `assets/logo-dpmptsp.png` — logo DPMPTSP.
- `assets/favicon.png` — favicon dari logo Kota Tegal.

## Penting: keamanan token
File Apps Script lama yang Anda kirim memuat token Fonnte hardcoded sebagai fallback. Anggap token tersebut sudah terekspos dan **cabut/rotasi token itu di Fonnte**. Di Apps Script, buka **Project Settings → Script properties**, tambahkan:
- Property: `FONNTE_TOKEN`
- Value: token Fonnte yang baru

Jangan pernah menaruh token di `index.html`, GitHub, atau file frontend.

## 1. Pasang backend Google Apps Script
1. Buka spreadsheet Google yang menjadi sumber data.
2. Pilih **Extensions → Apps Script**.
3. Buat/isi file script dengan isi `Code.gs` dari paket ini. Sesuaikan fungsi notifikasi lain bila project lama memiliki trigger yang masih dipakai.
4. Pastikan nama sheet data `Form_Responses`.
5. Simpan, lalu pilih **Deploy → New deployment → Web app**.
6. Execute as: **Me**.
7. Who has access: **Anyone** agar halaman publik dapat membaca API.
8. Deploy dan salin URL berakhiran `/exec`.

API hanya mempublikasikan nama usaha, bidang usaha, lokasi, kelurahan, kecamatan, status, catatan, tanggal monitoring, foto, serta koordinat jika kolom koordinat tersedia. Nomor WhatsApp dan status pesan tidak disertakan.

## 2. Konfigurasi frontend
1. Buka `index.html`.
2. Cari `const API_URL = "GANTI_DENGAN_URL_WEB_APP_APPS_SCRIPT";`
3. Ganti nilai tersebut dengan URL Web App `/exec` yang baru.
4. Simpan.

## 3. Hosting di GitHub Pages
1. Buat repository GitHub baru, misalnya `monitoring-perizinan-tegal`.
2. Unggah `index.html` dan folder `assets/` ke root repository.
3. Buka **Settings → Pages**.
4. Pilih deploy from branch, branch `main`, folder `/(root)`, lalu Save.
5. Buka URL GitHub Pages yang ditampilkan GitHub.

## 4. Struktur kolom sumber yang dikenali
Backend menggunakan header di baris pertama `Form_Responses`:
- `Nama Pelaku Usaha`
- `Nomor WA` (tidak dipublikasikan)
- `Bidang Usaha`
- `Lokasi`
- `Kelurahan`
- `Kecamatan`
- `Status Perizinan`
- `Catatan`
- `Tanggal Monitoring`
- `Foto`
- `Column 1` (status pesan; tidak dipublikasikan)

Koordinat bersifat opsional. Jika ingin lokasi yang presisi, tambahkan kolom `Latitude` dan `Longitude` ke sheet dan isi koordinat desimal. Jika kosong, tombol peta menggunakan pencarian alamat + kelurahan + kecamatan + Kota Tegal.

## 5. Foto Google Drive
Foto di spreadsheet saat ini berupa tautan Drive. Agar foto terlihat oleh semua pengunjung, file foto harus dapat diakses oleh publik melalui pengaturan berbagi Drive atau disimpan di layanan hosting gambar publik. Jangan mengubah izin folder yang berisi dokumen sensitif. Jika foto tidak bisa dimuat, kartu tetap menampilkan placeholder dan tautan sumber foto tetap disediakan bila URL tersedia.

## 6. Catatan fungsi yang dipertahankan
- `kirimHarianOtomatis()` dapat digunakan oleh trigger harian yang sudah ada.
- `kirimUlangManual(row)` mengirim ulang notifikasi dari sisi server.
- Fungsi pembaruan status admin tidak disediakan melalui API publik ini karena website ditujukan sebagai dashboard publik dan bukan panel pengelolaan.
- Jika project Apps Script lama memiliki trigger `kirimWAotomatis(e)`, periksa ulang trigger dan pasang fungsi yang sesuai sebelum menimpa script lama. Jangan menghapus trigger lama sebelum memastikan alur notifikasi pengganti sudah diuji.

## Batasan yang diketahui
Spreadsheet contoh belum menunjukkan kolom koordinat GPS. Karena itu, peta akurat per titik memerlukan kolom latitude/longitude; tanpa koordinat, Google Maps melakukan pencarian berdasarkan alamat.
