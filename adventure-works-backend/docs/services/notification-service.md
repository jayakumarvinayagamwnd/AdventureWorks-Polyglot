# Notification Service

## Overview

The **Notification Service** delivers event-driven notifications in Adventure Works — order confirmations, shipping updates, and system alerts. It subscribes to RabbitMQ events and dispatches email/SMS/push notifications.

## Technology Stack

| Item         | Value         |
|--------------|--------------|
| Language     | Go 1.23      |
| Framework    | Fiber        |
| Architecture | Vertical Slice |
| Messaging    | RabbitMQ     |
| Cache        | Redis 7      |

## Port

| Environment | Port |
|-------------|------|
| Local | 8080 |
| Docker (host) | 5007 |
| Docker (container) | 8080 |

## Project Structure

```
notification-service/
├── Dockerfile
├── .dockerignore
└── cmd/
│   └── api/
│       └── main.go           # Entry point
└── internal/
    ├── features/
    │   ├── email/
    │   │   ├── handler.go
    │   │   └── sender.go
    │   ├── sms/
    │   └── push/
    ├── messaging/
    │   └── consumer.go       # RabbitMQ subscriber
    └── cache/
        └── redis.go
```

## Environment Variables

| Variable | Description |
|----------|-------------|
| `APP_ENV` | `development` / `production` |
| `PORT` | App port (`8080`) |
| `REDIS_ADDR` | Redis address (`redis:6379`) |
| `RABBITMQ_HOST` | RabbitMQ hostname (`rabbitmq`) |

## Dependencies

| Service | Purpose |
|---------|---------|
| `rabbitmq` | Subscribe to domain events (OrderCreated, etc.) |
| `redis` | Deduplication / rate limiting |

## Event Subscriptions (Planned)

| Exchange | Routing Key | Action |
|----------|-------------|--------|
| `sales` | `order.created` | Send order confirmation email |
| `sales` | `order.shipped` | Send shipping notification |
| `purchasing` | `po.approved` | Notify supplier |

## Running Locally

```bash
# Via Go
cd services/notification-service
go mod tidy
go run ./cmd/api

# Via Docker Compose
cd deployment
docker compose up -d notification-service rabbitmq redis
```

## Key Endpoints (Planned)

| Method | Route | Description |
|--------|-------|-------------|
| `POST` | `/notifications/email` | Send email manually |
| `POST` | `/notifications/sms` | Send SMS manually |
| `GET` | `/health` | Health check |

## Roadmap

- [ ] Initialize Go module (`go mod init`)
- [ ] Add Fiber framework
- [ ] Connect to RabbitMQ with `amqp091-go`
- [ ] Connect to Redis with `go-redis`
- [ ] Implement Email notification feature
- [ ] Implement RabbitMQ consumer
- [ ] Add health check endpoint
