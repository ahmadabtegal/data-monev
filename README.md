# Monitoring Perizinan DPMPTSP Kota Tegal — GitHub Pages + Apps Script

## Isi paket
- `index.html`: frontend publik dan panel admin (input, edit, hapus data).
- `Code.gs`: API Google Apps Script, autentikasi admin, CRUD spreadsheet, dan fungsi notifikasi WhatsApp.
- `assets/logo-kota-tegal.png`, `assets/logo-dpmptsp.png`, `assets/favicon.png`.
- `README.md`: panduan pemasangan.

## A. Pengamanan awal
Script lama yang dikirim sebelumnya berisi token Fonnte hardcoded. Anggap token itu telah terekspos dan **rotasi/cabut token lama di Fonnte**. Jangan commit token baru ke GitHub.

Pada Apps Script: **Project Settings → Script properties**, buat:
- `FONNTE_TOKEN` = token Fonnte yang baru
- `ADMIN_PASSWORD` = password admin yang kuat (minimal 14 karakter disarankan)

Password admin disimpan hanya di Script Properties, bukan di HTML atau repository. Bagikan password hanya kepada admin yang berwenang. Sesi admin berlaku 30 menit dan disimpan di sessionStorage browser.

## B. Pasang backend
1. Buka spreadsheet sumber, pilih **Extensions → Apps Script**.
2. Salin isi `Code.gs` paket ini ke project Apps Script yang terikat ke spreadsheet. Simpan salinan kode lama terlebih dahulu.
3. Sheet data harus bernama `Form_Responses`.
4. Pastikan Script Properties pada bagian A sudah diisi.
5. Deploy → **New deployment** → **Web app**.
6. Execute as: **Me**. Who has access: **Anyone**, hanya jika diizinkan kebijakan keamanan instansi.
7. Deploy dan salin URL Web App yang berakhir `/exec`.

Backend menambahkan header `Latitude` dan `Longitude` secara otomatis bila belum tersedia. Header data lama yang dikenali: `Timestamp`, `Nama Pelaku Usaha`, `Nomor WA`, `Bidang Usaha`, `Lokasi`, `Kelurahan`, `Kecamatan`, `Status Perizinan`, `Catatan`, `Tanggal Monitoring`, `Foto`, `Column 1`. Koordinat ditambahkan di kolom baru.

## C. Konfigurasi frontend
1. Buka `index.html`.
2. Ganti `GANTI_DENGAN_URL_WEB_APP_APPS_SCRIPT` di `const API_URL` dengan URL `/exec` deployment Anda.
3. Jangan memasukkan password admin atau token Fonnte ke file ini.

## D. Hosting di GitHub Pages
1. Buat repository baru, misalnya `monitoring-perizinan-tegal`.
2. Upload `index.html` dan folder `assets/` ke root repository. README boleh ikut diunggah.
3. Repository → **Settings → Pages** → Deploy from a branch → branch `main` → folder `/(root)` → Save.
4. Buka URL GitHub Pages yang diberikan.

## E. Menggunakan panel admin
1. Buka website dan klik **Panel Admin**.
2. Masukkan password yang disetel pada Script Properties `ADMIN_PASSWORD`.
3. Klik **Input Data** untuk membuat data; klik **Edit** atau **Hapus** pada daftar admin untuk mengelola data.
4. Isian koordinat dapat dilakukan manual pada Latitude/Longitude atau dengan tombol **Ambil koordinat dari perangkat** (perlu HTTPS dan izin lokasi browser).
5. Simpan. Frontend meminta backend memperbarui Google Sheets lalu memuat ulang daftar.

Latitude adalah angka desimal antara -90 dan 90; longitude antara -180 dan 180. Isi keduanya bersama-sama. Jika kosong, tombol peta memakai pencarian alamat + kelurahan + kecamatan + Kota Tegal.

## F. Foto publik
Tautan foto Google Drive hanya akan terlihat jika file tersebut memang bisa diakses oleh pengunjung website. Jangan membuka akses seluruh folder Drive jika berisi dokumen sensitif. Foto yang tidak bisa diakses akan menampilkan placeholder.

## G. Notifikasi WhatsApp dan trigger lama
Fungsi `kirimWAotomatis(e)`, `kirimHarianOtomatis()`, `kirimUlangManual(row)`, dan menu spreadsheet tetap disertakan. Pembuatan data lewat panel admin menulis langsung ke Sheets; **trigger on form submit tidak otomatis berjalan untuk baris yang dibuat lewat script**. Jika input admin juga harus mengirim WhatsApp, lakukan pengujian terpisah dan tentukan aturan pengiriman agar tidak mengirim pesan tanpa sengaja. Jangan menghapus trigger lama sebelum memverifikasi penggantinya.

## H. Penting sebelum publikasi
- API publik memang menampilkan nama usaha, bidang usaha, alamat, kelurahan, kecamatan, status, catatan monitoring, tanggal, foto, dan koordinat. Pastikan data tersebut memang layak dipublikasikan.
- Nomor WhatsApp dan status pesan tidak ditampilkan pada halaman publik.
- Deployment Web App harus di-update setelah perubahan kode Apps Script: Deploy → Manage deployments → Edit → New version → Deploy.
- Pengujian file yang dibuat belum bisa membuktikan integrasi dengan deployment aktif. Setelah pemasangan, uji login salah/benar, tambah, edit, hapus, foto, dan peta pada data percobaan terlebih dahulu.
