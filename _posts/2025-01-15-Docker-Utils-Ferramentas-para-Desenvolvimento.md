---
title: Docker Utils - Ferramentas Essenciais para Desenvolvimento
date: 2025-01-15 00:00:00 +0000
categories: [Docker, DevOps, Desenvolvimento]
tags: [docker, docker-compose, desenvolvimento, banco-dados, redis, postgresql, mysql, mongodb]
---

## Introdução ao Docker Utils

Acabei de lançar meu mais novo projeto: **[Docker Utils](https://github.com/rickmax/docker-utils)** - uma coleção de serviços úteis para desenvolvimento com Docker Compose. Este projeto nasceu da necessidade de ter um ambiente de desenvolvimento consistente e fácil de configurar.

### Por que este projeto?

Como desenvolvedores, constantemente precisamos de vários serviços para nossos projetos: bancos de dados, caches, filas de mensagens, ferramentas de email e muito mais. Em vez de instalar cada serviço individualmente em nossa máquina ou criar múltiplos arquivos Docker Compose para cada projeto, o **Docker Utils** fornece uma coleção centralizada e pronta para uso de serviços de desenvolvimento.

### Problemas que resolve

- ⚡ **Configuração Rápida**: Inicie qualquer serviço com um único comando
- 🧹 **Ambiente Limpo**: Não precisa instalar serviços diretamente na sua máquina
- 🔄 **Configuração Consistente**: Mesma configuração em diferentes projetos e membros da equipe
- 💾 **Persistência de Dados**: Seus dados são preservados entre reinicializações
- 🌐 **Múltiplos Projetos**: Uma configuração serve múltiplos projetos de desenvolvimento
- 🛠 **Conjunto Completo de Ferramentas**: Inclui interfaces web para gerenciamento fácil

## Serviços Disponíveis

### Bancos de Dados

- **PostgreSQL 17** - Porta: 5432
- **MySQL 8** - Porta: 3306
- **MongoDB 7** - Porta: 27017
- **Redis 7** - Porta: 6379
- **SQL Server 2022** - Porta: 1433
- **ElasticSearch 8.11** - Porta: 9200

### Ferramentas de Email

- **MailCatcher** - SMTP: 1025, Web: 1080

### Filas e Mensageria

- **RabbitMQ** - Porta: 5672, Gerenciamento: 15672

### Armazenamento

- **MinIO** (Compatível com S3) - API: 9000, Console: 9001

### Emuladores

- **LocalStack** (Serviços AWS) - Porta: 4566

### Interfaces Web

- **Adminer** - Porta: 8080 (MySQL, PostgreSQL, SQL Server)
- **RedisInsight** - Porta: 8001 (Redis)
- **Mongo Express** - Porta: 8081 (MongoDB)
- **Kibana** - Porta: 5601 (ElasticSearch)

## Como Usar

### 1. Clone o projeto

```bash
git clone https://github.com/rickmax/docker-utils.git
cd docker-utils
```

### 2. Configure o ambiente

```bash
cp .env.example .env
```

### 3. Inicie os serviços

```bash
# Iniciar todos os serviços
docker-compose up -d

# Iniciar apenas serviços específicos
docker-compose up -d postgres redis

# Ver logs
docker-compose logs -f [nome-do-serviço]

# Parar todos os serviços
docker-compose down

# Remover volumes (limpar dados)
docker-compose down -v
```

### Exemplos de Uso

```bash
# Bancos de dados
docker-compose up -d postgres          # PostgreSQL
docker-compose up -d mysql             # MySQL
docker-compose up -d mongodb           # MongoDB
docker-compose up -d redis             # Redis
docker-compose up -d sqlserver         # SQL Server
docker-compose up -d elasticsearch     # ElasticSearch

# Ferramentas e interfaces
docker-compose up -d adminer           # Interface web para bancos
docker-compose up -d redis-insight     # Interface Redis
docker-compose up -d mongo-express     # Interface MongoDB
docker-compose up -d kibana            # Interface ElasticSearch
docker-compose up -d mailcatcher       # Capturador de emails

# Mensageria e armazenamento
docker-compose up -d rabbitmq          # RabbitMQ
docker-compose up -d minio             # MinIO (S3)
docker-compose up -d localstack        # Emulador AWS

# Combinações úteis
docker-compose up -d postgres redis                    # Bancos principais
docker-compose up -d postgres adminer                  # PostgreSQL + interface
docker-compose up -d mongodb mongo-express             # MongoDB + interface
docker-compose up -d elasticsearch kibana              # ElasticSearch + Kibana
docker-compose up -d rabbitmq mailcatcher              # Mensageria + email
```

## Conexões

### PostgreSQL
- Host: localhost
- Porta: 5432
- Usuário: postgres
- Senha: postgres
- Banco: postgres

### MySQL
- Host: localhost
- Porta: 3306
- Usuário root: root / root
- Usuário regular: mysql / mysql
- Banco: mysql

### MongoDB
- Host: localhost
- Porta: 27017
- Usuário: root
- Senha: mongodb
- Banco: mongodb

### Redis
- Host: localhost
- Porta: 6379
- Sem autenticação por padrão

### SQL Server
- Host: localhost
- Porta: 1433
- Usuário: sa
- Senha: SqlServer@2024

### RabbitMQ
- Host: localhost
- Porta: 5672
- Gerenciamento: http://localhost:15672
- Usuário: rabbitmq
- Senha: rabbitmq

### MailCatcher
- SMTP: localhost:1025
- Interface Web: http://localhost:1080

### MinIO
- API: http://localhost:9000
- Console: http://localhost:9001
- Access Key: minioadmin
- Secret Key: minioadmin

### LocalStack
- Endpoint: http://localhost:4566
- Serviços: S3, DynamoDB, SQS, SNS, Lambda

## Interfaces Web

- **Adminer**: http://localhost:8080
- **RedisInsight**: http://localhost:8001
- **Mongo Express**: http://localhost:8081 (admin/admin)
- **Kibana**: http://localhost:5601
- **RabbitMQ Management**: http://localhost:15672
- **MinIO Console**: http://localhost:9001
- **MailCatcher**: http://localhost:1080

## Uso em Projetos

Para usar em seus projetos, você pode:

### 1. Referência Externa
Aponte suas aplicações para `localhost` nas portas correspondentes

### 2. Rede Docker
Conecte seu projeto à rede `docker-utils-network`:

```yaml
# No docker-compose.yml do seu projeto
networks:
  default:
    external:
      name: docker-utils_docker-utils-network
```

### 3. Estender
Use este compose como base:

```yaml
# Seu docker-compose.yml
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

## Dicas

- Use variáveis de ambiente para evitar expor senhas em produção
- Cada serviço tem health checks configurados
- Os dados são persistidos em volumes nomeados
- Para resetar um banco, remova o volume: `docker-compose down -v`
- Verifique logs se algum serviço falhar ao iniciar: `docker-compose logs [serviço]`

## Requisitos

- Docker 20.10+
- Docker Compose 2.0+
- Mínimo 4GB RAM disponível (para todos os serviços)
- Espaço em disco para volumes

## Compatibilidade

Este projeto é **cross-platform** e funciona em:

- ✅ **Windows** (Docker Desktop)
- ✅ **macOS** (Docker Desktop)
- ✅ **Linux** (Docker Engine/Docker Desktop)

Todas as imagens Docker usadas são oficiais e suportam múltiplas arquiteturas (x86_64, ARM64).

## Conclusão

O **Docker Utils** é perfeito para:

- **Prototipagem Rápida**: Teste ideias rapidamente sem configuração complexa
- **Aprendizado**: Experimente diferentes tecnologias com segurança
- **Desenvolvimento em Equipe**: Garanta que todos usem as mesmas versões dos serviços
- **Microserviços**: Toda a infraestrutura que você precisa em um lugar
- **Testes de Integração**: Teste contra serviços reais, não mocks

Este projeto nasceu da necessidade de ter um ambiente de desenvolvimento consistente e fácil de configurar. Espero que seja útil para outros desenvolvedores que enfrentam os mesmos desafios que eu enfrentei.

Confira o projeto no GitHub: [https://github.com/rickmax/docker-utils](https://github.com/rickmax/docker-utils)
