# Traefik Docker Compose Template

A production-ready [Copier](https://github.com/copier-org/copier) template for quickly setting up Traefik reverse proxy with Docker Compose.

## 🎯 Features

- **⚡ Quick Setup**: Generate a complete Traefik project in seconds
- **🔐 SSL/TLS Ready**: Automatic HTTPS certificates with Let's Encrypt
- **🛡️ Security Built-in**: Optional basic authentication with htpasswd
- **🌍 Multi-Environment**: Separate configurations for development and production
- **📝 Customizable**: Interactive prompts or pre-configured answers file
- **🐳 Docker Native**: Docker Compose configurations ready to deploy
- **📊 Dashboard Access**: Traefik dashboard with configurable domains
- **🔄 Updatable**: Use `copier update` to sync with template changes

## 📋 Prerequisites

- Python 3.7+ (for Copier)
- Docker and Docker Compose
- Basic understanding of Traefik and Docker

## 🚀 Quick Start

### Install Copier

```bash
pip install copier
# or for isolated installation
pipx install copier
```

### Create New Project

#### Interactive Mode (Recommended for First Time)

```bash
copier copy gh:YOUR_USERNAME/traefik-template ./my-traefik-project
```

You'll be prompted to answer:
- Project name
- Development and production dashboard domains
- Email for Let's Encrypt
- Traefik version
- Basic auth configuration (optional)
- Log level

#### Non-Interactive Mode (Using Answers File)

```bash
copier copy gh:YOUR_USERNAME/traefik-template ./my-traefik-project \
  --data-file ./example-answers.yml
```

#### From Local Path

```bash
copier copy /path/to/traefik-template ./my-traefik-project
```

### What Gets Generated

After running Copier, you'll get a complete project with:

```
my-traefik-project/
├── docker-compose.common.yaml   # Shared Traefik service configuration
├── docker-compose.devel.yaml    # Development environment setup
├── docker-compose.prod.yaml     # Production environment setup
├── .env                         # Environment variables
├── .gitignore                   # Git ignore rules
├── README.md                    # Project-specific documentation
├── setup.sh                     # Setup script (auto-executed)
├── data/
│   ├── acme.json               # Let's Encrypt certificates (auto-created)
│   └── usersfile               # Basic auth credentials (if enabled)
└── traefik/
    ├── traefik.yml             # Main Traefik configuration
    └── dynamic/                # Dynamic configuration directory
```

## 🎨 Template Configuration

### Questions Asked During Setup

| Variable | Description | Default | Required |
|----------|-------------|---------|----------|
| `project_name` | Name of your Traefik project | `traefik-proxy` | Yes |
| `traefik_dashboard_host_devel` | Development dashboard domain | `traefik.localhost` | Yes |
| `traefik_dashboard_host_prod` | Production dashboard domain | `traefik.example.com` | Yes |
| `traefik_acme_email` | Email for Let's Encrypt | `admin@example.com` | Yes |
| `traefik_version` | Traefik Docker image version | `v2.11` | Yes |
| `create_auth_user` | Create basic auth user? | `true` | Yes |
| `auth_username` | Basic auth username | `admin` | If auth enabled |
| `auth_password` | Basic auth password | - | If auth enabled |
| `log_level` | Logging level | `INFO` | Yes |

### Using Custom Answers File

Create a YAML file with your answers:

```yaml
# my-answers.yml
project_name: company-traefik
traefik_dashboard_host_devel: traefik.local.dev
traefik_dashboard_host_prod: traefik.company.com
traefik_acme_email: devops@company.com
traefik_version: v2.11
create_auth_user: true
auth_username: admin
auth_password: secure-password-here
log_level: INFO
```

Then use it:

```bash
copier copy gh:YOUR_USERNAME/traefik-template ./project \
  --data-file my-answers.yml
```

## 🔄 Updating Existing Projects

When the template is updated, you can sync your project:

```bash
cd my-traefik-project
copier update
```

Copier will:
1. Show you what has changed in the template
2. Ask if you want to update each changed file
3. Preserve your customizations where possible

## 📁 Template Structure

### Template Files (`.jinja`)

These files are processed by Jinja2 templating engine:

- `docker-compose.common.yaml.jinja` → Docker Compose base configuration
- `traefik/traefik.yml.jinja` → Main Traefik configuration
- `README.md.jinja` → Generated project README
- `setup.sh.jinja` → Post-generation setup script
- `.env.jinja` → Environment variables file

### Static Files

These files are copied as-is:

- `docker-compose.devel.yaml` → Development environment
- `docker-compose.prod.yaml` → Production environment
- `.gitignore` → Git ignore rules

### Template Configuration

- `copier.yml` → Template metadata and questions
- `example-answers.yml` → Example answers file for reference

### Documentation

- `README.md` → This file (template documentation)
- `QUICKSTART.md` → Quick start guide
- `USAGE.md` → Detailed usage instructions
- `TEMPLATE.md` → Template structure documentation
- `PUBLISHING.md` → Publishing guidelines
- `SETUP-SUMMARY.md` → Setup summary reference

## 🛠️ Development

### Testing the Template Locally

```bash
# Test generating a project
copier copy . /tmp/test-project

# Check the generated files
cd /tmp/test-project
ls -la
```

### Modifying the Template

1. Edit template files (`.jinja` files) or configuration (`copier.yml`)
2. Test locally using the command above
3. Commit and push changes
4. Users can update their projects with `copier update`

### Adding New Questions

Edit `copier.yml` and add your question:

```yaml
new_variable:
  type: str
  help: "Description of what this does"
  default: "default-value"
  validator: "{% if not new_variable %}Required{% endif %}"
```

Then use it in your templates: `{{ new_variable }}`

## 📚 Usage Examples

### Example 1: Quick Local Development

```bash
# Create project
copier copy . ./my-dev-traefik --data project_name=dev-traefik

# Start development environment
cd my-dev-traefik
docker-compose -f docker-compose.common.yaml -f docker-compose.devel.yaml up -d

# Access dashboard at http://traefik.localhost (or your configured domain)
```

### Example 2: Production Deployment

```bash
# Create project with production answers
copier copy . ./prod-traefik --data-file prod-answers.yml

# Deploy to production
cd prod-traefik
docker-compose -f docker-compose.common.yaml -f docker-compose.prod.yaml up -d
```

### Example 3: Multiple Environments

```bash
# Create separate projects for each environment
copier copy . ./traefik-staging --data-file staging-answers.yml
copier copy . ./traefik-production --data-file production-answers.yml
```

## 🔒 Security Considerations

- **Passwords**: Never commit `.env` files or answers files with real passwords to version control
- **ACME Certificates**: The `data/acme.json` file contains private keys - keep it secure
- **Basic Auth**: Use strong passwords for production environments
- **Dashboard Access**: Always enable authentication for production dashboards
- **Domains**: Use proper DNS configuration for production domains

## 🤝 Contributing

Contributions are welcome! Please:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Make your changes
4. Test the template generation
5. Commit your changes (`git commit -m 'Add amazing feature'`)
6. Push to the branch (`git push origin feature/amazing-feature`)
7. Open a Pull Request

## 📝 License

[Specify your license here]

## 🆘 Troubleshooting

### Issue: `copier: command not found`

**Solution**: Install Copier with `pip install copier` or `pipx install copier`

### Issue: Permission denied on `acme.json`

**Solution**: The setup script automatically sets correct permissions. If needed, run:
```bash
chmod 600 data/acme.json
```

### Issue: Dashboard not accessible

**Solution**: 
- Check if domain resolves correctly
- For development, add to `/etc/hosts`: `127.0.0.1 traefik.localhost`
- Verify Docker containers are running: `docker ps`

### Issue: Let's Encrypt certificate errors

**Solution**:
- Verify email address is correct
- Ensure domain points to your server's public IP
- Check Traefik logs: `docker logs <traefik-container>`

## 📖 Additional Resources

- [Traefik Documentation](https://doc.traefik.io/traefik/)
- [Copier Documentation](https://copier.readthedocs.io/)
- [Docker Compose Documentation](https://docs.docker.com/compose/)
- [Let's Encrypt Documentation](https://letsencrypt.org/docs/)

## ⭐ Support

If you find this template helpful, please consider:
- Starring the repository ⭐
- Sharing it with others
- Reporting issues or suggesting improvements

---

**Made with ❤️ for the DevOps community**
