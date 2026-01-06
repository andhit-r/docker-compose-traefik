# Cara Menggunakan Template Traefik Copier

Template ini memungkinkan Anda untuk membuat project Traefik yang sudah dikonfigurasi dengan mudah.

## Instalasi Copier

```bash
pip install copier
# atau
pipx install copier
```

## Membuat Project Baru

### Dari Local Path

Jika template ada di komputer Anda:

```bash
copier copy /path/to/traefik-copier-template /path/to/project-baru
```

### Dari Git Repository

Jika template sudah di-push ke git:

```bash
# Via HTTPS
copier copy https://github.com/username/traefik-copier-template.git /path/to/project-baru

# Via SSH
copier copy git@github.com:username/traefik-copier-template.git /path/to/project-baru

# Shorthand GitHub
copier copy gh:username/traefik-copier-template /path/to/project-baru
```

## Proses Setup

Copier akan menanyakan beberapa pertanyaan:

1. **project_name**: Nama project Anda (default: traefik-proxy)
2. **traefik_dashboard_host_devel**: Domain untuk dashboard development (default: traefik.localhost)
3. **traefik_dashboard_host_prod**: Domain untuk dashboard production (default: traefik.example.com)
4. **traefik_acme_email**: Email untuk Let's Encrypt (wajib valid)
5. **traefik_version**: Versi Traefik (default: v2.11)
6. **create_auth_user**: Buat basic auth user? (default: true)
7. **auth_username**: Username untuk basic auth (jika create_auth_user=true)
8. **auth_password**: Password untuk basic auth (jika create_auth_user=true)
9. **log_level**: Level logging (DEBUG/INFO/WARN/ERROR, default: INFO)

## Contoh Session

```bash
$ copier copy gh:username/traefik-copier-template ~/my-traefik

🎤 Nama project Traefik Anda
   traefik-proxy
   my-production-traefik

🎤 Domain/hostname untuk dashboard Traefik di environment development
   traefik.localhost
   traefik.dev.local

🎤 Domain untuk dashboard Traefik di environment production
   traefik.example.com
   traefik.mycompany.com

🎤 Email untuk registrasi Let's Encrypt (ACME)
   admin@example.com
   devops@mycompany.com

🎤 Versi Traefik yang akan digunakan
   v2.11
   v2.11

🎤 Apakah Anda ingin membuat basic auth user untuk production?
   (Y/n) Y

🎤 Username untuk basic auth (production)
   admin
   admin

🎤 Password untuk basic auth (production)
   ********

🎤 Level logging untuk Traefik
   INFO
   INFO

🚀 Setting up Traefik project...
✅ Created data/acme.json with correct permissions
✅ Created basic auth user: admin
✅ Created traefik/dynamic directory

✨ Setup complete! Your Traefik project is ready.
```

## Setelah Setup

Project baru Anda akan memiliki struktur:

```
my-traefik/
├── .copier-answers.yml      # Jawaban Anda (untuk update nanti)
├── .env                      # Environment variables
├── .gitignore
├── README.md
├── setup.sh                  # Script setup (sudah dijalankan)
├── docker-compose.common.yaml
├── docker-compose.devel.yaml
├── docker-compose.prod.yaml
├── data/
│   ├── acme.json            # Untuk SSL certificates
│   └── usersfile            # Basic auth credentials
└── traefik/
    ├── traefik.yml          # Konfigurasi Traefik
    └── dynamic/             # Dynamic configuration
```

## Menjalankan Traefik

### Development

```bash
cd my-traefik
docker compose -f docker-compose.common.yaml -f docker-compose.devel.yaml up -d
```

Akses dashboard di: http://traefik.localhost (atau domain yang Anda set)

### Production

```bash
cd my-traefik
docker compose -f docker-compose.common.yaml -f docker-compose.prod.yaml up -d
```

Akses dashboard di: https://traefik.mycompany.com (atau domain yang Anda set)

## Update Konfigurasi

Jika ingin mengubah konfigurasi yang sudah ada:

### Update Interaktif

```bash
cd my-traefik
copier update
```

Copier akan menanyakan pertanyaan lagi dengan nilai default dari `.copier-answers.yml`.

### Update dengan File Answers

Edit `.copier-answers.yml` secara manual, lalu:

```bash
copier update --force
```

### Update dari Template yang Lebih Baru

Jika template di repository asli di-update:

```bash
copier update --trust
```

## Tips

### Gunakan Non-Interactive Mode

Jika ingin automate setup:

```bash
copier copy gh:username/traefik-copier-template ~/my-traefik \
  --data project_name="my-traefik" \
  --data traefik_dashboard_host_devel="traefik.localhost" \
  --data traefik_dashboard_host_prod="traefik.mycompany.com" \
  --data traefik_acme_email="devops@mycompany.com" \
  --data traefik_version="v2.11" \
  --data create_auth_user=true \
  --data auth_username="admin" \
  --data auth_password="supersecret123" \
  --data log_level="INFO"
```

### Simpan Answers File

Anda bisa membuat file `answers.yml` sendiri:

```yaml
project_name: my-traefik
traefik_dashboard_host_devel: traefik.localhost
traefik_dashboard_host_prod: traefik.mycompany.com
traefik_acme_email: devops@mycompany.com
traefik_version: v2.11
create_auth_user: true
auth_username: admin
auth_password: supersecret123
log_level: INFO
```

Lalu gunakan:

```bash
copier copy gh:username/traefik-copier-template ~/my-traefik --data-file answers.yml
```

### Version Control

File-file yang harus di-commit ke git:
- `.copier-answers.yml` (untuk tracking configuration)
- `.env` (atau gunakan `.env.example` dan ignore `.env`)
- Semua file konfigurasi lainnya

File-file yang **JANGAN** di-commit:
- `data/acme.json`
- `data/usersfile` (berisi password hash)
- `.env` (jika berisi secrets)

## Troubleshooting

### Permission Error pada acme.json

```bash
chmod 600 data/acme.json
```

### htpasswd Command Not Found

```bash
# Ubuntu/Debian
sudo apt-get install apache2-utils

# macOS
# htpasswd sudah terinstall secara default

# Alternative: gunakan openssl
echo "username:$(openssl passwd -apr1 'password')" > data/usersfile
```

### Dashboard Tidak Bisa Diakses

1. Pastikan domain di `.env` sudah benar
2. Untuk development, tambahkan ke `/etc/hosts`:
   ```
   127.0.0.1 traefik.localhost
   ```
3. Periksa logs: `docker compose logs traefik`

## Referensi

- [Copier Documentation](https://copier.readthedocs.io/)
- [Traefik Documentation](https://doc.traefik.io/traefik/)
- [Docker Compose Documentation](https://docs.docker.com/compose/)
