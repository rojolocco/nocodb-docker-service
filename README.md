# NocoDB Docker Service

This repository contains a Docker configuration to deploy [NocoDB](https://nocodb.com/), an open-source Airtable alternative.

## What is NocoDB?

NocoDB is an open-source platform that transforms any database into a smart spreadsheet. It allows you to:

- Create spreadsheet-like visual interfaces over SQL databases
- Build no-code applications
- Manage data with custom views, filters, and sorts
- Collaborate in real-time
- Automate workflows
- Integrate with external services via webhooks and APIs

## Repository Contents

This repository includes:

- `docker-compose.yaml`: Production configuration with resource limits
- `docker-compose.dev.yaml`: Development configuration with port mapping
- `.env.example`: Example of required environment variables
- `.dockerignore` and `.gitignore`: Files to exclude unnecessary content

## Prerequisites

- Docker and Docker Compose installed
- Caddy external network configured (`caddy_network`)
- PostgreSQL database (referenced as `pgvector` in the configuration)

## Configuration

1. Create an `.env` file based on `.env.example`:

```bash
cp .env.example .env
```

1. Edit the `.env` file with your PostgreSQL credentials:

``` env
# PostgreSQL Database Configuration
POSTGRES_USER=postgres
POSTGRES_PASSWORD=your_secure_password
POSTGRES_DB=nocodb

# NocoDB Configuration
NC_POSTGRESDB_HOST=pgvector
NC_POSTGRESDB_PORT=5432
NC_DB=pg://${NC_POSTGRESDB_HOST}:${NC_POSTGRESDB_PORT}?u=${POSTGRES_USER}&p=${POSTGRES_PASSWORD}&d=${POSTGRES_DB}
```

## Deployment

### For Production

```bash
docker-compose up -d
```

This configuration:

- Uses the latest NocoDB image
- Limits memory usage to 512MB and CPU to 0.5 cores
- Connects to the external `caddy_network` (for reverse proxy)
- Persists data in the `nocodb_data` volume
- Does not expose ports directly (assumes access through reverse proxy)

### For Development

```bash
docker-compose -f docker-compose.dev.yaml up -d
```

This configuration:

- Exposes NocoDB on port 8081 of the host
- Has no resource limits
- Maintains the same network and volume configurations as the production version

## Access

- **Production**: Access through your reverse proxy configuration (Caddy)
- **Development**: Access directly at `http://localhost:8081`

When starting for the first time, NocoDB will prompt you to create an admin account.

## Maintenance

### View Logs

```bash
docker logs nocodb
```

### Update to the Latest Version

```bash
docker-compose pull
docker-compose up -d
```

### Backup Data

Data is stored in the Docker volume `nocodb_data`. To backup:

```bash
docker run --rm -v nocodb_data:/data -v $(pwd):/backup alpine tar -czvf /backup/nocodb_data_backup.tar.gz /data
```

## Additional Notes

- This configuration assumes you have a PostgreSQL database running in a container named `pgvector`
- The `caddy_network` must exist beforehand (created by your Caddy configuration)
- NocoDB can also use SQLite, MySQL, or MS SQL Server as a database

## Resources

- [NocoDB Official Documentation](https://docs.nocodb.com/)
- [NocoDB GitHub Repository](https://github.com/nocodb/nocodb)
- [NocoDB Docker Hub](https://hub.docker.com/r/nocodb/nocodb)

## License

This project is licensed under the license included in the LICENSE file.
