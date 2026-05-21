# AdventureWorks-Polyglot
Master modern software architecture by building production-grade APIs using the Adventure Works database as the backend

---

## Table of Contents
1. [Architecture Styles](#architecture-styles)
2. [Code Principles](#code-principles)
3. [Cloud Principles](#cloud-principles)
4. [Implementation Practices](#implementation-practices)
5. [Learning Phases](#learning-phases)
6. [Project Structure](#project-structure)

---

## Architecture Styles

### 1. Microservices Architecture

**Objective:** Break monolithic Adventure Works into independently deployable services.

**Services to Create:**
- **Sales Service** - Manage orders, customers, territories
- **Product Service** - Product catalog, inventory, manufacturing
- **Purchasing Service** - Vendor management, purchase orders
- **HR Service** - Employee management, departments
- **Person/Contact Service** - Business entities, addresses, contacts

**Key Considerations:**
- Service boundaries based on business domains
- Database per service pattern
- API versioning and deprecation strategies
- Inter-service communication (sync/async)
- Shared data challenges and solutions
- Distributed transactions (Saga pattern)
- Service discovery
- Circuit breakers and resilience

**Success Metrics:**
- Independent deployment of each service
- Reduced coupling between services
- Clear service contracts (OpenAPI/Swagger)
- Measurable latency and throughput per service

### 2. Clean Architecture

**Objective:** Build layered, maintainable, testable code structure.

**Implementation:**
- **Domain Layer** - Core business entities (Order, Product, Customer)
- **Application Layer** - Use cases (CreateOrder, GetProductDetails, UpdateSalesTerritory)
- **Interface/Presentation Layer** - REST controllers, GraphQL resolvers
- **Infrastructure Layer** - Database repositories, external service clients

**Key Practices:**
- Dependency injection
- Abstraction of external dependencies
- High cohesion within layers
- Low coupling between layers
- No framework leakage into domain logic
### 3. Domain-Driven Design (DDD)

**Objective:** Model business domains explicitly in code.

**Bounded Contexts in Adventure Works:**

#### Sales Bounded Context
- **Aggregates:** Order (root), OrderLine, Customer
- **Value Objects:** Money, ProductId, OrderStatus, CustomerTier
- **Repositories:** OrderRepository, CustomerRepository
- **Domain Events:** OrderCreated, OrderShipped, PaymentReceived

#### Product Bounded Context
- **Aggregates:** Product (root), ProductModel, BillOfMaterials
- **Value Objects:** SKU, ProductStatus, Price, Inventory
- **Repositories:** ProductRepository, InventoryRepository
- **Domain Events:** ProductCreated, StockAdjusted, ManufactureCompleted

#### Purchasing Bounded Context
- **Aggregates:** PurchaseOrder (root), PurchaseOrderLine, Vendor
- **Value Objects:** VendorId, OrderStatus, LeadTime
- **Repositories:** VendorRepository, PurchaseOrderRepository
- **Domain Events:** PurchaseOrderCreated, DeliveryReceived

**Key Elements:**
- Ubiquitous Language - Shared terminology across team
- Anti-Corruption Layer - Translate between bounded contexts
- Aggregate boundaries - Transaction consistency zones
- Domain Events - Communication between contexts
- Repository pattern - Abstraction over persistence

---

### 4. Event-Driven Architecture

**Objective:** Decouple services through asynchronous event streaming.

**Core Events:**

**Sales Domain Events:**
- `OrderCreated` → Trigger inventory deduction, notifications
- `OrderPaid` → Update financial records, trigger fulfillment
- `OrderShipped` → Notify customer, update delivery service
- `DeliveryConfirmed` → Complete transaction, request feedback

**Product Domain Events:**
- `StockLevelChanged` → Update search index, notify stakeholders
- `ProductDiscontinued` → Notify active customers, clear inventory
- `ManufactureStarted/Completed` → Update work order status

**Purchasing Domain Events:**
- `PurchaseOrderCreated` → Send to vendor, set delivery SLA
- `GoodsReceived` → Update inventory, verify quality
- `InvoiceReceived` → Trigger payment processing

**Implementation Technology:**
- Message Broker: RabbitMQ, Apache Kafka, Azure Service Bus, AWS SNS/SQS
- Event Store: Event Sourcing, Change Data Capture (CDC)
- Message Patterns: Pub/Sub, Request/Reply, Saga

**Benefits:**
- Temporal decoupling of services
- Event replay for audit and debugging
- Natural scaling for event subscribers
- Eventual consistency handling

---

### 5. Docker & Containerization

**Objective:** Package each microservice as a container for consistent deployment.

**Container Strategy:**

### 6. Polyglot Architecture

**Objective:** Use the best tool/language for each service.

**Recommended Tech Stack per Service:**

| Service | Primary Language | Framework | Database | Justification |
|---------|-----------------|-----------|----------|---|
| **Sales API** | C# | ASP.NET Core | SQL Server | Complex business logic, transactions |
| **Product API** | Node.js | Express/Fastify | PostgreSQL | Fast reads, JSON-native, elastic |
| **Reporting/Analytics** | Python | FastAPI | ClickHouse/Cloud Data Warehouse | Data processing, ML ready |
| **Notification Service** | Go | Fiber | Redis | High throughput, low latency |
| **Search Service** | Java | Spring Boot | Elasticsearch | Full-text search, agile querying |
| **Recommendation Engine** | Python | Flask | TensorFlow | ML capabilities |

**Communication Layer:**
- REST (JSON) - Standard synchronous
- gRPC (Protocol Buffers) - High-performance inter-service
- GraphQL - Flexible client queries
- OpenAPI/Swagger - Contract-driven development

**Principles:**
- Technology diversity only where justified
- Clear contracts between services (API specs)
- Language-agnostic monitoring and logging
- Shared patterns (Circuit Breaking, Retries, etc.)

**Vertical Slice Folder Templates (Per Polyglot Framework):**

## Code Principles

### SOLID Principles (Architectural Lens)

## Cloud Principles

## Implementation Practices

## Learning Phases

## Project Structure

/deployment> docker compose build
/deployment> docker compose up -d
/deployment> docker compose ps