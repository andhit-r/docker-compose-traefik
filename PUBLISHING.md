# 📦 Publishing & Using This Template

## Sebagai Template Author (Maintainer)

### 1. Push ke Git Repository

```bash
cd /path/to/traefik-copier-template

# Init git jika belum
git init

# Add remote
git remote add origin git@github.com:username/traefik-copier-template.git

# Commit semua template files
git add .
git commit -m "Initial Copier template"

# Push
git push -u origin main
```

### 2. Create Releases (Optional but Recommended)

Copier bisa menggunakan git tags untuk versioning:

```bash
# Tag versi pertama
git tag v1.0.0
git push origin v1.0.0

# Update template...
git add .
git commit -m "Add new features"
git tag v1.1.0
git push origin v1.1.0
```

User bisa specify versi:
```bash
copier copy gh:username/traefik-copier-template ~/project --vcs-ref=v1.0.0
```

### 3. Update Documentation

Pastikan README.md (yang ini) updated dengan:
- URL repository yang benar
- Badge status CI/CD (jika ada)
- Contoh penggunaan dengan URL real

### 4. Test Template

Sebelum publish/update, always test:

```bash
# Test dari local
copier copy --trust . /tmp/test-project

# Test dari git (after push)
copier copy gh:username/traefik-copier-template /tmp/test-project

# Test dengan berbagai config
copier copy . /tmp/test1 --data create_auth_user=true
copier copy . /tmp/test2 --data create_auth_user=false
```

## Sebagai Template User

### Cara 1: Dari GitHub (Recommended)

```bash
# HTTPS
copier copy https://github.com/username/traefik-copier-template.git ~/my-project

# SSH
copier copy git@github.com:username/traefik-copier-template.git ~/my-project

# GitHub Shorthand (paling simple!)
copier copy gh:username/traefik-copier-template ~/my-project
```

### Cara 2: Dari GitLab

```bash
copier copy https://gitlab.com/username/traefik-copier-template.git ~/my-project

# Atau shorthand
copier copy gl:username/traefik-copier-template ~/my-project
```

### Cara 3: Dari Local

```bash
# Clone dulu
git clone https://github.com/username/traefik-copier-template.git

# Lalu copy
copier copy /path/to/traefik-copier-template ~/my-project
```

### Cara 4: Dari Specific Version/Branch

```bash
# Specific version (tag)
copier copy gh:username/traefik-copier-template ~/my-project --vcs-ref=v1.0.0

# Specific branch
copier copy gh:username/traefik-copier-template ~/my-project --vcs-ref=develop

# Specific commit
copier copy gh:username/traefik-copier-template ~/my-project --vcs-ref=abc1234
```

## Update Workflow

### Untuk User: Update Project dari Template

```bash
cd ~/my-project

# Update interaktif
copier update

# Update dengan re-apply answers
copier update --skip-answered

# Update dengan force (overwrite semua)
copier update --force

# Update dari specific version
copier update --vcs-ref=v2.0.0
```

### Untuk Maintainer: Maintain Template

```bash
# Make improvements
vim copier.yml
vim traefik/traefik.yml.jinja

# Test locally
copier copy . /tmp/test

# Commit & tag
git add .
git commit -m "Improve template: add new feature"
git tag v1.1.0
git push origin main --tags

# Announce to users
# Users can now: copier update
```

## Best Practices

### For Template Maintainers

1. **Use Semantic Versioning**
   - `v1.0.0` - Initial release
   - `v1.1.0` - New features
   - `v1.0.1` - Bug fixes
   - `v2.0.0` - Breaking changes

2. **Keep CHANGELOG.md**
   ```markdown
   # Changelog
   
   ## v1.1.0 - 2026-01-06
   - Added support for custom middleware
   - Improved documentation
   
   ## v1.0.0 - 2026-01-01
   - Initial release
   ```

3. **Test Before Release**
   ```bash
   # Test matrix
   copier copy . /tmp/test-dev --data-file tests/dev-config.yml
   copier copy . /tmp/test-prod --data-file tests/prod-config.yml
   ```

4. **Document Breaking Changes**
   - Update major version
   - Write migration guide
   - Notify users

### For Template Users

1. **Commit .copier-answers.yml**
   - Allows easy updates
   - Team collaboration
   - Reproducible setup

2. **Review Updates Before Applying**
   ```bash
   # Preview changes
   copier update --pretend
   
   # Apply if looks good
   copier update
   ```

