# n8n-self-host-starter-kit

**n8n Self-Host Starter Kit** is an open-source Docker Compose template designed to swiftly initialize a self-hosted n8n environment for both local development and production deployment.

This starter kit provides a ready-to-use configuration for running n8n with all necessary components including workers, Redis, and PostgreSQL. This is a standard n8n setup without any AI components.

### What’s included

✅ [**Self-hosted n8n**](https://n8n.io/) - Low-code automation platform with over 400 integrations

✅ [**n8n Worker**](https://docs.n8n.io/hosting/scaling/queue-mode/) - Separate worker process for handling workflow executions

✅ [**Redis**](https://redis.io/) - In-memory data store used for queuing and caching

✅ [**PostgreSQL**](https://www.postgresql.org/) - Reliable database for storing workflow data and configurations

✅ [**pgAdmin 4**](https://www.pgadmin.org/) - Web-based PostgreSQL administration tool

### Environment Configurations

This starter kit includes two Docker Compose configurations:

1. **docker-compose.yml** - For local development environment, includes:
   - n8n server
   - n8n worker
   - Redis (for queuing and caching)
   - PostgreSQL (for workflow data storage)
   - pgAdmin 4 (for database administration)

2. **prod.docker-compose.yml** - For production environment:
   - n8n server
   - n8n worker
   - Assumes external Redis and PostgreSQL services are already set up
   - Configured to connect to your existing Redis and PostgreSQL instances

## Getting Started

### Prerequisites

- [Docker](https://docs.docker.com/get-docker/) and [Docker Compose](https://docs.docker.com/compose/install/) installed on your system
- Git (optional, for cloning the repository)

### Local Development Setup

1. **Clone the repository**

   ```bash
   git clone https://github.com/ivannguyendev/n8n-self-host-starter-kit.git
   cd n8n-self-host-starter-kit
   ```

   Or download and extract the ZIP file.

2. **Configure environment variables**

   The repository includes a `local.env` file with default settings. You can modify these settings as needed:

   ```bash
   # Review and modify if necessary
   nano local.env
   ```

   For security in production, you should change the default passwords and encryption keys.

3. **Start the local development environment**

   ```bash
   # Create symbolic link from local.env to .env
   ln -sf local.env .env

   # Start all services
   docker compose up -d
   ```

4. **Access n8n and pgAdmin**

   Open your browser and navigate to:

   ```
   n8n:     http://localhost:5678
   pgAdmin: http://localhost:5050
   ```

   For pgAdmin, use the following default credentials (or the ones you set in your environment file):
   - Email: admin@example.com
   - Password: admin

   After logging into pgAdmin, you'll need to add a new server connection:
   1. Right-click on "Servers" in the left panel and select "Create" > "Server..."
   2. On the "General" tab, give it a name (e.g., "n8n-postgres")
   3. On the "Connection" tab, enter:
      - Host: postgres
      - Port: 5432
      - Maintenance database: n8n
      - Username: root (or your POSTGRES_USER value)
      - Password: password (or your POSTGRES_PASSWORD value)

5. **Stop the environment**

   ```bash
   docker compose down
   ```

   To stop and remove all containers, networks, and volumes:

   ```bash
   docker compose down -v
   ```

### Production Deployment

1. **Configure production environment**

   Create a production environment file:

   ```bash
   cp local.env prod.env
   ```

   Edit the `prod.env` file with your production settings, including:
   - Strong passwords
   - Secure encryption keys
   - Connection details for your external PostgreSQL and Redis instances

2. **Deploy using production configuration**

   ```bash
   # Create symbolic link from prod.env to .env
   ln -sf prod.env .env

   # Start using production configuration
   docker compose -f prod.docker-compose.yml up -d
   ```

3. **Access your production n8n instance**

   Access n8n at the configured domain or IP address on port 5678 (or your custom configured port).

## Maintenance

### Updating n8n

To update to the latest version of n8n:

```bash
# Pull the latest images
docker compose pull

# Restart the services with the new images
docker compose up -d
```

### Backup and Restore

The starter kit includes a backup directory structure at `./n8n/backup/` that can be used to store workflow and credential exports.

To backup your workflows and credentials:

```bash
# Export workflows
docker compose exec n8n n8n export:workflow --all --output=/backup/workflows

# Export credentials
docker compose exec n8n n8n export:credentials --all --output=/backup/credentials
```

Restore is handled automatically on startup by the n8n-import service.

## Troubleshooting

### Checking Logs

```bash
# View logs for all services
docker compose logs

# View logs for a specific service
docker compose logs n8n

# Follow logs in real-time
docker compose logs -f
```

### Common Issues

1. **Database connection errors**
   - Check that PostgreSQL is running: `docker compose ps postgres`
   - Verify database credentials in your .env file

2. **Redis connection errors**
   - Check that Redis is running: `docker compose ps redis`
   - Verify Redis connection settings in your .env file

3. **Permission issues**
   - Ensure proper permissions on mounted volumes: `chmod -R 777 ./n8n`

## Customization

### Adding Custom Environment Variables

Edit the `.env` file to add custom environment variables, then update the `docker-compose.yml` file to pass these variables to the appropriate services.

### Modifying Service Configuration

To modify service configurations, edit the `docker-compose.yml` file directly. For production, modify the `prod.docker-compose.yml` file.