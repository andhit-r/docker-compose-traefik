# 🚢 Template Copier untuk Traefik Docker Compose

[![Copier](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/copier-org/copier/master/img/badge/badge-grayscale-inverted-border-orange.json)](https://github.com/copier-org/copier)

Template [Copier](https://copier.readthedocs.io/) untuk membuat project Traefik reverse proxy yang sudah siap pakai dengan Docker Compose. Support environment development dan production lengkap dengan SSL/TLS otomatis via Let's Encrypt.

## ✨ Fitur Utama

- 🚀 **Setup Instan** - Project siap dalam hitungan detik
- 🔧 **Sepenuhnya Configurable** - Semua konfigurasi via Copier
- 🔒 **Keamanan Built-in** - Basic auth, SSL/TLS, secure defaults
- 🌍 **Multi-Environment** - Development & production terpisah
- 📝 **Dokumentasi Otomatis** - README sesuai konfigurasi Anda
- 🔄 **Bisa Diupdate** - Sinkronkan dengan template terbaru
- 🎯 **Best Practices** - Mengikuti standar Docker & Traefik

## 📋 Prasyarat

- Docker & Docker Compose
- Python 3.7+ (untuk Copier)
- `htpasswd` atau `openssl` (untuk basic auth)

## 🚀 Cara Menggunakan

### 1. Install Copier

```bash
pip install copier
# atau dengan pipx (recommended)
pipx install copier
```

### 2. Buat Project Baru

**Cara 1: Interactive (Diajukan Pertanyaan)**

```bash
copier copy /path/to/traefik-copier-template ~/project-saya
```

Copier akan menanyakan:
- Nama project
- Domain dashboard (development & production)
- Email untuk Let's Encrypt
- Username & password untuk basic auth
- Dan lainnya...

**Cara 2: Non-Interactive (Pakai File Config)**

```bash
copier copy /path/to/traefik-copier-template ~/project-saya \
  --data-file example-answers.yml
```

**Cara 3: One-Liner**

```bash
copier copy /path/to/traefik-copier-template ~/project-saya \
  --data project_name="traefik-saya" \
  --data traefik_dashboard_host_devel="traefik.localhost" \
  --data traefik_dashboard_host_prod="traefik.domain.com" \
  --data traefik_acme_email="admin@domain.com" \
  --data create_auth_user=true \
  --data auth_username="admin" \
  --data auth_password="password123"
```

### 3. Jalankan Traefik

```bash
cd ~/project-saya

# Untuk Development
docker compose -f docker-compose.common.yaml -f docker-compose.devel.yaml up -d

# Untuk Production
docker compose -f docker-compose.common.yaml -f docker-compose.prod.yaml up -d
```

**🎉 Selesai!** Dashboard bisa diakses di domain yang Anda konfigurasi.

## 📖 Dokumentasi Lengkap

- 📘 [QUICKSTART.md](QUICKSTART.md) - Setup cepat 5 detik!
- 📗 [USAGE.md](USAGE.md) - Panduan lengkap
- 📙 [TEMPLATE.md](TEMPLATE.md) - Dokumentasi untuk developer template

## 🎛️ Konfigurasi Yang Bisa Diatur

| Variable | Deskripsi | Default |
|----------|-----------|---------|
| `project_name` | Nama project Anda | `traefik-proxy` |
| `traefik_dashboard_host_devel` | Domain dashboard di development | `traefik.localhost` |
| `traefik_dashboard_host_prod` | Domain dashboard di production | `traefik.example.com` |
| `traefik_acme_email` | Email untuk Let's Encrypt | `admin@example.com` |
| `traefik_version` | Versi Traefik | `v2.11` |
| `create_auth_user` | Buat user basic auth? | `true` |
| `auth_username` | Username basic auth | `admin` |
| `auth_password` | Password basic auth | (rahasia) |
| `log_level` | Level logging | `INFO` |

## 📁 Struktur Yang Dihasilkan

```
project-anda/
├── .copier-answers.yml          # Konfigurasi Anda
├── .env                         # Environment variables
├── .gitignore                   
├── README.md                    # Dokumentasi project
├── docker-compose.common.yaml   # Config Docker bersama
├── docker-compose.devel.yaml    # Config development
├── docker-compose.prod.yaml     # Config production
├── setup.sh                     # Script setup (sudah dijalankan)
├── data/
│   ├── acme.json               # Sertifikat SSL (otomatis)
│   └── usersfile               # Kredensial basic auth (otomatis)
└── traefik/
    ├── traefik.yml             # Konfigurasi Traefik
    └── dynamic/                # Konfigurasi dinamis
```

## 🔄 Update Project

Jika template diperbaiki/ditingkatkan, Anda bisa update project:

```bash
cd project-anda
copier update
```

Copier akan:
- Menunjukkan perubahan di template
- Menanyakan apakah ingin update
- Merge perubahan dengan aman

## 💡 Contoh Penggunaan

### Setup untuk Multiple Client

```bash
# Client A
copier copy /path/to/template ~/client-a-traefik \
  --data traefik_dashboard_host_prod="traefik.client-a.com"

# Client B
copier copy /path/to/template ~/client-b-traefik \
  --data traefik_dashboard_host_prod="traefik.client-b.com"
```

### Setup dengan File Config Berbeda

**dev-config.yml:**
```yaml
project_name: traefik-development
traefik_dashboard_host_prod: traefik-dev.perusahaan.com
traefik_acme_email: dev@perusahaan.com
log_level: DEBUG
```

**prod-config.yml:**
```yaml
project_name: traefik-production
traefik_dashboard_host_prod: traefik.perusahaan.com
traefik_acme_email: ops@perusahaan.com
log_level: INFO
```

Generate:
```bash
copier copy /path/to/template ~/traefik-dev --data-file dev-config.yml
copier copy /path/to/template ~/traefik-prod --data-file prod-config.yml
```

### Standardisasi di Team

Dengan menggunakan template ini, team Anda bisa:
- ✅ Menggunakan konfigurasi yang konsisten
- ✅ Mengurangi human error
- ✅ Setup lebih cepat
- ✅ Mudah di-maintain dan update

## 🎯 Apa Yang Otomatis Dilakukan?

Ketika Anda menjalankan Copier, template akan:

1. ✅ Generate semua file konfigurasi dengan nilai yang Anda berikan
2. ✅ Membuat direktori `data/` dan `traefik/dynamic/`
3. ✅ Membuat `data/acme.json` dengan permission 600 (untuk SSL)
4. ✅ Generate `data/usersfile` dengan password hash (jika enabled)
5. ✅ Generate README yang sesuai dengan konfigurasi Anda
6. ✅ Generate `.copier-answers.yml` untuk update di masa depan

Semua ini dilakukan otomatis dalam beberapa detik!

## 🔧 Customization Lanjutan

### Menambah Middleware Custom

Setelah generate, Anda bisa tambahkan middleware di `traefik/dynamic/`:

**traefik/dynamic/middlewares.yml:**
```yaml
http:
  middlewares:
    rate-limit:
      rateLimit:
        average: 100
        burst: 50
```

### Menambah Service Lain

Edit `docker-compose.devel.yaml` atau `docker-compose.prod.yaml`:

```yaml
services:
  my-app:
    image: my-app:latest
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.myapp.rule=Host(`app.domain.com`)"
      - "traefik.http.routers.myapp.entrypoints=websecure"
      - "traefik.http.routers.myapp.tls=true"
    networks:
      - traefik-public
```

## 🐛 Troubleshooting

### Problem: Permission denied pada acme.json

**Solution:**
```bash
chmod 600 data/acme.json
```

### Problem: Dashboard tidak bisa diakses (development)

**Solution:** Tambahkan ke `/etc/hosts`:
```
127.0.0.1 traefik.localhost
```

### Problem: htpasswd command not found

**Solution:**
```bash
# Ubuntu/Debian
sudo apt-get install apache2-utils

# Atau gunakan openssl
echo "username:$(openssl passwd -apr1 'password')" > data/usersfile
```

### Problem: SSL certificate tidak muncul

**Solution:**
1. Pastikan domain sudah pointing ke server Anda
2. Pastikan port 80 & 443 terbuka
3. Check logs: `docker compose logs traefik`
4. Pastikan email di `.env` valid

## 📚 Belajar Lebih Lanjut

- [Traefik Documentation](https://doc.traefik.io/traefik/) - Dokumentasi Traefik lengkap
- [Docker Compose Docs](https://docs.docker.com/compose/) - Dokumentasi Docker Compose
- [Copier Docs](https://copier.readthedocs.io/) - Dokumentasi Copier
- [Let's Encrypt](https://letsencrypt.org/) - Tentang Let's Encrypt

## 🤝 Kontribusi

Kontribusi sangat welcome! Beberapa ide yang bisa dikerjakan:

- [ ] Support untuk multiple certificate resolvers
- [ ] Template untuk middleware umum (rate limiting, etc)
- [ ] Integration dengan monitoring (Prometheus/Grafana)
- [ ] Support untuk TCP/UDP routers
- [ ] Dashboard customization options

## 📝 Lisensi

[MIT License](LICENSE)

## 🙏 Credits

- [Traefik](https://traefik.io/) - Reverse proxy yang powerful
- [Copier](https://copier.readthedocs.io/) - Project templating tool
- [Docker](https://www.docker.com/) - Containerization platform
- [Let's Encrypt](https://letsencrypt.org/) - Free SSL certificates

## 💬 Dukungan & Bantuan

- 📖 Baca dokumentasi lengkap di [USAGE.md](USAGE.md)
- 🐛 Laporkan bug via Issues
- 💡 Share ide via Discussions
- ⭐ Star repo ini jika berguna!
- 🔄 Share ke teman-teman developer!

---

Dibuat dengan ❤️ menggunakan [Copier](https://copier.readthedocs.io/)

**Selamat menggunakan! 🎉**
