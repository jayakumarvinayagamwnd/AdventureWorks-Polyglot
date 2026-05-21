# Purchasing Service

## Overview

The **Purchasing Service** handles supplier management, purchase orders, and goods receipt for the Adventure Works platform. It tracks procurement workflows from PO creation to fulfillment.

## Stack

| Item | Value |
|------|-------|
| Language | C# |
| Framework | ASP.NET Core 8 Minimal API |
| Architecture | Vertical Slice |
| Database | SQL Server 2022 |
| Messaging | RabbitMQ |

## Port

| Environment | Port |
|-------------|------|
| Local (`dotnet run`) | 5220 |
| Docker (host) | 5003 |
| Docker (container) | 8080 |

## Project Structure

```
purchasing-service/
├── Dockerfile
├── .dockerignore
└── src/
    └── Purchasing.API/
        ├── Purchasing.API.csproj
        ├── Program.cs
        └── Features/
            ├── PurchaseOrders/
            │   ├── Create/
            │   ├── Approve/
            │   └── List/
            ├── Suppliers/
            └── GoodsReceipt/
```

## Environment Variables

| Variable | Description |
|----------|-------------|
| `ASPNETCORE_ENVIRONMENT` | `Development` / `Production` |
| `ConnectionStrings__SqlServer` | SQL Server connection string |
| `RabbitMQ__Host` | RabbitMQ hostname (`rabbitmq`) |

## Database

- **Server:** SQL Server 2022 (container: `sqlserver`, port `1433`)
- **Database Name:** `AdventureWorksPurchasing`

## Dependencies

| Service | Purpose |
|---------|---------|
| `sqlserver` | Primary datastore |
| `rabbitmq` | Publish/subscribe purchasing events |

## Running Locally

```bash
# Via Docker Compose
cd deployment
docker compose up -d purchasing-service sqlserver rabbitmq
```

## Key Endpoints (Planned)

| Method | Route | Description |
|--------|-------|-------------|
| `POST` | `/purchase-orders` | Create purchase order |
| `GET` | `/purchase-orders/{id}` | Get PO by ID |
| `PUT` | `/purchase-orders/{id}/approve` | Approve PO |
| `GET` | `/suppliers` | List suppliers |
| `GET` | `/health` | Health check |

## Roadmap

- [ ] Scaffold `dotnet new webapi` project
- [ ] Add EF Core + SQL Server
- [ ] Implement PurchaseOrders vertical slice
- [ ] Implement Suppliers slice
- [ ] Integrate RabbitMQ publisher
- [ ] Add health checks
