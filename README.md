# 📝 REST API Manajemen Tugas To-Do

REST API untuk sistem manajemen tugas/to-do dengan fitur autentikasi, otorisasi, dan operasi CRUD lengkap. Dibangun menggunakan Next.js 16, Prisma ORM, dan PostgreSQL.

---

## 📋 Daftar Isi

- [Fitur Utama](#-fitur-utama)
- [Persyaratan Sistem](#-persyaratan-sistem)
- [Instalasi](#-instalasi)
- [Konfigurasi](#️-konfigurasi)
- [Menjalankan Aplikasi](#-menjalankan-aplikasi)
- [Dokumentasi API](#-dokumentasi-api)
- [Pengujian](#-pengujian)
- [Deployment](#-deployment)
- [Struktur Project](#-struktur-project)
- [Troubleshooting](#-troubleshooting)
---

## ✨ Fitur Utama

### Autentikasi & Otorisasi
- ✅ **Registrasi User** dengan email dan password
- ✅ **Sistem Login** dengan JWT tokens
- ✅ **Refresh Token** untuk perpanjangan sesi (dapat dikonfigurasi)
- ✅ **Password Hashing** menggunakan bcrypt (10 salt rounds)
- ✅ **Kontrol Akses Berbasis Peran** (User/Admin)

### Manajemen Tugas
- ✅ **Operasi CRUD** untuk tugas
- ✅ **Filter Tugas** berdasarkan status, prioritas, dan pencarian
- ✅ **Kepemilikan Tugas** - user hanya bisa akses tugas mereka
- ✅ **Hak Akses Admin** - admin bisa akses semua tugas

### Keamanan
- ✅ **Rate Limiting** (dapat dikonfigurasi, default: 100 req/15 menit)
- ✅ **Request Logging** dengan timestamp dan info user
- ✅ **Validasi Input** menggunakan Zod
- ✅ **Proteksi Middleware** untuk autentikasi route
- ✅ **Token Rotation** saat refresh untuk keamanan

### Fitur Tambahan
- ✅ **Penanganan Error** yang konsisten
- ✅ **Migrasi Database** dengan Prisma
- ✅ **Database Seeding** untuk testing
- ✅ **Environment Variables** untuk konfigurasi
- ✅ **Siap untuk Production** deployment

---

## 🛠️ Teknologi yang Digunakan

### Backend Framework
- **Next.js 16.1.1** - Framework React dengan App Router
- **JavaScript (ES Modules)** - Tanpa TypeScript untuk kesederhanaan

### Database
- **PostgreSQL** - Database relasional
- **Prisma ORM 6.2.0** - Database client yang type-safe
- **Prisma Migrate** - Tool migrasi database

### Autentikasi
- **JWT (jsonwebtoken)** - Autentikasi berbasis token
- **bcrypt** - Password hashing

### Validasi & Utilities
- **Zod** - Validasi schema
- **Next.js API Routes** - RESTful API endpoints

---

## 📦 Persyaratan Sistem

Sebelum instalasi, pastikan sudah terinstall:

- **Node.js** >= 18.0.0 ([Download](https://nodejs.org/))
- **npm** atau **yarn** (termasuk dalam Node.js)
- **PostgreSQL** >= 13 ([Download](https://www.postgresql.org/download/))
- **Git** ([Download](https://git-scm.com/))

### Cek Versi

```bash
node --version    # v18.0.0 atau lebih tinggi
npm --version     # v9.0.0 atau lebih tinggi
psql --version    # PostgreSQL 13 atau lebih tinggi
```

---

## 🚀 Instalasi

### 1. Clone Repository

```bash
git clone https://github.com/divaul/UAS-Pemrograman-API
cd UAS-Pemrograman-API
```

### 2. Install Dependencies

```bash
npm install
```

Ini akan menginstall semua dependencies yang diperlukan:
- Next.js 16
- Prisma Client 6.19.0
- bcrypt
- jsonwebtoken
- zod
- dan lainnya

### 3. Buat File Environment

```bash
cp .env.example .env
```

---

## ⚙️ Konfigurasi

### Environment Variables

Edit file `.env` yang sudah dibuat:

```env
# ===========================================
# KONFIGURASI DATABASE
# ===========================================
# Format: postgresql://username:password@host:port/database_name

# PostgreSQL Lokal
DATABASE_URL="postgresql://postgres:password@localhost:5432/todo_db"

# NeonDB (Production)
# DATABASE_URL="postgresql://user:pass@ep-xxx.us-east-2.aws.neon.tech/neondb"

# Supabase (Production)
# DATABASE_URL="postgresql://postgres:[YOUR-PASSWORD]@db.xxx.supabase.co:5432/postgres"


# ===========================================
# KONFIGURASI JWT
# ===========================================
# PENTING: Generate secret yang kuat minimal 32 karakter
# Gunakan: node -e "console.log(require('crypto').randomBytes(32).toString('hex'))"

JWT_SECRET="your-super-secret-jwt-key-change-this-in-production-min-32-chars"
JWT_REFRESH_SECRET="your-refresh-secret-key-change-this-too-min-32-chars"


# ===========================================
# WAKTU KADALUARSA TOKEN JWT
# ===========================================
# Format: Detik: "60", Menit: "15m", Jam: "2h", Hari: "7d"

# Access Token (berumur pendek untuk keamanan)
JWT_ACCESS_EXPIRES_IN="15m"

# Refresh Token (berumur panjang untuk UX)
JWT_REFRESH_EXPIRES_IN="7d"


# ===========================================
# KONFIGURASI RATE LIMITING
# ===========================================
# Proteksi API dari penyalahgunaan dan DDoS

# Maksimal request yang diizinkan dalam window
RATE_LIMIT_MAX_REQUESTS="100"

# Window time dalam milliseconds (default: 15 menit)
RATE_LIMIT_WINDOW_MS="900000"


# ===========================================
# NODE ENVIRONMENT
# ===========================================
NODE_ENV="development"
```

### Generate JWT Secrets

**PENTING**: Jangan gunakan default secrets di production!

```bash
# Generate JWT_SECRET
node -e "console.log(require('crypto').randomBytes(32).toString('hex'))"

# Generate JWT_REFRESH_SECRET (harus berbeda!)
node -e "console.log(require('crypto').randomBytes(32).toString('hex'))"
```

Copy hasil generate ke `.env`.

---

## 🗄️ Pengaturan Database

### 1. Buat Database PostgreSQL

#### Opsi A: PostgreSQL Lokal

```bash
# Login ke PostgreSQL
psql -U postgres

# Buat database
CREATE DATABASE todo_db;

# Keluar dari psql
\q
```

#### Opsi B: NeonDB (Cloud - Direkomendasikan untuk Production)

1. Buat akun di [neon.tech](https://neon.tech)
2. Buat project baru
3. Copy connection string
4. Update `DATABASE_URL` di `.env`

#### Opsi C: Supabase (Alternatif Cloud)

1. Buat akun di [supabase.com](https://supabase.com)
2. Buat project baru
3. Copy connection string dari Settings → Database
4. Update `DATABASE_URL` di `.env`

### 2. Generate Prisma Client

```bash
npx prisma generate
```

### 3. Jalankan Migrasi Database

```bash
# Development
npx prisma migrate dev --name init

```


### 5. Verifikasi Database

```bash
# Buka Prisma Studio (database viewer)
npx prisma studio
```

Browser akan terbuka di `http://localhost:5555`

---

## 🏃 Menjalankan Aplikasi

### Mode Development

```bash
npm run dev
```

Server akan berjalan di: **http://localhost:3000**

### Mode Production

```bash
# Build aplikasi
npm run build

# Start production server
npm start
```

### Script yang Tersedia

```bash
# Development
npm run dev                 # Jalankan development server

# Prisma
npm run prisma:generate    # Generate Prisma Client
npm run prisma:migrate     # Jalankan migrasi
npm run prisma:push        # Push schema ke database

# Build & Production
npm run build              # Build untuk production
npm start                  # Jalankan production server

```

---

## 📚 Dokumentasi API

### Base URL

```
Lokal: http://localhost:3000
Production: https://aplikasi-anda.vercel.app
```

### Format Response

#### Response Sukses
```json
{
  "success": true,
  "message": "Pesan sukses",
  "data": {
    // Data response
  }
}
```

#### Response Error
```json
{
  "success": false,
  "error": "Pesan error",
  "code": 400,
  "details": {
    // Detail error opsional
  }
}
```

---

## 🔐 Endpoint Autentikasi

### 1. Registrasi User

**Endpoint**: `POST /api/auth/register`

**Request:**
```json
{
  "name": "John Doe",
  "email": "john@example.com",
  "password": "password123",
  "role": "User"
}
```

**Response (201):**
```json
{
  "success": true,
  "message": "User berhasil didaftarkan",
  "data": {
    "id": "uuid",
    "name": "John Doe",
    "email": "john@example.com",
    "role": "User",
    "createdAt": "2025-01-02T10:00:00.000Z",
    "updatedAt": "2025-01-02T10:00:00.000Z"
  }
}
```

**Aturan Validasi:**
- `name`: Minimal 2 karakter
- `email`: Format email yang valid
- `password`: Minimal 6 karakter
- `role`: "User" atau "Admin" (opsional, default: "User")

---

### 2. Login

**Endpoint**: `POST /api/auth/login`

**Request:**
```json
{
  "email": "john@example.com",
  "password": "password123"
}
```

**Response (200):**
```json
{
  "success": true,
  "message": "Login berhasil",
  "data": {
    "user": {
      "id": "uuid",
      "name": "John Doe",
      "email": "john@example.com",
      "role": "User"
    },
    "accessToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "refreshToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
  }
}
```

**Rate Limit**: 5 request per menit per IP

---

### 3. Refresh Token

**Endpoint**: `POST /api/auth/refresh`

**Request:**
```json
{
  "refreshToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}
```

**Response (200):**
```json
{
  "success": true,
  "message": "Token berhasil di-refresh",
  "data": {
    "accessToken": "token-akses-baru...",
    "refreshToken": "token-refresh-baru...",
    "user": {
      "id": "uuid",
      "name": "John Doe",
      "email": "john@example.com",
      "role": "User"
    }
  }
}
```

**Catatan:**
- Mengimplementasikan token rotation untuk keamanan
- Refresh token lama menjadi tidak valid setelah digunakan

---

## 📋 Endpoint Tugas

**Autentikasi Diperlukan**: Semua endpoint tugas memerlukan Bearer token

### Headers
```
Authorization: Bearer <access-token>
Content-Type: application/json
```

---

### 1. Dapatkan Semua Tugas

**Endpoint**: `GET /api/tasks`

**Parameter Query:**
- `status` (opsional): PENDING, IN_PROGRESS, COMPLETED, CANCELLED
- `priority` (opsional): LOW, MEDIUM, HIGH, URGENT
- `search` (opsional): Pencarian dalam judul dan deskripsi

**Contoh:**
```
GET /api/tasks?status=PENDING&priority=HIGH&search=next
```

**Response (200):**
```json
{
  "success": true,
  "message": "Tugas berhasil diambil",
  "data": [
    {
      "id": "uuid",
      "title": "Belajar Next.js",
      "description": "Pelajari App Router",
      "status": "PENDING",
      "priority": "HIGH",
      "dueDate": "2025-12-31T23:59:59.000Z",
      "userId": "uuid",
      "user": {
        "id": "uuid",
        "name": "John Doe",
        "email": "john@example.com",
        "role": "User"
      },
      "createdAt": "2025-01-02T10:00:00.000Z",
      "updatedAt": "2025-01-02T10:00:00.000Z"
    }
  ]
}
```

**Otorisasi:**
- User: Hanya lihat tugas milik sendiri
- Admin: Lihat semua tugas

---

### 2. Dapatkan Tugas Berdasarkan ID

**Endpoint**: `GET /api/tasks/:id`

**Response (200):**
```json
{
  "success": true,
  "message": "Tugas berhasil diambil",
  "data": {
    "id": "uuid",
    "title": "Belajar Next.js",
    "description": "Pelajari App Router",
    "status": "PENDING",
    "priority": "HIGH",
    "dueDate": "2025-12-31T23:59:59.000Z",
    "userId": "uuid",
    "user": {...},
    "createdAt": "2025-01-02T10:00:00.000Z",
    "updatedAt": "2025-01-02T10:00:00.000Z"
  }
}
```

**Otorisasi:**
- User: Hanya akses tugas milik sendiri
- Admin: Akses semua tugas

---

### 3. Buat Tugas Baru

**Endpoint**: `POST /api/tasks`

**Request:**
```json
{
  "title": "Belajar Prisma",
  "description": "Pelajari ORM Prisma",
  "status": "PENDING",
  "priority": "MEDIUM",
  "dueDate": "2025-12-31T23:59:59Z"
}
```

**Response (201):**
```json
{
  "success": true,
  "message": "Tugas berhasil dibuat",
  "data": {
    "id": "uuid",
    "title": "Belajar Prisma",
    "description": "Pelajari ORM Prisma",
    "status": "PENDING",
    "priority": "MEDIUM",
    "dueDate": "2025-12-31T23:59:59.000Z",
    "userId": "uuid",
    "user": {...},
    "createdAt": "2025-01-02T10:00:00.000Z",
    "updatedAt": "2025-01-02T10:00:00.000Z"
  }
}
```

**Aturan Validasi:**
- `title`: Wajib, maksimal 200 karakter
- `description`: Opsional, maksimal 1000 karakter
- `status`: Opsional, enum [PENDING, IN_PROGRESS, COMPLETED, CANCELLED]
- `priority`: Opsional, enum [LOW, MEDIUM, HIGH, URGENT]
- `dueDate`: Opsional, format datetime ISO 8601

---

### 4. Update Tugas

**Endpoint**: `PUT /api/tasks/:id`

**Request:**
```json
{
  "status": "COMPLETED",
  "description": "Selesai dipelajari"
}
```

**Response (200):**
```json
{
  "success": true,
  "message": "Tugas berhasil diupdate",
  "data": {
    "id": "uuid",
    "title": "Belajar Prisma",
    "description": "Selesai dipelajari",
    "status": "COMPLETED",
    ...
  }
}
```

**Catatan:**
- Update parsial (hanya kirim field yang ingin diubah)
- Semua field opsional

**Otorisasi:**
- User: Update tugas milik sendiri saja
- Admin: Update semua tugas

---

### 5. Hapus Tugas

**Endpoint**: `DELETE /api/tasks/:id`

**Response (200):**
```json
{
  "success": true,
  "message": "Tugas berhasil dihapus",
  "data": null
}
```

**Otorisasi:**
- User: ❌ Tidak bisa hapus
- Admin: ✅ Bisa hapus semua tugas

---

## 👥 Endpoint Manajemen User

### Dapatkan Semua User

**Endpoint**: `GET /api/users`

**Otorisasi**: **KHUSUS ADMIN**

**Parameter Query:**
- `role` (opsional): User, Admin
- `search` (opsional): Pencarian dalam nama dan email

**Contoh:**
```
GET /api/users?role=User&search=john
```

**Response (200):**
```json
{
  "success": true,
  "message": "User berhasil diambil",
  "data": [
    {
      "id": "uuid",
      "name": "John Doe",
      "email": "john@example.com",
      "role": "User",
      "taskCount": 5,
      "createdAt": "2025-01-02T10:00:00.000Z",
      "updatedAt": "2025-01-02T10:00:00.000Z"
    }
  ]
}
```

---

## 🔒 Matriks Otorisasi

| Endpoint | Publik | User | Admin |
|----------|--------|------|-------|
| `POST /api/auth/register` | ✅ | ✅ | ✅ |
| `POST /api/auth/login` | ✅ | ✅ | ✅ |
| `POST /api/auth/refresh` | ✅ | ✅ | ✅ |
| `GET /api/tasks` | ❌ | ✅ (sendiri) | ✅ (semua) |
| `POST /api/tasks` | ❌ | ✅ | ✅ |
| `GET /api/tasks/:id` | ❌ | ✅ (sendiri) | ✅ (semua) |
| `PUT /api/tasks/:id` | ❌ | ✅ (sendiri) | ✅ (semua) |
| `DELETE /api/tasks/:id` | ❌ | ❌ | ✅ |
| `GET /api/users` | ❌ | ❌ | ✅ |

---

## 🧪 Pengujian

### Pengujian dengan Postman

#### 1. Setup Environment

Buat Postman Environment dengan variabel:
```
baseUrl: http://localhost:3000
accessToken: (akan terisi otomatis)
refreshToken: (akan terisi otomatis)
```

#### 2. Import Collection

Download [Postman Collection](./postman_collection.json) dan import.

#### 3. Alur Pengujian

**A. Alur Autentikasi:**
```
1. Register → POST {{baseUrl}}/api/auth/register
2. Login → POST {{baseUrl}}/api/auth/login
   (Simpan token dari response)
3. Test Endpoint Terproteksi → GET {{baseUrl}}/api/tasks
   (Gunakan Bearer token)
4. Refresh Token → POST {{baseUrl}}/api/auth/refresh
```

**B. Alur Manajemen Tugas:**
```
1. Buat Tugas → POST {{baseUrl}}/api/tasks
2. Dapatkan Semua Tugas → GET {{baseUrl}}/api/tasks
3. Dapatkan Tugas by ID → GET {{baseUrl}}/api/tasks/:id
4. Update Tugas → PUT {{baseUrl}}/api/tasks/:id
5. Coba Hapus (sebagai User) → Harus return 403
6. Login sebagai Admin → POST {{baseUrl}}/api/auth/login
7. Hapus Tugas (sebagai Admin) → Harus berhasil
```

## 📁 Struktur Project

```
todo-task-api/
├── app/
│   ├── api/
│   │   ├── auth/
│   │   │   ├── register/
│   │   │   │   └── route.js          # Endpoint register
│   │   │   ├── login/
│   │   │   │   └── route.js          # Endpoint login
│   │   │   └── refresh/
│   │   │       └── route.js          # Endpoint refresh token
│   │   ├── tasks/
│   │   │   ├── route.js              # GET & POST tugas
│   │   │   └── [id]/
│   │   │       └── route.js          # GET, PUT, DELETE by ID
│   │   └── users/
│   │       └── route.js              # GET users (Khusus Admin)
│   ├── favicon.ico
│   ├── globals.css
│   ├── layout.jsx
│   └── page.jsx
│
├── lib/
│   ├── middleware/
│   │   ├── auth.js                   # Middleware autentikasi
│   │   ├── logger.js                 # Request logging
│   │   └── rateLimit.js              # Rate limiting
│   ├── jwt.js                        # JWT utilities
│   ├── prisma.js                     # Prisma client
│   └── response.js                   # Response formatters
│
├── prisma/
│   ├── schema.prisma                 # Database schema
│   └── seed.js                       # Database seeder
│
├── public/                           # File statis
│
├── .env                              # Environment variables (gitignored)
├── .env.example                      # Template environment
├── .gitignore                        # Aturan Git ignore
├── middleware.js                     # Next.js Edge middleware
├── next.config.js                    # Konfigurasi Next.js
├── package.json                      # Dependencies
└── README.md                         # Dokumentasi
```

---

## 🛡️ Fitur Keamanan

### 1. Keamanan Password
- **bcrypt hashing** dengan 10 salt rounds
- Password TIDAK PERNAH disimpan dalam plain text
- Password TIDAK PERNAH dikembalikan di response API

### 2. Keamanan JWT Token
- **Access Token**: Berumur pendek (15 menit default)
- **Refresh Token**: Berumur panjang (7 hari default)
- **Token Rotation**: Refresh token lama tidak valid setelah digunakan
- **Secret Terpisah**: Access dan refresh menggunakan secret berbeda

### 3. Rate Limiting
- Default: 100 request per 15 menit per IP
- Dapat dikonfigurasi via environment variables
- Limit khusus untuk endpoint sensitif:
  - Login: 5 per menit (mencegah brute force)
  - Register: 10 per 15 menit (mencegah spam)

### 4. Validasi Input
- Semua input divalidasi dengan **Zod**
- Type checking dan sanitasi
- Mencegah SQL injection (via Prisma parameterized queries)
- Mencegah serangan XSS

### 5. Otorisasi
- **Level Route**: Next.js middleware
- **Level Function**: Auth middleware wrapper
- **Berbasis Peran**: Izin Admin vs User
- **Pengecekan Kepemilikan**: User hanya akses data mereka

### 6. HTTPS Only (Production)
- Vercel otomatis menyediakan SSL
- Paksa HTTPS di production

### 7. Konfigurasi CORS
- Origin yang diizinkan dapat dikonfigurasi
- Header CORS yang tepat
- Dukungan credential

---

## ❗ Kode Error

| Kode | Status | Deskripsi |
|------|--------|-------------|
| 200 | OK | Request berhasil |
| 201 | Created | Resource berhasil dibuat |
| 400 | Bad Request | Input tidak valid/error validasi |
| 401 | Unauthorized | Autentikasi diperlukan atau token tidak valid |
| 403 | Forbidden | Izin tidak mencukupi |
| 404 | Not Found | Resource tidak ditemukan |
| 409 | Conflict | Konflik resource (misal: email sudah ada) |
| 422 | Unprocessable Entity | Error validasi |
| 429 | Too Many Requests | Rate limit terlampaui |
| 500 | Internal Server Error | Error server |

---

## 🐛 Troubleshooting

### Masalah: "Cannot find module @prisma/client"

**Solusi:**
```bash
npx prisma generate
rm -rf .next
npm run dev
```

### Masalah: "Invalid or expired token"

**Kemungkinan penyebab:**
1. Token sudah expired (> 15 menit)
2. JWT_SECRET berubah setelah generate token
3. Token tidak lengkap (terpotong saat copy)
4. Server di-restart dengan JWT_SECRET berbeda

**Solusi:**
```bash
# 1. Verifikasi JWT_SECRET di .env tidak berubah
cat .env

# 2. Login ulang untuk mendapatkan token baru
POST /api/auth/login

# 3. Copy token LENGKAP (3 bagian dipisah titik)
# Format: xxxxx.yyyyy.zzzzz

# 4. Test token dengan:
node -e "console.log(require('jsonwebtoken').decode('TOKEN_ANDA'))"
```

### Masalah: "Database connection failed"

**Solusi:**
```bash
# 1. Cek format DATABASE_URL
cat .env

# 2. Test koneksi PostgreSQL
psql $DATABASE_URL

# 3. Verifikasi database exists
psql -U postgres -l

# 4. Test koneksi Prisma
npx prisma studio
```


### Masalah: "Rate limit exceeded"

**Solusi:**
```bash
# Opsi 1: Tunggu window reset (15 menit)

# Opsi 2: Tingkatkan limit di .env
RATE_LIMIT_MAX_REQUESTS="200"

# Opsi 3: Tingkatkan window time
RATE_LIMIT_WINDOW_MS="1800000"  # 30 menit

# Kemudian restart server
```

### Masalah: "Prisma migration failed"

**Solusi:**
```bash
# Reset database (⚠️ akan hapus semua data)
npx prisma migrate reset

# Atau push schema tanpa migrasi
npx prisma db push

# Re-seed jika diperlukan
npm run prisma:seed
```

---


