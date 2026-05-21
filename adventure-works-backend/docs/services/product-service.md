# Product Service

## Overview

The **Product Service** manages the product catalog for Adventure Works — products, categories, inventory levels, and pricing. It is consumed by several other services (Sales, Search, Recommendation).

## Technology Stack

| Item         | Value                    |
|--------------|-------------------------|
| Language     | JavaScript (Node.js)    |
| Framework    | Express / Fastify       |
| Architecture | Vertical Slice          |
| Database     | PostgreSQL 16           |
| Messaging    | RabbitMQ                |

## Port

| Environment | Port |
|-------------|------|
| Local | 3000 |
| Docker (host) | 5002 |
| Docker (container) | 8080 |

## Project Structure

```
product-service/
├── Dockerfile
├── .dockerignore
└── src/
    ├── app.js                  # Entry point
    ├── features/               # Vertical slices
    │   ├── products/
    │   │   ├── create.js
    │   │   ├── getById.js
    │   │   └── list.js
    │   ├── categories/
    │   └── inventory/
    ├── db/
    │   └── client.js           # pg client setup
    └── messaging/
        └── publisher.js
```

## Environment Variables

| Variable | Description |
|----------|-------------|
| `NODE_ENV` | `development` / `production` |
| `PORT` | App port (default `8080`) |
| `POSTGRES_HOST` | PostgreSQL hostname (`postgres`) |
| `POSTGRES_PORT` | PostgreSQL port (`5432`) |
| `POSTGRES_DB` | Database name (`adventureworks_product`) |
| `POSTGRES_USER` | DB username |
| `POSTGRES_PASSWORD` | DB password |
| `RABBITMQ_HOST` | RabbitMQ hostname (`rabbitmq`) |

## Database

- **Server:** PostgreSQL 16 (container: `postgres`, port `5432`)
- **Database Name:** `adventureworks_product`

## Dependencies

| Service | Purpose |
|---------|---------|
| `postgres` | Primary datastore |
| `rabbitmq` | Publish product events (ProductUpdated, StockChanged) |

## Running Locally

```bash
# Via Node.js
cd services/product-service
npm install
npm run dev

# Via Docker Compose
cd deployment
docker compose up -d product-service postgres rabbitmq
```

## Key Endpoints (Planned)

| Method | Route | Description |
|--------|-------|-------------|
| `GET` | `/products` | List products (paged) |
| `GET` | `/products/:id` | Get product by ID |
| `POST` | `/products` | Create product |
| `PUT` | `/products/:id` | Update product |
| `GET` | `/categories` | List categories |
| `GET` | `/health` | Health check |

## Roadmap

- [ ] Initialize Node.js project (`npm init`)
- [ ] Add Express or Fastify
- [ ] Connect to PostgreSQL with `pg` or `knex`
- [ ] Implement Products slice (CRUD)
- [ ] Implement Categories slice
- [ ] Integrate RabbitMQ publisher (`amqplib`)
- [ ] Add health check endpoint
