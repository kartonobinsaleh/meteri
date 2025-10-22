# Refactor Handlers ke Controller–Service Pattern

## 🎯 Tujuan
Memisahkan logika `request/response` (controller) dan logika bisnis/database (service), agar kode lebih rapi, mudah dibaca, dan siap dikembangkan.

---

## 📂 1. Struktur Sebelum Refactor

Awalnya, semua logika aplikasi masih berada di dalam satu folder `handlers/`.

```bash
phone_store/
├── node_modules/              # Folder dependensi NPM
├── package.json               # Info & script project
├── package-lock.json          # Lock versi dependensi
└── src/                       # Source utama aplikasi
    ├── config/                # Koneksi & konfigurasi database
    │   └── db.js
    │
    ├── handlers/              # Semua logika masih digabung di sini
    │   ├── productHandler.js
    │   └── usersHandler.js
    │
    ├── routes/                # Routing tiap resource (user, product, dll)
    │   ├── productRoute.js
    │   └── usersRoute.js
    │
    └── server.js              # Entry point Express

```