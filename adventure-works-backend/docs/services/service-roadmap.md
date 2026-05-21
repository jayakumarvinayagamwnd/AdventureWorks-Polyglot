# Service Roadmap

This document provides a summary of all microservices in the AdventureWorks-Polyglot platform, including their core technologies, frameworks, tools, and databases.

| Service                | Language             | Framework(s)           | Database         | Messaging/Cache | Other Tools         |
|------------------------|----------------------|------------------------|------------------|-----------------|---------------------|
| Search Service         | Java 21              | Spring Boot 3          | Elasticsearch    | -               | -                   |
| Sales Service          | C#                   | ASP.NET Core 8 Minimal | SQL Server 2022  | RabbitMQ, Redis | -                   |
| Reporting Service      | Python 3.12          | FastAPI                | ClickHouse 24.8  | -               | -                   |
| Recommendation Service | Python 3.12          | Flask                  | -                | Redis 7         | -                   |
| Purchasing Service     | C#                   | ASP.NET Core 8 Minimal | SQL Server 2022  | RabbitMQ        | -                   |
| Product Service        | JavaScript (Node.js) | Express / Fastify      | PostgreSQL 16    | RabbitMQ        | -                   |
| Notification Service   | Go 1.23              | Fiber                  | -                | RabbitMQ, Redis | -                   |
| HR Service             | C#                   | ASP.NET Core 8 Minimal | SQL Server 2022  | RabbitMQ        | -                   |

## Legend
- **Language:** Main programming language used
- **Framework(s):** Primary web/service framework
- **Database:** Main data store
- **Messaging/Cache:** Messaging brokers or cache systems used
- **Other Tools:** Notable supporting tools or platforms

---

For detailed documentation, see each service's markdown file in `docs/services/`.
