# HR Service

## Overview

The **HR Service** manages human resources data for Adventure Works — employees, departments, shifts, pay history, and job candidates. It serves internal tooling and reporting pipelines.

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
| Docker (host) | 5004 |
| Docker (container) | 8080 |

## Project Structure

```
hr-service/
├── Dockerfile
├── .dockerignore
└── src/
    └── HR.API/
        ├── HR.API.csproj
        ├── Program.cs
        └── Features/
            ├── Employees/
            │   ├── GetById/
            │   ├── List/
            │   └── Update/
            ├── Departments/
            └── Shifts/
```

## Environment Variables

| Variable | Description |
|----------|-------------|
| `ASPNETCORE_ENVIRONMENT` | `Development` / `Production` |
| `ConnectionStrings__SqlServer` | SQL Server connection string |
| `RabbitMQ__Host` | RabbitMQ hostname (`rabbitmq`) |

## Database

- **Server:** SQL Server 2022 (container: `sqlserver`, port `1433`)
- **Database Name:** `AdventureWorksHR`

## Dependencies

| Service | Purpose |
|---------|---------|
| `sqlserver` | Primary datastore |
| `rabbitmq` | Subscribe to org events |

## Running Locally

```bash
# Via Docker Compose
cd deployment
docker compose up -d hr-service sqlserver rabbitmq
```

## Key Endpoints (Planned)

| Method | Route | Description |
|--------|-------|-------------|
| `GET` | `/employees` | List employees (paged) |
| `GET` | `/employees/{id}` | Get employee by ID |
| `PUT` | `/employees/{id}` | Update employee |
| `GET` | `/departments` | List departments |
| `GET` | `/shifts` | List shifts |
| `GET` | `/health` | Health check |

## Roadmap

- [ ] Scaffold `dotnet new webapi` project
- [ ] Add EF Core + SQL Server
- [ ] Implement Employees vertical slice
- [ ] Implement Departments slice
- [ ] Add health checks
