# Cosmere RPG Infrastructure

Docker Compose infrastructure for the Cosmere RPG Character Sheet Manager.

## URLs

| Environment   | URL                        |
| ------------- | -------------------------- |
| Production    | <https://rpg.gunarsk.com/> |
| Local (HTTPS) | <https://localhost:8543/>  |

## Repositories

| Repo                                                                      | Description                                  |
| ------------------------------------------------------------------------- | -------------------------------------------- |
| [public-web](https://github.com/GunarsK-rpg/public-web)                   | Vue 3 frontend                               |
| [public-api](https://github.com/GunarsK-rpg/public-api)                   | Go backend API                               |
| [database](https://github.com/GunarsK-rpg/database)                       | PostgreSQL schema and migrations             |
| database-seeds                                                            | Seed data (private)                          |
| [auth-service](https://github.com/GunarsK-portfolio/auth-service)         | JWT authentication                           |
| [portfolio-common](https://github.com/GunarsK-portfolio/portfolio-common) | Shared Go libraries (auth, logging, metrics) |

## Services

| Service    | Port       | Description                  |
| ---------- | ---------- | ---------------------------- |
| Traefik    | 8100, 8543 | Reverse proxy with TLS       |
| PostgreSQL | 5435       | Database                     |
| Redis      | 6380       | Session cache                |
| MinIO      | 9010, 9011 | S3-compatible object storage |
| Flyway     | -          | Database migrations          |
| public-api | 8182       | Go backend API               |
| public-web | 8180       | Vue 3 frontend               |

## Quick Start

```bash
# 1. Generate secure credentials
task secrets:generate

# 2. Start all services
task services:up

# 3. View logs
task services:logs
```

## Prerequisites

- Docker and Docker Compose
- Task (task runner) - `go install github.com/go-task/task/v3/cmd/task@latest`
- Python 3 (for secret generation)

## Directory Structure

```text
infrastructure/
├── docker/
│   ├── minio/          # MinIO bucket policies
│   ├── redis/          # Redis configuration
│   └── traefik/        # Traefik config and certs
├── scripts/
│   └── generate-secrets.py
├── terraform/          # AWS infrastructure (managed separately)
├── .env.example        # Environment template
├── docker-compose.yml  # Service definitions
└── Taskfile.yml        # Task automation
```

## Environment Setup

1. Copy `.env.example` to `.env` (or run `task secrets:generate`)
2. All passwords are left empty in `.env.example` - the generator fills them
3. Never commit `.env` or `.secrets.txt` to version control

## Common Tasks

```bash
# Service management
task services:up        # Start all services
task services:down      # Stop all services
task services:restart   # Restart all services
task services:logs      # View all logs
task services:build     # Rebuild and restart
task services:clean     # Remove volumes (data loss!)

# Individual services
task public-api:logs    # View API logs
task public-api:rebuild # Rebuild API
task public-web:rebuild # Rebuild frontend

# Database
task db:console         # Connect via psql
task db:logs            # View database logs

# CI/CD
task ci:all             # Run all checks
task services:ci        # Run CI for all repos
```

## Port Allocation

| Resource      | Host Port |
| ------------- | --------- |
| PostgreSQL    | 5435      |
| Redis         | 6380      |
| MinIO API     | 9010      |
| MinIO Console | 9011      |

## Network

All services run on the `rpg_network` bridge network.

## Production Notes

- Production runs on AWS, managed via Terraform in the [infrastructure](https://github.com/GunarsK-portfolio/infrastructure) repo
- Production uses shared AWS Aurora (single cluster, separate databases)
- Local development uses separate PostgreSQL instances for isolation
