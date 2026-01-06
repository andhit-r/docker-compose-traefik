# Template Structure Documentation

## File Template (*.jinja)

File-file dengan ekstensi `.jinja` adalah template Jinja2 yang akan di-render oleh Copier:

### Core Configuration Templates

1. **`.env.jinja`**
   - Template untuk environment variables
   - Variabel: `traefik_dashboard_host_devel`, `traefik_dashboard_host_prod`, `traefik_acme_email`
   - Output: `.env`

2. **`docker-compose.common.yaml.jinja`**
   - Template untuk konfigurasi Docker Compose yang shared
   - Variabel: `traefik_version`
   - Output: `docker-compose.common.yaml`

3. **`traefik/traefik.yml.jinja`**
   - Template untuk konfigurasi utama Traefik
   - Variabel: `log_level`, `traefik_acme_email`
   - Output: `traefik/traefik.yml`

4. **`README.md.jinja`**
   - Template untuk dokumentasi project
   - Menggunakan conditional Jinja2 untuk menampilkan instruksi yang berbeda tergantung `create_auth_user`
   - Output: `README.md`

### Setup & Configuration

5. **`setup.sh.jinja`**
   - Post-generation script yang dijalankan otomatis setelah copy
   - Membuat direktori `data/` dan `traefik/dynamic/`
   - Membuat `data/acme.json` dengan permission 600
   - Membuat `data/usersfile` jika `create_auth_user=true`
   - Output: `setup.sh`

6. **`.copier-answers.yml.jinja`**
   - Template untuk menyimpan jawaban user
   - Digunakan untuk `copier update` di masa depan
   - Output: `.copier-answers.yml`

## File Static (Tidak di-template)

File-file berikut tidak menggunakan template dan akan di-copy as-is:

- `docker-compose.devel.yaml` - Konfigurasi untuk development environment
- `docker-compose.prod.yaml` - Konfigurasi untuk production environment
- `.gitignore` - Git ignore rules

## Copier Configuration

### copier.yml

File utama untuk konfigurasi Copier template:

```yaml
_min_copier_version: "9.0.0"      # Minimum versi Copier yang diperlukan
_subdirectory: .                   # Root directory template
_templates_suffix: .jinja          # Suffix untuk file template

_exclude:                          # File yang tidak akan di-copy
  - copier.yml
  - .git
  - .github
  - "*.pyc"
  - __pycache__
  - .DS_Store
  - .copier-answers.yml

# Questions for users
project_name: ...
traefik_dashboard_host_devel: ...
# ... (see copier.yml for full list)

_tasks:                            # Post-generation tasks
  - "chmod +x setup.sh"
  - "./setup.sh"
```

## Variabel Template

### String Variables

- `project_name`: Nama project (digunakan di README title)
- `traefik_dashboard_host_devel`: Domain dashboard development
- `traefik_dashboard_host_prod`: Domain dashboard production
- `traefik_acme_email`: Email untuk Let's Encrypt ACME
- `traefik_version`: Versi Docker image Traefik
- `auth_username`: Username untuk basic auth
- `auth_password`: Password untuk basic auth (secret)
- `log_level`: Level logging Traefik (DEBUG/INFO/WARN/ERROR)

### Boolean Variables

- `create_auth_user`: Flag untuk membuat basic auth user

### Conditional Logic

Template menggunakan Jinja2 conditionals:

```jinja
{% if create_auth_user %}
# Generate usersfile
{% else %}
# Skip usersfile generation
{% endif %}
```

## Generated Files Structure

Setelah menjalankan `copier copy`, struktur project akan seperti ini:

```
project-name/
├── .copier-answers.yml          # Jawaban user (auto-generated)
├── .env                         # From .env.jinja
├── .gitignore                   # Copied as-is
├── README.md                    # From README.md.jinja
├── setup.sh                     # From setup.sh.jinja (executed)
├── docker-compose.common.yaml   # From docker-compose.common.yaml.jinja
├── docker-compose.devel.yaml    # Copied as-is
├── docker-compose.prod.yaml     # Copied as-is
├── data/                        # Created by setup.sh
│   ├── acme.json               # Created by setup.sh
│   └── usersfile               # Created by setup.sh (if create_auth_user)
└── traefik/
    ├── traefik.yml             # From traefik/traefik.yml.jinja
    └── dynamic/                # Created by setup.sh
```

## Update Process

Ketika user menjalankan `copier update`:

1. Copier membaca `.copier-answers.yml`
2. Menanyakan pertanyaan dengan default dari answers file
3. Re-render semua template dengan jawaban baru
4. Merge changes dengan file yang sudah ada (smart merging)
5. Menjalankan `_tasks` lagi (setup.sh)

## Best Practices

### Untuk Template Developer

1. **Jangan hardcode values** - Gunakan variabel Jinja2
2. **Provide sensible defaults** - Mudahkan user dengan default yang baik
3. **Use validators** - Validasi input user (contoh: email harus valid)
4. **Document variables** - Berikan `help` text yang jelas
5. **Test template** - Test dengan berbagai kombinasi input

### Untuk Template User

1. **Commit .copier-answers.yml** - Untuk tracking dan team collaboration
2. **Review generated files** - Jangan blindly trust template
3. **Keep template updated** - Run `copier update` untuk mendapat fixes
4. **Customize freely** - Template adalah starting point, bukan constraint

## Customization Examples

### Menambah Variable Baru

Di `copier.yml`:

```yaml
custom_network_name:
  type: str
  help: "Custom Docker network name"
  default: "traefik-public"
```

Di template (e.g., `docker-compose.common.yaml.jinja`):

```yaml
networks:
  traefik-public:
    name: {{ custom_network_name }}
```

### Menambah Conditional Feature

Di `copier.yml`:

```yaml
enable_metrics:
  type: bool
  help: "Enable Prometheus metrics?"
  default: false

metrics_port:
  type: int
  help: "Port for Prometheus metrics"
  default: 8082
  when: "{{ enable_metrics }}"
```

Di `traefik/traefik.yml.jinja`:

```yaml
{% if enable_metrics %}
metrics:
  prometheus:
    entryPoint: metrics
{% endif %}
```

## Troubleshooting

### Template Syntax Error

Jika ada error saat rendering:
1. Check Jinja2 syntax di file `.jinja`
2. Pastikan semua variabel sudah didefinisikan di `copier.yml`
3. Test dengan `copier copy --trust`

### Post-generation Task Failed

Jika `setup.sh` gagal:
1. Check permission: `chmod +x setup.sh`
2. Check bash syntax
3. Run manually: `./setup.sh`
4. Check logs di terminal output

### Update Conflicts

Jika ada conflict saat `copier update`:
1. Copier akan menanyakan conflict resolution
2. Atau gunakan `--skip` untuk skip conflicts
3. Atau `--force` untuk overwrite

## Resources

- [Copier Documentation](https://copier.readthedocs.io/)
- [Jinja2 Template Documentation](https://jinja.palletsprojects.com/)
- [YAML Syntax](https://yaml.org/)