3. **Pin Template Version for Production**
   ```bash
   # In .copier-answers.yml, specify:
   _src_path: gh:username/traefik-copier-template
   _commit: v1.0.0
   ```

4. **Keep Local Changes Separated**
   - Template changes → Update via copier
   - Local customizations → Edit directly
   - Avoid editing template-generated sections

## CI/CD Integration

### GitHub Actions Example

**.github/workflows/deploy.yml:**
```yaml
name: Deploy Traefik

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v3
      
      - name: Setup Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.11'
      
      - name: Install Copier
        run: pip install copier
      
      - name: Generate Config
        run: |
          copier copy \
            --trust \
            --vcs-ref=v1.0.0 \
            gh:username/traefik-copier-template \
            ./traefik \
            --data-file prod-answers.yml
      
      - name: Deploy to Server
        run: |
          scp -r traefik user@server:/opt/
          ssh user@server "cd /opt/traefik && docker compose up -d"
```

### GitLab CI Example

**.gitlab-ci.yml:**
```yaml
deploy:
  stage: deploy
  image: python:3.11
  before_script:
    - pip install copier
  script:
    - copier copy --trust gl:username/traefik-copier-template ./traefik
        --data-file $CI_ENVIRONMENT_NAME-answers.yml
    - scp -r traefik user@server:/opt/
    - ssh user@server "cd /opt/traefik && docker compose up -d"
  only:
    - main
```

## Template Directory Structure for Git

What to commit:

```
traefik-copier-template/
├── .copier-answers.yml.jinja    ✅ Template
├── .copierignore                ✅ Config
├── .gitignore                   ✅ Config
├── copier.yml                   ✅ Config (required!)
├── example-answers.yml          ✅ Example
├── docker-compose.*.yaml.jinja  ✅ Templates
├── docker-compose.*.yaml        ❌ Generated files
├── .env.jinja                   ✅ Template
├── .env                         ❌ Generated file
├── README-TEMPLATE.md           ✅ Documentation
├── USAGE.md                     ✅ Documentation
├── QUICKSTART.md                ✅ Documentation
├── setup.sh.jinja               ✅ Template
├── traefik/
│   ├── traefik.yml.jinja       ✅ Template
│   └── traefik.yml             ❌ Generated file
├── data/                        ❌ Generated by setup.sh
└── .github/                     ✅ Optional (CI/CD)
```

## Troubleshooting

### Template Not Found

```bash
# Error: Template not found
# Solution: Check URL dan network

# Test connectivity
git ls-remote https://github.com/username/traefik-copier-template.git

# Use full URL instead of shorthand
copier copy https://github.com/username/traefik-copier-template.git ~/project
```

### Permission Denied

```bash
# Error: Permission denied (publickey)
# Solution: Use HTTPS instead of SSH

copier copy https://github.com/username/traefik-copier-template.git ~/project
```

### Dirty Local Warning

```bash
# Warning: Dirty template changes included automatically
# This is OK for testing, but for production use committed versions

# Solution: Commit changes first
git add .
git commit -m "Update template"
git push

# Or use specific commit
copier copy gh:username/traefik-copier-template ~/project --vcs-ref=abc1234
```

## Resources

- [Copier Documentation](https://copier.readthedocs.io/)
- [Copier GitHub](https://github.com/copier-org/copier)
- [Semantic Versioning](https://semver.org/)
- [Keep a Changelog](https://keepachangelog.com/)

## Example: Full Workflow

### As Maintainer

```bash
# 1. Create template
cd traefik-copier-template
# ... create template files ...

# 2. Test locally
copier copy . /tmp/test

# 3. Init git & push
git init
git add .
git commit -m "Initial commit"
git remote add origin git@github.com:username/traefik-copier-template.git
git push -u origin main

# 4. Tag version
git tag v1.0.0
git push origin v1.0.0

# 5. Announce to team
echo "Template ready at: gh:username/traefik-copier-template"
```

### As User

```bash
# 1. Create project from template
copier copy gh:username/traefik-copier-template ~/my-traefik

# 2. Customize if needed
cd ~/my-traefik
vim traefik/dynamic/middlewares.yml

# 3. Commit
git init
git add .
git commit -m "Initial setup"

# 4. Deploy
docker compose -f docker-compose.common.yaml \
               -f docker-compose.prod.yaml up -d

# 5. Later, update from template
copier update
```

---

**Happy Templating! 🎉**
