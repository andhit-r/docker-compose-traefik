# Traefik Docker Compose Setup

Proyek ini berisi konfigurasi Docker Compose untuk menjalankan Traefik sebagai *reverse proxy*. Pengaturan ini dipisahkan untuk lingkungan pengembangan (development) dan produksi (production) guna mengoptimalkan alur kerja dan keamanan.

## Prasyarat

- Docker
- Docker Compose

## Struktur Proyek

- `docker-compose.common.yaml`: Berisi konfigurasi dasar yang digunakan bersama oleh lingkungan development dan production.
- `docker-compose.devel.yaml`: Konfigurasi khusus untuk lingkungan **development**. Mengaktifkan dashboard Traefik yang tidak aman (`insecure`) untuk kemudahan akses dan debugging.
- `docker-compose.prod.yaml`: Konfigurasi khusus untuk lingkungan **produksi**. Mencakup pengaturan keamanan, HTTPS otomatis melalui Let's Encrypt, dan otentikasi untuk dashboard.
- `traefik/traefik.yml`: File konfigurasi statis utama untuk Traefik, terutama digunakan di lingkungan produksi.
- `traefik/dynamic/`: Direktori untuk file konfigurasi dinamis Traefik (misalnya, middleware, router, dll).
- `.env`: Variabel lingkungan bersama; memuat host dashboard untuk environment development dan production serta email ACME Let's Encrypt.
- `acme.json`: File untuk menyimpan sertifikat SSL/TLS dari Let's Encrypt. **Penting**: Pastikan hak akses file ini adalah `600`.
- `usersfile`: File untuk menyimpan kredensial otentikasi dasar (digunakan di produksi).

## Konfigurasi `.env`

Seluruh environment share satu file `.env`. Pastikan nilai berikut disesuaikan sebelum menjalankan Docker Compose:

- `TRAEFIK_DASHBOARD_HOST_DEVEL`: Domain atau hostname yang Anda gunakan untuk mengakses dashboard Traefik di lingkungan development (mis. `traefik.localhost`).
- `TRAEFIK_DASHBOARD_HOST_PROD`: Domain dashboard Traefik di production (mis. `traefik.toyscation.id`). Nilai ini digunakan oleh router di `docker-compose.prod.yaml`.
- `TRAEFIK_ACME_EMAIL`: Alamat email yang didaftarkan ke Let's Encrypt untuk sertifikat otomatis. Traefik membacanya melalui variabel lingkungan di `docker-compose.common.yaml`.

## Cara Menjalankan

### Lingkungan Development

Pengaturan ini dioptimalkan untuk pengujian lokal. Dashboard Traefik dapat diakses tanpa otentikasi.

1.  **Jalankan services:**
    ```bash
    sudo docker compose -f docker-compose.common.yaml -f docker-compose.devel.yaml up -d
    ```

2.  **Akses Dashboard:**
    Dashboard Traefik tersedia di domain `TRAEFIK_DASHBOARD_HOST_DEVEL` dari `.env` (contoh: [http://traefik.localhost](http://traefik.localhost)).

### Lingkungan Produksi

Pengaturan ini dioptimalkan untuk production dengan fokus pada keamanan.

**Sebelum memulai:**

1.  **Konfigurasi Domain:** Edit `.env` dan sesuaikan `TRAEFIK_DASHBOARD_HOST_PROD` dengan domain dashboard produksi Anda (juga `TRAEFIK_DASHBOARD_HOST_DEVEL` bila perlu).
2.  **Konfigurasi Email:** Edit `.env` dan sesuaikan `TRAEFIK_ACME_EMAIL` agar sertifikat Let's Encrypt terdaftar menggunakan alamat Anda.
3.  **Buat Direktori `data` dan File Rahasia:** Buat direktori `data` untuk menyimpan file-file yang tidak boleh masuk ke Git.
    ```bash
    mkdir -p data
    ```
4.  **Buat `acme.json`:** Buat file untuk sertifikat Let's Encrypt di dalam `data` dan amankan hak aksesnya.
    ```bash
    touch data/acme.json
    chmod 600 data/acme.json
    ```
5.  **Buat Kredensial Pengguna:** Buat file `usersfile` yang berisi kredensial pengguna di dalam direktori `data`. Ganti `your_user` dan `your_password` dengan nilai yang Anda inginkan.
    ```bash
    # Install apache2-utils jika belum ada (untuk htpasswd)
    # sudo apt-get update && sudo apt-get install apache2-utils
    htpasswd -cb data/usersfile your_user your_password
    ```
    Pastikan path untuk `usersfile` dan `acme.json` di `docker-compose.prod.yaml` sudah benar menunjuk ke direktori `data`.

**Menjalankan services:**

1.  Gunakan perintah berikut untuk memulai container:
    ```bash
    sudo docker compose -f docker-compose.common.yaml -f docker-compose.prod.yaml up -d
    ```

2.  **Akses Dashboard:**
    Dashboard Traefik akan tersedia di `https://traefik.yourdomain.com` (sesuaikan dengan domain Anda). Anda akan diminta memasukkan username dan password yang telah dibuat.

### Menghentikan Services

Untuk menghentikan services di lingkungan mana pun, gunakan perintah `down` yang sesuai.

- **Development:**
  ```bash
  sudo docker compose -f docker-compose.common.yaml -f docker-compose.devel.yaml down
  ```

- **Produksi:**
  ```bash
  sudo docker compose -f docker-compose.common.yaml -f docker-compose.prod.yaml down
  ```
