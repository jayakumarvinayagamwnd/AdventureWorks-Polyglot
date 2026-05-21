# Recommendation Service

## Overview

The **Recommendation Service** generates product recommendations for Adventure Works customers using collaborative filtering and purchase history analysis. Results are cached in Redis for fast retrieval.

## Technology Stack

| Item         | Value         |
|--------------|--------------|
| Language     | Python 3.12  |
| Framework    | Flask        |
| Architecture | Vertical Slice |
| Cache        | Redis 7      |

## Port

| Environment | Port |
|-------------|------|
| Local (`flask run`) | 5000 |
| Docker (host) | 5006 |
| Docker (container) | 8080 |

## Project Structure

```
recommendation-service/
├── Dockerfile
├── .dockerignore
└── src/
    ├── app.py                  # Flask app entry point
    ├── features/
    │   ├── product_recommendations/
    │   │   ├── routes.py
    │   │   └── engine.py       # Recommendation logic
    │   └── related_products/
    └── cache/
        └── redis_client.py
```

## Environment Variables

| Variable | Description |
|----------|-------------|
| `PYTHONUNBUFFERED` | `1` (for real-time log output) |
| `REDIS_HOST` | Redis hostname (`redis`) |
| `REDIS_PORT` | Redis port (`6379`) |

## Cache Strategy

- Recommendations per customer are cached with a TTL of 1 hour.
- Cache key pattern: `reco:customer:{customerId}`

## Dependencies

| Service | Purpose |
|---------|---------|
| `redis` | Cache recommendation results |

## Running Locally

```bash
# Via Flask
cd services/recommendation-service/src
pip install flask redis
flask --app app run --port 5000

# Via Docker Compose
cd deployment
docker compose up -d recommendation-service redis
```

## Key Endpoints (Planned)

| Method | Route | Description |
|--------|-------|-------------|
| `GET` | `/recommendations/{customerId}` | Get product recs for customer |
| `GET` | `/recommendations/{customerId}/related` | Related product suggestions |
| `DELETE` | `/recommendations/{customerId}/cache` | Invalidate customer cache |
| `GET` | `/health` | Health check |

## Roadmap

- [ ] Initialize Python project with `requirements.txt`
- [ ] Set up Flask app structure
- [ ] Connect to Redis with `redis-py`
- [ ] Implement basic recommendation engine
- [ ] Implement caching layer
- [ ] Add health check endpoint
