# n8n with Caddy and PostgreSQL

This is a combined deployment configuration for n8n that includes:
- **n8n**: Workflow automation platform
- **Caddy**: Reverse proxy with automatic HTTPS
- **PostgreSQL**: Database for n8n data persistence

## Features

- Automatic HTTPS with Caddy
- PostgreSQL database for reliable data storage
- Health checks for database connectivity
- Local file storage support
- Production-ready configuration

## Prerequisites

- Docker and Docker Compose installed
- A domain name pointing to your server
- Ports 80 and 443 available

## Quick Start

1. **Clone or download this configuration**

2. **Set up environment variables**
   ```bash
   cp env.example .env
   # Edit .env with your actual values
   ```

3. **Create required Docker volumes**
   ```bash
   docker volume create caddy_data
   docker volume create n8n_data
   ```

4. **Update the Caddyfile**
   Edit `caddy_config/Caddyfile` and replace `<domain>.<suffix>` with your actual domain.

5. **Start the services**
   ```bash
   docker-compose up -d
   ```

## Environment Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `DOMAIN_NAME` | Your main domain | `example.com` |
| `SUBDOMAIN` | Subdomain for n8n | `n8n` |
| `GENERIC_TIMEZONE` | Timezone for n8n | `UTC` |
| `POSTGRES_USER` | PostgreSQL admin user | `postgres` |
| `POSTGRES_PASSWORD` | PostgreSQL admin password | `secure_password` |
| `POSTGRES_DB` | Database name | `n8n` |
| `POSTGRES_NON_ROOT_USER` | n8n database user | `n8n_user` |
| `POSTGRES_NON_ROOT_PASSWORD` | n8n database password | `secure_password` |
| `DATA_FOLDER` | Local files directory | `./data` |

## Services

### n8n
- **Port**: 5678 (internal), accessible via Caddy
- **Database**: PostgreSQL
- **Storage**: Docker volume + local files
- **URL**: `https://n8n.yourdomain.com`

### Caddy
- **Ports**: 80, 443
- **Features**: Automatic HTTPS, reverse proxy
- **Configuration**: `caddy_config/Caddyfile`

### PostgreSQL
- **Port**: 5432 (internal only)
- **Health Check**: Enabled
- **Initialization**: Automatic user creation

## File Structure

```
.
├── docker-compose.yml      # Main deployment configuration
├── caddy_config/
│   └── Caddyfile          # Caddy reverse proxy configuration
├── init-data.sh           # PostgreSQL initialization script
├── env.example            # Environment variables template
├── local_files/           # Local file storage (create if needed)
└── README.md              # This file
```

## Security Notes

- Change all default passwords
- Use strong, unique passwords for database users
- Keep your `.env` file secure and never commit it to version control
- Consider using Docker secrets for production deployments

## Troubleshooting

### Database Connection Issues
- Ensure PostgreSQL is healthy: `docker-compose ps`
- Check logs: `docker-compose logs postgres`

### Caddy Issues
- Verify domain configuration in Caddyfile
- Check Caddy logs: `docker-compose logs caddy`

### n8n Issues
- Check n8n logs: `docker-compose logs n8n`
- Verify database connectivity
- Check environment variables

## Backup

To backup your data:
```bash
# Backup n8n data
docker run --rm -v n8n_data:/data -v $(pwd):/backup alpine tar czf /backup/n8n_backup.tar.gz -C /data .

# Backup PostgreSQL data
docker-compose exec postgres pg_dump -U $POSTGRES_USER $POSTGRES_DB > backup.sql
```

## License

This configuration is provided as-is for educational and deployment purposes.# leanlabs-n8n
