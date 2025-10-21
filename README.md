# 📰 cf-newsportal: Edge-Native Modular Web System
## 🚀 Gambaran Umum Proyek
cf-newsportal adalah implementasi dari Edge-Native Modular Web System yang menggunakan prinsip Clean Architecture (berbasis Domain, Application, Interface, Infrastructure). Proyek ini dirancang untuk mencapai latensi sangat rendah dan maintainability yang tinggi dengan memanfaatkan stack Cloudflare Edge Computing.
Sistem ini memisahkan:
 * Portal Publik (Reader): Dibangun dengan Astro untuk zero-JS dan SEO optimal di Cloudflare Pages.
 * Admin UI (Editor): Dibangun dengan Vue 3 untuk interaktivitas kompleks.
 * Backend API: Dibangun dengan Hono yang di-deploy sebagai Cloudflare Worker untuk logika bisnis di edge.
💡 Validasi Arsitektur
Tujuan utama proyek ini adalah membuktikan Portabilitas dan Kinerja:
 * Portabilitas: Lapisan Domain dan Application pure TypeScript, terbebas dari binding Cloudflare (D1/KV/R2).
 * Kinerja: Memanfaatkan KV Storage sebagai Read-Through Cache untuk waktu respons sub-10ms.
## 🛠️ Tech Stack & Monorepo Structure
Proyek ini menggunakan pnpm Monorepo Workspaces untuk memisahkan empat lapisan Clean Architecture:
| Lapisan | Package | Teknologi Kunci | Tanggung Jawab |
|---|---|---|---|
| 1. Domain | /packages/domain | TypeScript | Entitas, Use Cases, Repository Interfaces. (Core Business Rules) |
| 2. Application | /packages/application | TypeScript | Logika Bisnis, Cacing (Read-Through/Invalidation KV). |
| 3. Infrastructure | /packages/api-worker | Hono, Wrangler, D1, KV, R2 | Implementasi repository konkret (D1), Routing API, Autentikasi. |
| 4. Interface | /packages/frontend | Astro, Vue 3 | Rendering Portal Publik (Astro) & Admin Dashboard (Vue). |

## ⚙️ Persyaratan Sistem
Pastikan Anda telah menginstal yang berikut sebelum memulai:
 * Node.js (v20 atau lebih tinggi)
 * pnpm (npm install -g pnpm)
 * Wrangler CLI (npm install -g wrangler)
 * Akun Cloudflare dengan D1 Database, KV Namespace, dan R2 Bucket yang sudah di-provision.
## 🚀 Memulai Pengembangan
### 1. Instalasi dan Setup Awal
```
# Clone repositori
git clone https://github.com/username/cf-newsportal.git
cd cf-newsportal

# Instal dependencies di seluruh monorepo menggunakan pnpm
pnpm install

# Buat file .env untuk local development secrets (diabaikan oleh .gitignore)
touch .env 
# Tambahkan token dan ID Cloudflare untuk Wrangler:
# CLOUDFLARE_API_TOKEN="..."
# CLOUDFLARE_ACCOUNT_ID="..."
```

### 2. Pengembangan Lokal
Anda harus menjalankan Worker API dan Frontend Pages secara terpisah:
| Komponen | Perintah | Catatan |
|---|---|---|
| Backend Worker (Hono) | pnpm dev:worker | Wrangler akan me-mock D1, KV, R2 binding secara lokal. |
| Frontend (Astro/Vue) | pnpm dev:frontend | Berjalan di port terpisah. Pastikan fetch API menunjuk ke port lokal Worker. |
### 3. Skrip Utama Monorepo
Gunakan skrip root ini untuk menjalankan operasi global:
| Skrip | Deskripsi |
|---|---|
| pnpm run build:worker | Membangun api-worker (tsup) ke /packages/api-worker/dist. |
| pnpm run build:frontend | Membangun frontend (Astro) ke /packages/frontend/dist. |
| pnpm run lint | Menjalankan Linter di semua packages. |
| pnpm run test | Menjalankan Unit Test (Vitest) di semua packages. |

## ☁️ Deployment (CI/CD)
Proyek ini menggunakan GitHub Actions untuk deployment terpisah dan otomatis.
### A. Konfigurasi Rahasia
Anda wajib mengatur rahasia berikut di GitHub Repository Settings Anda:
 * CLOUDFLARE_API_TOKEN
 * CLOUDFLARE_ACCOUNT_ID
 
### B. Deployment Manual (Jika di Luar CI/CD)

Jika Anda ingin deploy dari mesin lokal/server CI yang berbeda, gunakan skrip root ini setelah build berhasil:
```
# Pastikan CLOUDFLARE_API_TOKEN dan CLOUDFLARE_ACCOUNT_ID diekspor ke environment
pnpm run deploy:worker      # Deploy Worker ke Cloudflare Workers
pnpm run deploy:frontend    # Deploy Pages ke Cloudflare Pages
```

## 🧪 Testing dan Validasi
Kami menggunakan Vitest untuk pengujian.
 * Unit Tests (Portability Proof): Pengujian pada domain dan application tidak boleh mengimpor Cloudflare types. Repositori D1 harus di-mock menggunakan interface IArticleRepository.
 * Integration Tests: Pengujian pada api-worker menggunakan mock HTTP request untuk memvalidasi routing Hono dan binding D1/KV yang benar.

## 🤝 Kontribusi
Selamat datang! Silakan ikuti standar kode TypeScript yang ketat dan pastikan semua unit test dan linting lulus sebelum melakukan pull request.
```
pnpm run lint
pnpm run test
```

Dibangun untuk membuktikan Clean Architecture dan Kinerja Edge.