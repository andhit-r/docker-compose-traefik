# 🚀 Quick Start Guide - Traefik Copier Template

Template ini memungkinkan Anda membuat project Traefik yang sudah dikonfigurasi dalam hitungan detik!

## Installation

```bash
pip install copier
# atau untuk isolated installation
pipx install copier
```

## Cara Tercepat (5 Detik!)

```bash
# Gunakan file answers yang sudah ada
copier copy /path/to/traefik-copier-template /path/to/new-project \
  --data-file /path/to/traefik-copier-template/example-answers.yml
```

**✨ Done!** Project Anda sudah siap dengan:
- ✅ Environment variables configured
- ✅ Docker Compose files ready
- ✅ SSL/TLS setup (acme.json)
- ✅ Basic auth user created
- ✅ All directories created

## Interactive Setup (1 Menit)

Jika ingin customize saat setup:

```bash
copier copy /path/to/traefik-copier-template /path/to/new-project
```

Anda akan ditanya:
1. **Project name** - Nama project (contoh: `my-traefik`)
2. **Development domain** - Domain lokal (contoh: `traefik.localhost`)
3. **Production domain** - Domain production (contoh: `traefik.mycompany.com`)
4. **Email** - Email untuk Let's Encrypt (contoh: `admin@mycompany.com`)
5. **Traefik version** - Versi Traefik (default: `v2.11`)
6. **Create auth user?** - Buat user untuk basic auth? (Y/n)
7. **Username** - Username (default: `admin`)
8. **Password** - Password (akan disembunyikan)
9. **Log level** - INFO/DEBUG/WARN/ERROR (default: `INFO`)

## One-Line Non-Interactive

```bash
copier copy /path/to/traefik-copier-template ~/my-traefik \
  --data project_name="my-traefik" \
  --data traefik_dashboard_host_devel="traefik.localhost" \
  --data traefik_dashboard_host_prod="traefik.mycompany.com" \
  --data traefik_acme_email="admin@mycompany.com" \
  --data traefik_version="v2.11" \
  --data create_auth_user=true \
  --data auth_username="admin" \
  --data auth_password="mypassword123" \
  --data log_level="INFO"
```

## Setelah Setup

### Test di Development

```bash
cd /path/to/new-project
docker compose -f docker-compose.common.yaml -f docker-compose.devel.yaml up -d
```

Akses dashboard: http://traefik.localhost (atau domain yang Anda set)

### Deploy ke Production

```bash
cd /path/to/new-project
docker compose -f docker-compose.common.yaml -f docker-compose.prod.yaml up -d
```

Akses dashboard: https://traefik.yourdomain.com (dengan basic auth)

## Tips & Tricks

### 1. Simpan Answers File untuk Team

Buat file `my-answers.yml`:

```yaml
project_name: company-traefik
traefik_dashboard_host_devel: traefik.localhost
traefik_dashboard_host_prod: traefik.company.com
traefik_acme_email: devops@company.com
traefik_version: v2.11
create_auth_user: true
auth_username: admin
auth_password: SecurePassword123!
log_level: INFO
```

Share dengan team:

```bash
copier copy /path/to/template ~/new-project --data-file my-answers.yml
```

### 2. Update Project Existing

Jika template di-update, update project Anda:

```bash
cd /path/to/your-project
copier update
```

### 3. Gunakan dari Git Repository

Jika template sudah di-push ke git:

```bash
# Via HTTPS
copier copy https://github.com/username/traefik-copier-template.git ~/my-project

# Via GitHub shorthand
copier copy gh:username/traefik-copier-template ~/my-project
```

### 4. Multiple Environments

Buat answers file berbeda untuk setiap environment:

**dev-answers.yml:**
```yaml
project_name: traefik-dev
traefik_dashboard_host_prod: traefik-dev.company.com
# ...
```

**staging-answers.yml:**
```yaml
project_name: traefik-staging
traefik_dashboard_host_prod: traefik-staging.company.com
# ...
```

**prod-answers.yml:**
```yaml
project_name: traefik-prod
traefik_dashboard_host_prod: traefik.company.com
# ...
```

## What Gets Generated?

```
your-project/
├── .copier-answers.yml          # Your configuration
├── .env                         # Environment variables
├── .gitignore                   # Git ignore rules
├── README.md                    # Project documentation
├── docker-compose.common.yaml   # Shared Docker config
├── docker-compose.devel.yaml    # Development config
├── docker-compose.prod.yaml     # Production config
├── setup.sh                     # Setup script (already ran)
├── data/
│   ├── acme.json               # SSL certificates (600 permission)
│   └── usersfile               # Basic auth credentials
└── traefik/
    ├── traefik.yml             # Traefik configuration
    └── dynamic/                # Dynamic configs (empty)
```

## Troubleshooting

### acme.json Permission Denied

```bash
chmod 600 data/acme.json
```

### Dashboard Not Accessible (Development)

Add to `/etc/hosts`:
```
127.0.0.1 traefik.localhost
```

### Password Not Working

Re-generate usersfile:
```bash
htpasswd -cb data/usersfile username newpassword
```

Or using openssl:
```bash
echo "username:$(openssl passwd -apr1 'password')" > data/usersfile
```

### Copier Not Found

```bash
pip install copier
# or
pipx install copier
```

## Advanced Usage

### Dry Run (Preview Without Creating)

```bash
copier copy --pretend /path/to/template /path/to/new-project
```

### Force Overwrite

```bash
copier copy --force /path/to/template /path/to/existing-project
```

### Trust Mode (Skip VCS check)

```bash
copier copy --trust /path/to/template /path/to/new-project
```

## Next Steps

1. ✅ Generated project dengan Copier
2. 📝 Review `.env` file
3. 🔧 Customize `traefik/dynamic/` jika perlu
4. 🚀 Start services dengan Docker Compose
5. 🌐 Configure DNS untuk production domain
6. 🔒 Test SSL certificates dari Let's Encrypt
7. 👤 Test basic auth login
8. 📊 Monitor dashboard

## Resources

- 📖 [Full Documentation](USAGE.md)
- 🏗️ [Template Structure](TEMPLATE.md)
- 🐳 [Docker Compose Docs](https://docs.docker.com/compose/)
- 🔀 [Traefik Documentation](https://doc.traefik.io/traefik/)
- 📋 [Copier Documentation](https://copier.readthedocs.io/)

---

**Happy Traefik-ing! 🎉**
