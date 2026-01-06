# 🚢 Traefik Docker Compose - Copier Template

[![Copier](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/copier-org/copier/master/img/badge/badge-grayscale-inverted-border-orange.json)](https://github.com/copier-org/copier)

Template [Copier](https://copier.readthedocs.io/) untuk membuat project Traefik reverse proxy yang sudah dikonfigurasi dengan Docker Compose. Mendukung environment development dan production dengan SSL/TLS otomatis via Let's Encrypt.

## ✨ Features

- 🚀 **Setup Instan** - Project siap dalam hitungan detik
- 🔧 **Fully Configurable** - Semua environment variables dikonfigurasi via Copier
- 🔒 **Security Built-in** - Basic auth, SSL/TLS, secure defaults
- 🌍 **Multi-Environment** - Konfigurasi terpisah untuk development & production
- 📝 **Auto Documentation** - README yang di-generate sesuai konfigurasi
- 🔄 **Updateable** - Update project saat template di-improve
- 🎯 **Best Practices** - Mengikuti best practices Docker & Traefik

## 📋 Requirements

- Docker & Docker Compose
- Python 3.7+ (untuk Copier)
- `htpasswd` atau `openssl` (untuk basic auth)

## 🚀 Quick Start

### 1. Install Copier

```bash
pip install copier
# atau
pipx install copier
```

### 2. Generate Project

```bash
# Interactive mode
copier copy gh:username/traefik-copier-template ~/my-traefik

# Atau non-interactive dengan file answers
copier copy gh:username/traefik-copier-template ~/my-traefik \
  --data-file example-answers.yml
```

### 3. Start Traefik

```bash
cd ~/my-traefik

# Development
docker compose -f docker-compose.common.yaml -f docker-compose.devel.yaml up -d

# Production
docker compose -f docker-compose.common.yaml -f docker-compose.prod.yaml up -d
```

**🎉 Done!** Dashboard tersedia di domain yang Anda konfigurasi.

## 📖 Documentation

- 📘 [Quick Start Guide](QUICKSTART.md) - Setup dalam 5 detik!
- 📗 [Usage Guide](USAGE.md) - Panduan lengkap penggunaan
- 📙 [Template Structure](TEMPLATE.md) - Dokumentasi untuk template developer

## 🎛️ Configuration Options

Template akan menanyakan:

| Variable | Description | Default |
|----------|-------------|---------|
| `project_name` | Nama project | `traefik-proxy` |
| `traefik_dashboard_host_devel` | Domain dashboard development | `traefik.localhost` |
| `traefik_dashboard_host_prod` | Domain dashboard production | `traefik.example.com` |
| `traefik_acme_email` | Email untuk Let's Encrypt | `admin@example.com` |
| `traefik_version` | Versi Traefik Docker image | `v2.11` |
| `create_auth_user` | Buat basic auth user? | `true` |
| `auth_username` | Username untuk basic auth | `admin` |
| `auth_password` | Password untuk basic auth | (secret) |
| `log_level` | Level logging Traefik | `INFO` |

## 📁 Generated Structure

```
your-project/
├── .copier-answers.yml          # Konfigurasi (untuk update)
├── .env                         # Environment variables
├── .gitignore                   
├── README.md                    # Project documentation
├── docker-compose.common.yaml   # Shared Docker config
├── docker-compose.devel.yaml    # Development config
├── docker-compose.prod.yaml     # Production config
├── setup.sh                     # Setup script
├── data/
│   ├── acme.json               # SSL certificates (auto-generated)
│   └── usersfile               # Basic auth (auto-generated)
└── traefik/
    ├── traefik.yml             # Traefik configuration
    └── dynamic/                # Dynamic configs
```

## 🔄 Update Project

Saat template di-improve, update project Anda:

```bash
cd your-project
copier update
```

## 💡 Use Cases

### Single Server Setup

Perfect untuk:
- Small to medium websites
- Microservices architecture
- Development environments
- Staging servers

### Development Workflow

```bash
# Clone template untuk setiap project/client
copier copy gh:username/traefik-copier-template ~/client-a-traefik
copier copy gh:username/traefik-copier-template ~/client-b-traefik

# Atau gunakan answers file berbeda
copier copy gh:username/traefik-copier-template ~/project \
  --data-file client-a-answers.yml
```

### Team Standardization

```bash
# Share template di organization
copier copy gh:your-org/traefik-copier-template ~/new-project

# Enforce standards via template
# - SSL/TLS wajib di production
# - Basic auth required
# - Log level konsisten
```

## 🛠️ Advanced Usage

### Custom Answers File

Buat `my-config.yml`:

```yaml
project_name: company-traefik
traefik_dashboard_host_devel: traefik.localhost
traefik_dashboard_host_prod: traefik.company.com
traefik_acme_email: devops@company.com
traefik_version: v2.11
create_auth_user: true
auth_username: admin
auth_password: SecurePass123!
log_level: INFO
```

Generate:

```bash
copier copy /path/to/template ~/project --data-file my-config.yml
```

### CI/CD Integration

```yaml
# .github/workflows/deploy.yml
- name: Generate Traefik Config
  run: |
    pip install copier
    copier copy --trust gh:org/traefik-template ./traefik \
      --data-file prod-answers.yml
    
- name: Deploy
  run: |
    cd traefik
    docker compose -f docker-compose.common.yaml \
                   -f docker-compose.prod.yaml up -d
```

## 🤝 Contributing

Contributions welcome! Beberapa ide:

- [ ] Add support untuk Consul/etcd backends
- [ ] Add middleware templates (rate limiting, etc)
- [ ] Add monitoring setup (Prometheus/Grafana)
- [ ] Add multiple certificate resolvers
- [ ] Add custom entrypoints configuration

## 📝 License

[MIT License](LICENSE)

## 🙏 Acknowledgments

- [Traefik](https://traefik.io/) - Amazing reverse proxy
- [Copier](https://copier.readthedocs.io/) - Awesome project templating tool
- [Docker](https://www.docker.com/) - Containerization platform

## 📧 Support

- 📖 Check [USAGE.md](USAGE.md) for detailed docs
- 🐛 Open an issue for bugs
- 💡 Open a discussion for ideas
- ⭐ Star this repo if you find it useful!

---

Made with ❤️ using [Copier](https://copier.readthedocs.io/)
