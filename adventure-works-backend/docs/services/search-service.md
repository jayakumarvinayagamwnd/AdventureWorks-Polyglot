# Search Service

## Overview

The **Search Service** provides full-text and faceted search capabilities for Adventure Works products and orders. It indexes data from other services into Elasticsearch and exposes a unified search API.

## Technology Stack

| Item         | Value                |
|--------------|---------------------|
| Language     | Java 21             |
| Framework    | Spring Boot 3       |
| Architecture | Vertical Slice      |
| Search Engine| Elasticsearch 8.14  |

## Port

| Environment | Port |
|-------------|------|
| Local (`mvn spring-boot:run`) | 8080 |
| Docker (host) | 5008 |
| Docker (container) | 8080 |

## Project Structure

```
search-service/
├── Dockerfile
├── .dockerignore
└── src/
    └── main/
        └── java/com/adventureworks/search/
            ├── SearchApplication.java
            ├── features/
            │   ├── products/
            │   │   ├── ProductSearchController.java
            │   │   ├── ProductSearchService.java
            │   │   └── ProductDocument.java
            │   └── orders/
            └── config/
                └── ElasticsearchConfig.java
```

## Environment Variables

| Variable | Description |
|----------|-------------|
| `SPRING_PROFILES_ACTIVE` | `docker` for container config |
| `ELASTICSEARCH_HOST` | Elasticsearch URL (`http://elasticsearch:9200`) |

## Search Engine

- **Server:** Elasticsearch 8.14.3 (container: `elasticsearch`, port `9200`)
- **Security:** Disabled for dev (`xpack.security.enabled: false`)
- **Java Opts:** `-Xms512m -Xmx512m`

## Dependencies

| Service | Purpose |
|---------|---------|
| `elasticsearch` | Full-text search and indexing |

## Running Locally

```bash
# Via Maven
cd services/search-service
./mvnw spring-boot:run

# Via Docker Compose
cd deployment
docker compose up -d search-service elasticsearch
```

## Key Endpoints (Planned)

| Method | Route | Description |
|--------|-------|-------------|
| `GET` | `/search/products?q={query}` | Full-text product search |
| `GET` | `/search/products?category={cat}` | Faceted search by category |
| `POST` | `/index/products` | Index a product document |
| `DELETE` | `/index/products/{id}` | Remove product from index |
| `GET` | `/health` | Health check |

## Roadmap

- [ ] Initialize Maven project (`pom.xml`)
- [ ] Add Spring Boot + Spring Data Elasticsearch
- [ ] Configure Elasticsearch client
- [ ] Implement Products search slice
- [ ] Implement indexing consumer (RabbitMQ events)
- [ ] Add health check endpoint
