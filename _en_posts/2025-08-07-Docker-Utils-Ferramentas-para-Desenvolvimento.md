---
title: Docker Utils - Essential Development Tools
date: 2025-08-07 00:00:00 +0000
categories: [Docker, DevOps, Development]
tags: [docker, docker-compose, development, databases, redis, postgresql, mysql, mongodb]
---

## Introduction to Docker Utils

I just launched my newest project: **[Docker Utils](https://github.com/rickmax/docker-utils)** - a collection of useful services for development with Docker Compose. This project was born from the need to have a consistent and easy-to-configure development environment.

### Why this project?

As developers, we constantly need various services for our projects: databases, caches, message queues, email tools, and more. Instead of installing each service individually on our machine or creating multiple Docker Compose files for each project, **Docker Utils** provides a centralized, ready-to-use collection of development services.

### Problems it solves

- ⚡ **Quick Setup**: Start any service with a single command
- 🧹 **Clean Environment**: No need to install services directly on your machine
- 🔄 **Consistent Configuration**: Same setup across different projects and team members
- 💾 **Data Persistence**: Your data is preserved between restarts
- 🌐 **Multiple Projects**: One setup serves multiple development projects
- 🛠 **Complete Toolset**: Includes web interfaces for easy management

## Available Services

### Databases

- **PostgreSQL 17** - Port: 5432
- **MySQL 8** - Port: 3306
- **MongoDB 7** - Port: 27017
- **Redis 7** - Port: 6379
- **SQL Server 2022** - Port: 1433
- **ElasticSearch 8.11** - Port: 9200

### Email Tools

- **MailCatcher** - SMTP: 1025, Web: 1080

### Queues and Messaging

- **RabbitMQ** - Port: 5672, Management: 15672

### Storage

- **MinIO** (S3 Compatible) - API: 9000, Console: 9001

### Emulators

- **LocalStack** (AWS Services) - Port: 4566

### Web Interfaces

- **Adminer** - Port: 8080 (MySQL, PostgreSQL, SQL Server)
- **RedisInsight** - Port: 8001 (Redis)
- **Mongo Express** - Port: 8081 (MongoDB)
- **Kibana** - Port: 5601 (ElasticSearch)

## How to Use

### 1. Clone the project

```bash
git clone https://github.com/rickmax/docker-utils.git
cd docker-utils
```

### 2. Configure the environment

```bash
cp .env.example .env
```

### 3. Start the services

```bash
# Start all services
docker-compose up -d

# Start only specific services
docker-compose up -d postgres redis

# View logs
docker-compose logs -f [service-name]

# Stop all services
docker-compose down

# Remove volumes (clean data)
docker-compose down -v
```

### Usage Examples

```bash
# Databases
docker-compose up -d postgres          # PostgreSQL
docker-compose up -d mysql             # MySQL
docker-compose up -d mongodb           # MongoDB
docker-compose up -d redis             # Redis
docker-compose up -d sqlserver         # SQL Server
docker-compose up -d elasticsearch     # ElasticSearch

# Tools and interfaces
docker-compose up -d adminer           # Web interface for databases
docker-compose up -d redis-insight     # Redis interface
docker-compose up -d mongo-express     # MongoDB interface
docker-compose up -d kibana            # ElasticSearch interface
docker-compose up -d mailcatcher       # Email catcher

# Messaging and storage
docker-compose up -d rabbitmq          # RabbitMQ
docker-compose up -d minio             # MinIO (S3)
docker-compose up -d localstack        # AWS emulator

# Useful combinations
docker-compose up -d postgres redis                    # Main databases
docker-compose up -d postgres adminer                  # PostgreSQL + interface
docker-compose up -d mongodb mongo-express             # MongoDB + interface
docker-compose up -d elasticsearch kibana              # ElasticSearch + Kibana
docker-compose up -d rabbitmq mailcatcher              # Messaging + email
```

## Connections

### PostgreSQL
- Host: localhost
- Port: 5432
- User: postgres
- Password: postgres
- Database: postgres

### MySQL
- Host: localhost
- Port: 3306
- Root user: root / root
- Regular user: mysql / mysql
- Database: mysql

### MongoDB
- Host: localhost
- Port: 27017
- User: root
- Password: mongodb
- Database: mongodb

### Redis
- Host: localhost
- Port: 6379
- No authentication by default

### SQL Server
- Host: localhost
- Port: 1433
- User: sa
- Password: SqlServer@2024

### RabbitMQ
- Host: localhost
- Port: 5672
- Management: http://localhost:15672
- User: rabbitmq
- Password: rabbitmq

### MailCatcher
- SMTP: localhost:1025
- Web Interface: http://localhost:1080

### MinIO
- API: http://localhost:9000
- Console: http://localhost:9001
- Access Key: minioadmin
- Secret Key: minioadmin

### LocalStack
- Endpoint: http://localhost:4566
- Services: S3, DynamoDB, SQS, SNS, Lambda

## Web Interfaces

- **Adminer**: http://localhost:8080
- **RedisInsight**: http://localhost:8001
- **Mongo Express**: http://localhost:8081 (admin/admin)
- **Kibana**: http://localhost:5601
- **RabbitMQ Management**: http://localhost:15672
- **MinIO Console**: http://localhost:9001
- **MailCatcher**: http://localhost:1080

## Usage in Projects

To use in your projects, you can:

### 1. External Reference
Point your applications to `localhost` on the corresponding ports

### 2. Docker Network
Connect your project to the `docker-utils-network`:

```yaml
# In your project's docker-compose.yml
networks:
  default:
    external:
      name: docker-utils_docker-utils-network
```

### 3. Extend
Use this compose as a base:

```yaml
# Your project's docker-compose.yml
version: '3.8'

services:
  app:
    build: .
    networks:
      - docker-utils_docker-utils-network
    external_links:
      - docker-utils-postgres:postgres
      - docker-utils-redis:redis

networks:
  docker-utils_docker-utils-network:
    external: true
```

## Tips

- Use environment variables to avoid exposing passwords in production
- Each service has health checks configured
- Data is persisted in named volumes
- To reset a database, remove the volume: `docker-compose down -v`
- Check logs if any service fails to start: `docker-compose logs [service]`

## Requirements

- Docker 20.10+
- Docker Compose 2.0+
- Minimum 4GB RAM available (for all services)
- Disk space for volumes

## Compatibility

This project is **cross-platform** and works on:

- ✅ **Windows** (Docker Desktop)
- ✅ **macOS** (Docker Desktop)
- ✅ **Linux** (Docker Engine/Docker Desktop)

All Docker images used are official and support multiple architectures (x86_64, ARM64).

## Conclusion

**Docker Utils** is perfect for:

- **Rapid Prototyping**: Test ideas quickly without complex setup
- **Learning**: Experiment with different technologies safely
- **Team Development**: Ensure everyone uses the same service versions
- **Microservices**: All the infrastructure you need in one place
- **Integration Testing**: Test against real services, not mocks

This project was born from the need to have a consistent and easy-to-configure development environment. I hope it's useful for other developers who face the same challenges I faced.

Check out the project on GitHub: [https://github.com/rickmax/docker-utils](https://github.com/rickmax/docker-utils)
