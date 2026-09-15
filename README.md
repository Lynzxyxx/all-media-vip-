# SoraPay

Web app pribadi: Spotify Playlist, TikTok/Instagram/Pinterest Downloader, Al-Qur'an, Mini Games,
Tourl (foto/video ke link), dan **Fitur VIP** (login, admin panel, fitur custom, PWA installable).

## Struktur Folder

```
sorapay-vip/
├── index.html          <- seluruh aplikasi (frontend)
├── manifest.json        <- config PWA (installable app)
├── sw.js                 <- service worker (PWA)
├── icon-192.png          <- ikon PWA
├── icon-512.png          <- ikon PWA
├── vercel.json            <- config Vercel
├── firestore.rules         <- security rules Firestore (WAJIB di-set di Firebase Console)
├── .env.example              <- daftar env var yang dibutuhkan
├── .gitignore
├── api/
│   └── config.js         <- Vercel Serverless Function, kirim config Firebase dari env var
└── README.md            <- file ini
```

---

## LANGKAH 1 — Bikin Project Firebase (gratis)

1. Buka [console.firebase.google.com](https://console.firebase.google.com) → **Add project** → kasih nama bebas (misal `sorapay-vip`) → lanjut sampai selesai.
2. Di sidebar kiri, klik **Build > Authentication** → tab **Sign-in method** → aktifkan provider **Email/Password**.
3. Di sidebar kiri, klik **Build > Firestore Database** → **Create database** → pilih mode **production** → pilih lokasi server (misal `asia-southeast2` Jakarta) → Create.
4. Masih di Firestore, buka tab **Rules**, hapus isinya, lalu **copy-paste seluruh isi file `firestore.rules`** dari folder project ini → klik **Publish**.
5. Klik ikon gerigi ⚙️ (pojok kiri atas) → **Project settings** → scroll ke bawah ke bagian **Your apps** → klik ikon **</> (Web)** → kasih nama app (bebas) → **Register app**.
6. Setelah itu muncul kode `firebaseConfig` seperti ini:

```js
const firebaseConfig = {
  apiKey: "AIzaSy...",
  authDomain: "sorapay-vip.firebaseapp.com",
  projectId: "sorapay-vip",
  storageBucket: "sorapay-vip.appspot.com",
  messagingSenderId: "123456789012",
  appId: "1:123456789012:web:abcdef123456"
};
```

**Catat 6 value ini** — nanti dipakai di Langkah 3.

---

## LANGKAH 2 — Upload ke GitHub

1. Extract file ZIP ini ke folder di komputer kamu.
2. Buat repo baru di GitHub (bisa private atau public).
3. Upload semua isi folder (jangan skip file yang diawali titik seperti `.gitignore` dan `.env.example`).

```bash
git init
git add .
git commit -m "Initial commit SoraPay"
git branch -M main
git remote add origin https://github.com/USERNAME/NAMA-REPO.git
git push -u origin main
```

---

## LANGKAH 3 — Deploy ke Vercel + Isi Environment Variables

1. Buka [vercel.com](https://vercel.com) → login/daftar pakai akun GitHub.
2. Klik **Add New > Project** → pilih repo GitHub yang tadi di-push.
3. **Sebelum klik Deploy**, buka bagian **Environment Variables**, lalu isi **6 baris** ini
   (nama di kolom kiri, value dari Langkah 1 nomor 6 di kolom kanan):

   | Name (nama key — ketik PERSIS begini) | Value (diisi dari firebaseConfig Firebase kamu) |
   |---|---|
   | `FIREBASE_API_KEY` | isi dari `apiKey` |
   | `FIREBASE_AUTH_DOMAIN` | isi dari `authDomain` |
   | `FIREBASE_PROJECT_ID` | isi dari `projectId` |
   | `FIREBASE_STORAGE_BUCKET` | isi dari `storageBucket` |
   | `FIREBASE_MESSAGING_SENDER_ID` | isi dari `messagingSenderId` |
   | `FIREBASE_APP_ID` | isi dari `appId` |

4. Klik **Deploy**. Tunggu sampai selesai (~1 menit).
5. Buka link `https://nama-project-kamu.vercel.app` — website sudah live.

> **Kalau nanti ganti/tambah value env var**, harus klik **Redeploy** di dashboard Vercel
> (Settings > Environment Variables > Save, lalu Deployments > ⋯ > Redeploy) supaya perubahan kepakai.

---

## LANGKAH 4 — Buat Akun Admin

1. Buka website yang sudah live, klik ☰ (hamburger) → **🔒 Fitur VIP**.
2. Klik **Daftar Akun Baru**, isi email + password.
3. **Orang PERTAMA yang daftar otomatis jadi admin** — akan langsung masuk ke Admin Panel.
4. Dari Admin Panel, kamu bisa:
   - **Tambah Fitur VIP**: isi nama, deskripsi, upload file `.html` — file itu nanti dijalankan di kotak khusus (iframe sandbox) saat konsumen buka fitur itu, terpisah dari halaman utama demi keamanan.
   - **Buat Akun Konsumen**: isi email + password, langsung jadi akun customer yang bisa login dan lihat semua fitur VIP yang kamu tambahkan (tapi tidak bisa akses Admin Panel).

---

## Soal "Download jadi APK"

File `.apk` asli (installable & sign, siap upload ke Play Store) butuh Android SDK, Gradle, dan proses
signing yang di luar jangkauan tool ini. Sebagai gantinya, project ini sudah disiapkan sebagai
**PWA (Progressive Web App)**:

- Di HP Android (Chrome): buka website → menu titik tiga → **"Add to Home screen" / "Install app"**.
- Di iPhone (Safari): tombol Share → **"Add to Home Screen"**.
- Hasilnya: ikon sendiri di homescreen, buka fullscreen tanpa address bar, terasa seperti app asli.

**Kalau tetap butuh file `.apk` sungguhan** untuk didistribusikan manual:
1. Deploy dulu ke Vercel (Langkah 3).
2. Buka [pwabuilder.com](https://www.pwabuilder.com) → masukkan link Vercel kamu.
3. Klik **Package for Stores > Android** → download APK-nya (gratis).

---

## Soal API Downloader (TikTok, Instagram, Pinterest)

API-API ini **sengaja tidak dimasukkan ke Environment Variables** karena semuanya publik & gratis
tanpa API key (tikwm.com, fastdl.app, savepin.app, widgets.pinterest.com) — tidak ada rahasia yang
perlu disembunyikan, jadi endpoint-nya langsung tertulis di kode `index.html`. Kalau nanti kamu
upgrade ke layanan berbayar (misal API TikTok/Pinterest resmi yang butuh API key), baru itu perlu
ditambahkan sebagai env var baru dengan pola yang sama seperti Firebase di atas.

## Disclaimer

Fitur upload HTML di Admin Panel dijalankan di **iframe sandbox** (`sandbox="allow-scripts allow-forms allow-popups"`)
supaya kode yang diupload tidak bisa mengakses/merusak data di luar kotak itu (localStorage situs utama, dsb).
Meski begitu, tetap hanya upload file HTML dari sumber yang kamu percaya sendiri.
