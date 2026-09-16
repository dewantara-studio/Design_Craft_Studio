# DesignCraft Studio

Aplikasi web belajar desain grafis interaktif (elemen & hirarki, teori warna, tipografi, layout/grid) dilengkapi studio kanvas, kuis pemahaman, sistem login per siswa, dan dashboard admin untuk guru.

File aplikasi: **`designcraft-app.html`** — satu file tunggal, siap di-hosting di GitHub Pages atau hosting statis apa pun.

---

## 1. Kenapa Perlu Setup Firebase?

Karena setiap siswa membuka aplikasi dari **HP/laptop masing-masing**, data (akun, XP, hasil kuis, karya tersimpan) harus disimpan secara online agar:
- Siswa bisa login dari perangkat mana pun dan tetap melihat progresnya sendiri.
- Guru (admin) bisa melihat data **semua siswa** dari satu dashboard.

Firebase dipakai sebagai database gratis untuk ini (tanpa perlu bikin server sendiri).

---

## 2. Langkah Setup Firebase (sekali saja)

1. Buka **[console.firebase.google.com](https://console.firebase.google.com)**, login dengan akun Google, klik **Add project** → beri nama bebas (mis. `designcraft-tompokersan`) → ikuti wizard sampai selesai (boleh matikan Google Analytics).
2. Di menu kiri, buka **Build → Authentication** → klik **Get started** → tab **Sign-in method** → aktifkan **Email/Password**.
3. Di menu kiri, buka **Build → Firestore Database** → klik **Create database** → pilih lokasi server (mis. `asia-southeast2 (Jakarta)`) → mode **Start in production mode**.
4. Setelah database dibuat, buka tab **Rules**, hapus isinya, ganti dengan:

   ```
   rules_version = '2';
   service cloud.firestore {
     match /databases/{database}/documents {
       match /users/{userId} {
         allow read: if request.auth != null;
         allow write: if request.auth != null && request.auth.uid == userId;
       }
     }
   }
   ```
   Klik **Publish**.

5. Kembali ke halaman utama project → klik ikon gerigi (Project settings) → scroll ke bawah ke **Your apps** → klik ikon **`</>`** (Web) → beri nama app → klik **Register app**.
6. Akan muncul kode berisi `firebaseConfig = { apiKey: "...", ... }`. **Salin seluruh isinya.**

---

## 3. Isi Konfigurasi ke File HTML

1. Buka `designcraft-app.html` dengan text editor (Notepad, VS Code, dll).
2. Cari bagian ini (dekat akhir file, di dalam tag `<script>`):

   ```javascript
   const FIREBASE_CONFIG = {
     apiKey: "ISI_API_KEY_ANDA",
     authDomain: "ISI_PROJECT_ID.firebaseapp.com",
     projectId: "ISI_PROJECT_ID",
     storageBucket: "ISI_PROJECT_ID.appspot.com",
     messagingSenderId: "ISI_SENDER_ID",
     appId: "ISI_APP_ID"
   };
   const ADMIN_CODE = "TOMPOKERSAN2026";
   ```

3. Ganti isi `FIREBASE_CONFIG` dengan nilai yang Anda salin dari Firebase (langkah 2.6).
4. Ganti `ADMIN_CODE` dengan kode rahasia pilihan Anda sendiri — kode ini dipakai guru saat mendaftar sebagai admin, jangan dibagikan ke siswa.
5. Simpan file.

Tanpa langkah ini, saat dibuka aplikasi hanya menampilkan layar "Konfigurasi Firebase Belum Diisi".

---

## 4. Upload ke GitHub Pages

1. Buat repository baru di GitHub (atau pakai repo yang sudah ada).
2. Upload `designcraft-app.html`, lalu **rename menjadi `index.html`** agar otomatis jadi halaman utama.
3. Buka **Settings → Pages** di repo tersebut → pilih branch `main` dan folder `/root` → **Save**.
4. Tunggu 1-2 menit, aplikasi bisa diakses di `https://namauser.github.io/nama-repo/`.

---

## 5. Cara Pakai

**Siswa:**
- Buka link aplikasi → tab **Daftar** → isi Nama, Username, Password → klik **Buat Akun**.
- Login kembali kapan saja dengan Username & Password yang sama, dari perangkat mana pun.
- Belajar lewat 4 modul (tiap modul ada simulator interaktif + kuis 3 soal), coba **Tantangan**, berkreasi di **Studio Desain**, dan simpan karya ke **Galeri**.

**Guru/Admin:**
- Tab **Daftar** → isi data → centang **"Daftar sebagai Guru/Admin"** → masukkan `ADMIN_CODE` yang sudah diset di langkah 3 → **Buat Akun**.
- Setelah login, otomatis masuk ke **Dashboard Admin**: ringkasan statistik semua siswa, tabel siswa (XP, modul selesai, % pemahaman, jumlah karya), dan bisa klik tiap siswa untuk lihat detail — termasuk riwayat jawaban kuis (benar/salah per soal) dan galeri karya yang tersimpan.

---

## 6. Catatan Keamanan

- Password disimpan aman oleh Firebase Authentication (bukan di kode/teks biasa).
- Jaga kerahasiaan `ADMIN_CODE` — siapa pun yang tahu kode ini bisa mendaftar sebagai admin.
- Rules Firestore di atas memastikan siswa hanya bisa mengubah datanya sendiri, tapi bisa dibaca oleh akun admin untuk keperluan pemantauan.
