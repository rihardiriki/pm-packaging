# PM – Permintaan Packaging

PWA (Progressive Web App) untuk membuat permintaan material packing per SPK. Bisa di-install di HP/PC dan tetap jalan offline.

## Isi folder

| File | Fungsi |
|---|---|
| `index.html` | Aplikasi utama |
| `Code.gs` | Backend Google Apps Script (login & sinkron riwayat). Ditempel ke Apps Script, bukan dijalankan oleh GitHub/Vercel |
| `manifest.json` | Konfigurasi install (nama, ikon, warna) |
| `sw.js` | Service worker (cache & offline) |
| `icon-192.png`, `icon-512.png`, `icon-maskable-512.png`, `apple-touch-icon.png` | Ikon aplikasi |
| `logo.svg`, `logo-mark.svg`, `logo-mark-transparan.png`, `logo-horizontal.svg`, `logo-horizontal.png`, `favicon-32.png` | Logo (SVG & PNG): ikon, tanpa latar, dan versi horizontal dengan teks |
| `.github/workflows/pages.yml` | Deploy otomatis ke GitHub Pages |

## Beberapa customer dalam satu SPK

Customer dipilih **per ukuran roll**, bukan per SPK. Contoh: dalam 1 SPK ada 3 ukuran roll, ukuran 100 → Varion, ukuran 200 → Supernova, ukuran 300 → Pakar. Tombol **＋ Customer baru** ada di tiap kartu ukuran roll.

- Form menampilkan ringkasan roll & pallet per customer.
- Dokumen, Cetak, PDF, dan WhatsApp mengelompokkan roll per customer lengkap dengan subtotal (jika customer lebih dari satu).
- Pilihan **Type permintaan** (Pallet Kayu / Pallet Plastik) muncul jika salah satu roll memakai Varion atau Supernova.
- Data lama otomatis dimigrasi: customer SPK lama menjadi customer di semua roll-nya.
- `Code.gs` tidak perlu diubah.

## Cetak & PDF: selalu 1 lembar A4

Berapa pun jumlah SPK dan itemnya, hasil **Cetak** dan **Unduh PDF** dibatasi maksimal 1 lembar A4. Aplikasi otomatis memilih ukuran huruf terbesar dan jumlah kolom (1–4) yang masih muat. Jika SPK/item sangat banyak, huruf akan mengecil.

## Upload ke GitHub

```bash
cd pm-packaging-pwa
git init
git add .
git commit -m "Initial commit"
git branch -M main
git remote add origin https://github.com/USERNAME/pm-packaging-pwa.git
git push -u origin main
```

Atau lewat web: buat repo baru → **Add file → Upload files** → seret semua isi folder (termasuk folder `.github`).

## Aktifkan GitHub Pages

1. Buka repo → **Settings → Pages**
2. Pada **Source**, pilih **GitHub Actions**
3. Tunggu proses di tab **Actions** selesai
4. Aplikasi tersedia di `https://USERNAME.github.io/pm-packaging-pwa/`

## Install di HP

- **Android (Chrome):** buka link → menu ⋮ → **Install app / Tambahkan ke layar utama**
- **iPhone (Safari):** buka link → tombol Share → **Add to Home Screen**

## Login & riwayat ikut ke perangkat lain

Aplikasi punya layar **Masuk / Daftar** (username + password). Riwayat permintaan, profil, daftar customer, master item, dan ukuran tersimpan tersimpan di akun (Google Sheet lewat Apps Script), jadi otomatis muncul saat masuk dari HP/komputer lain. Tetap bisa dipakai offline; perubahan dikirim otomatis saat online.

### Pasang backend (sekali saja)

1. Buat Google Sheet baru → **Extensions → Apps Script**.
2. Tempel isi `Code.gs` (ada di folder ini), simpan. Jangan isi `REGISTER_CODE` di file yang ikut ke repo publik; isi kode rahasia hanya di Apps Script.
3. **Deploy → New deployment → Web app** → *Execute as*: **Me**, *Who has access*: **Anyone** → Deploy → izinkan akses.
4. Salin URL Web App (berakhiran `/exec`).
5. Tempel URL itu ke konstanta `API_URL` di `index.html` (cari `const API_URL=''`). Jika dibiarkan kosong, URL bisa diisi lewat layar login.
6. Naikkan `VERSION` di `sw.js`, lalu push.

Setelah mengubah `Code.gs`: **Deploy → Manage deployments → Edit → Version: New version**, supaya URL yang sama memakai kode terbaru.

### Catatan

- Data lama di perangkat (sebelum ada login) otomatis dipindahkan ke akun pertama yang masuk di perangkat itu.
- Draft yang sedang diisi (belum "Simpan Draft") hanya ada di perangkat itu; yang sudah disimpan masuk Riwayat dan ikut tersinkron.
- Jika dua perangkat mengubah permintaan yang sama, versi yang terakhir disimpan yang dipakai.
- Pendaftaran bisa dibatasi lewat `CFG.REGISTER_CODE` atau ditutup (`CFG.ALLOW_REGISTER=false`) di `Code.gs`.

## Update aplikasi

Setelah mengubah `index.html`, naikkan `VERSION` di `sw.js` (mis. `v1` → `v2`) lalu push, supaya cache di HP pengguna ikut diperbarui.

> Service worker hanya berjalan di HTTPS (GitHub Pages sudah HTTPS) atau `localhost`.
