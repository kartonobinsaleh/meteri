# Menerapkan Error Handling Terstruktur di Express dengan Custom Error dan Middleware

---

## 1. Validasi Data dengan Error Bawaan

```js
if (users.length === 0) {
  throw new Error("User tidak ditemukan");
}
```

**Penjelasan:**
Memastikan data yang dicari ada di database. Jika tidak ditemukan, service akan melempar error standar (`Error`) untuk ditangani di layer berikutnya.

---

## 2. Membuat Custom Error

```js
export class ResponseError extends Error {
  constructor(status, message) {
    super(message);
    this.status = status;
  }
}
```

**Penjelasan:**
Membuat error khusus yang menyimpan **status HTTP** dan **pesan error**. Ini memudahkan middleware untuk mengirim respons JSON yang konsisten.

---

## 3. Menggunakan Custom Error di Service

```js
if (users.length === 0) {
  throw new ResponseError(404, "User tidak ditemukan");
}
```

**Penjelasan:**
Service melempar error dengan kode status spesifik (misal 404) sehingga controller dan middleware dapat mengirim response yang sesuai ke client.

---

## 4. Menambahkan Parameter `next` di Controller

```js
export const getUserByIdHandler = async (req, res, next) => {
  try {
    // Panggil service
    const user = await UserService.getUserById(req.params.id);
    res.status(200).json({
      status: "success",
      data: user,
    });
  } catch (error) {
    next(error);
  }
};
```

**Penjelasan:**
`next` digunakan untuk meneruskan error ke middleware global. Dengan ini, controller tidak perlu menangani response error secara manual setiap kali ada error dari service.

---

## 5. Membuat Middleware Error

```js
export const errorMiddleware = (err, req, res, next) => {
  console.error(err);

  const statusCode = err.status || 500;
  const bodyStatus = statusCode >= 500 ? "error" : "fail";

  res.status(statusCode).json({
    status: bodyStatus,
    message: err.message || "Internal Server Error",
  });
};
```

**Penjelasan:**
Middleware ini menangani semua error yang diteruskan dari controller. Status dan message dikirim secara konsisten ke client dalam format JSON.

---

## 6. Mengaktifkan Middleware Error di `server.js`

```js
import express from "express";
import userRoutes from "./routes/userRoutes.js";
import { errorMiddleware } from "./middlewares/errorMiddleware.js";

const app = express();

app.use(express.json());
app.use("/api/users", userRoutes);

// Pasang middleware error paling terakhir
app.use(errorMiddleware);

export default app;
```

**Penjelasan:**
Middleware error harus dipasang **paling akhir**, setelah semua route. Ini memastikan setiap error yang dilempar dari controller atau service akan ditangani dengan benar.

---

## 📦 Hasil Response ke Client

**Data ditemukan:**

```json
{
  "status": "success",
  "data": {
    "fullname": "Kartono Saleh",
    "username": "kartono",
    "email": "kartono@example.com",
    "role": "admin"
  }
}
```

**User tidak ditemukan:**

```json
{
  "status": "fail",
  "message": "User tidak ditemukan"
}
```

**Error tak terduga (misal DB error):**

```json
{
  "status": "error",
  "message": "Internal Server Error"
}
```

---

> Dengan pola ini, service, controller, dan middleware error saling terpisah, membuat aplikasi lebih mudah dikembangkan, debug, dan dipelihara.
