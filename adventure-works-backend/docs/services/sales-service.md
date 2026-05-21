# Sales Service

## Overview

The **Sales Service** is responsible for all sales-related business operations in the Adventure Works platform. It manages orders, customers, invoices, and integrates with downstream services via messaging.

## Stack

| Item | Value |
|------|-------|
| Language | C# |
| Framework | ASP.NET Core 8 Minimal API |
| Architecture | Vertical Slice |
| Database | SQL Server 2022 |
| Messaging | RabbitMQ |
| Cache | Redis |

## Port

| Environment | Port |
|-------------|------|
| Local (`dotnet run`) | 5220 |
| Docker (host) | 5001 |
| Docker (container) | 8080 |

## Project Structure

```
sales-service/
├── Dockerfile
├── .dockerignore
└── src/
    └── Sales.API/
        ├── Sales.API.csproj          # net10.0 target
        ├── Program.cs                # Minimal hosting + route registration
        ├── Properties/
        │   └── launchSettings.json
        └── Features/                 # Vertical slices (to be created)
            ├── Orders/
            │   ├── Create/
            │   ├── GetById/
            │   └── List/
            ├── Customers/
            └── Invoices/
```

## Environment Variables

| Variable | Description |
|----------|-------------|
| `ASPNETCORE_ENVIRONMENT` | `Development` / `Production` |
| `ConnectionStrings__SqlServer` | SQL Server connection string |
| `RabbitMQ__Host` | RabbitMQ hostname (default: `rabbitmq`) |
| `Redis__ConnectionString` | Redis connection (default: `redis:6379`) |

## Database

- **Server:** SQL Server 2022 (container: `sqlserver`, port `1433`)
- **Database Name:** `AdventureWorksSales`
- **AdventureWorks Source:** `AdventureWorks2022`
- **Sales Schema Tables Available:** `19`

| # | Table |
|---|-------|
| 1 | `Sales.CountryRegionCurrency` |
| 2 | `Sales.CreditCard` |
| 3 | `Sales.Currency` |
| 4 | `Sales.CurrencyRate` |
| 5 | `Sales.Customer` |
| 6 | `Sales.PersonCreditCard` |
| 7 | `Sales.SalesOrderDetail` |
| 8 | `Sales.SalesOrderHeader` |
| 9 | `Sales.SalesOrderHeaderSalesReason` |
| 10 | `Sales.SalesPerson` |
| 11 | `Sales.SalesPersonQuotaHistory` |
| 12 | `Sales.SalesReason` |
| 13 | `Sales.SalesTaxRate` |
| 14 | `Sales.SalesTerritory` |
| 15 | `Sales.SalesTerritoryHistory` |
| 16 | `Sales.ShoppingCartItem` |
| 17 | `Sales.SpecialOffer` |
| 18 | `Sales.SpecialOfferProduct` |
| 19 | `Sales.Store` |

## Dependencies

| Service | Purpose |
|---------|---------|
| `sqlserver` | Primary datastore |
| `rabbitmq` | Publish domain events (OrderCreated, etc.) |
| `redis` | Distributed cache / idempotency keys |

## Running Locally

```bash
# Via dotnet CLI
cd services/sales-service/src/Sales.API
dotnet run

# Via Docker (standalone)
docker build -t sales-api:dev ./services/sales-service
docker run -d --name sales-api-local -p 5001:8080 sales-api:dev

# Via Docker Compose (with full infra)
cd deployment
docker compose up -d sales-service sqlserver rabbitmq redis
```

## Build

```bash
cd services/sales-service/src/Sales.API
dotnet build
dotnet publish -c Release
```

## Key Endpoints (Planned)

### Orders

| Method | Route | Description |
|--------|-------|-------------|
| `GET` | `/orders` | List all orders (paged, filterable by status/date/customer) |
| `GET` | `/orders/{id}` | Get order by ID (includes line items) |
| `POST` | `/orders` | Create a new sales order |
| `PUT` | `/orders/{id}` | Replace/update a full order |
| `PATCH` | `/orders/{id}` | Partial update (e.g. change status, update due date) |
| `DELETE` | `/orders/{id}` | Cancel / soft-delete an order |
| `GET` | `/orders/{id}/line-items` | List all line items for an order |
| `POST` | `/orders/{id}/line-items` | Add a line item to an order |
| `PUT` | `/orders/{id}/line-items/{lineId}` | Update a specific line item |
| `DELETE` | `/orders/{id}/line-items/{lineId}` | Remove a line item from an order |
| `POST` | `/orders/{id}/submit` | Submit order for processing |
| `POST` | `/orders/{id}/cancel` | Cancel a submitted order |
| `POST` | `/orders/{id}/ship` | Mark order as shipped |

### Customers

| Method | Route | Description |
|--------|-------|-------------|
| `GET` | `/customers` | List all customers (paged, filterable) |
| `GET` | `/customers/{id}` | Get customer by ID |
| `POST` | `/customers` | Create a new customer |
| `PUT` | `/customers/{id}` | Replace full customer record |
| `PATCH` | `/customers/{id}` | Partial update (e.g. update contact info) |
| `DELETE` | `/customers/{id}` | Soft-delete a customer |
| `GET` | `/customers/{id}/orders` | Get all orders for a customer |
| `GET` | `/customers/{id}/addresses` | List customer addresses |
| `POST` | `/customers/{id}/addresses` | Add a new address |
| `PUT` | `/customers/{id}/addresses/{addrId}` | Update an address |
| `DELETE` | `/customers/{id}/addresses/{addrId}` | Remove an address |

### Invoices

| Method | Route | Description |
|--------|-------|-------------|
| `GET` | `/invoices` | List all invoices (paged) |
| `GET` | `/invoices/{id}` | Get invoice by ID |
| `POST` | `/invoices` | Generate invoice from an order |
| `PATCH` | `/invoices/{id}` | Update invoice status (paid, void, etc.) |
| `DELETE` | `/invoices/{id}` | Void / soft-delete an invoice |
| `GET` | `/invoices/{id}/pdf` | Download invoice as PDF |

### Sales Territories & Regions

| Method | Route | Description |
|--------|-------|-------------|
| `GET` | `/territories` | List all sales territories |
| `GET` | `/territories/{id}` | Get territory by ID |
| `POST` | `/territories` | Create a territory |
| `PUT` | `/territories/{id}` | Update a territory |
| `DELETE` | `/territories/{id}` | Delete a territory |

### Sales Persons / Reps

| Method | Route | Description |
|--------|-------|-------------|
| `GET` | `/sales-persons` | List all sales persons |
| `GET` | `/sales-persons/{id}` | Get sales person by ID |
| `GET` | `/sales-persons/{id}/orders` | Get orders assigned to a sales person |
| `GET` | `/sales-persons/{id}/quota` | Get current sales quota |
| `PUT` | `/sales-persons/{id}/quota` | Update sales quota |

### Credit Cards & Payment

| Method | Route | Description |
|--------|-------|-------------|
| `GET` | `/customers/{id}/payment-methods` | List customer payment methods |
| `POST` | `/customers/{id}/payment-methods` | Add a payment method |
| `DELETE` | `/customers/{id}/payment-methods/{cardId}` | Remove a payment method |

### Special Offers & Discounts

| Method | Route | Description |
|--------|-------|-------------|
| `GET` | `/special-offers` | List active special offers |
| `GET` | `/special-offers/{id}` | Get special offer by ID |
| `POST` | `/special-offers` | Create a special offer |
| `PUT` | `/special-offers/{id}` | Update a special offer |
| `PATCH` | `/special-offers/{id}` | Activate or deactivate an offer |
| `DELETE` | `/special-offers/{id}` | Remove a special offer |

### Health & Diagnostics

| Method | Route | Description |
|--------|-------|-------------|
| `GET` | `/health` | Basic health check |
| `GET` | `/health/ready` | Readiness probe (checks DB + deps) |
| `GET` | `/health/live` | Liveness probe |


## Endpoint to Table Sync (AdventureWorks2022)

| Endpoint Group | Primary Sales Tables | Related Tables (Other Schemas) |
|----------------|----------------------|----------------------------------|
| Orders | `Sales.SalesOrderHeader`, `Sales.SalesOrderDetail`, `Sales.SalesOrderHeaderSalesReason` | `Purchasing.ShipMethod`, `Production.Product`, `Person.Address` |
| Customers | `Sales.Customer`, `Sales.Store` | `Person.Person`, `Person.BusinessEntity`, `Person.BusinessEntityAddress`, `Person.Address`, `Person.StateProvince`, `Person.CountryRegion` |
| Invoices | `Sales.SalesOrderHeader`, `Sales.SalesOrderDetail` | `Sales.Customer`, `Sales.CurrencyRate`, `Person.Address` |
| Territories | `Sales.SalesTerritory`, `Sales.SalesTerritoryHistory` | `Person.CountryRegion` |
| Sales Persons / Quota | `Sales.SalesPerson`, `Sales.SalesPersonQuotaHistory`, `Sales.SalesTerritory` | `HumanResources.Employee`, `Person.Person` |
| Credit Cards & Payment | `Sales.CreditCard`, `Sales.PersonCreditCard`, `Sales.SalesOrderHeader` | `Person.Person` |
| Special Offers | `Sales.SpecialOffer`, `Sales.SpecialOfferProduct`, `Sales.SalesOrderDetail` | `Production.Product` |

## Additional Tables to Include (Cross-Schema)

The Sales schema has `19` tables, but Sales endpoints also depend on these `10` non-Sales tables:

1. `HumanResources.Employee`
2. `Person.Address`
3. `Person.AddressType`
4. `Person.BusinessEntity`
5. `Person.BusinessEntityAddress`
6. `Person.CountryRegion`
7. `Person.Person`
8. `Person.StateProvince`
9. `Production.Product`
10. `Purchasing.ShipMethod`

## Core Sales Relationships

1. Order header/detail: `Sales.SalesOrderHeader (1) -> (many) Sales.SalesOrderDetail`
2. Order to customer: `Sales.SalesOrderHeader.CustomerID -> Sales.Customer.CustomerID`
3. Customer type split:
    - B2C: `Sales.Customer.PersonID -> Person.Person.BusinessEntityID`
    - B2B: `Sales.Customer.StoreID -> Sales.Store.BusinessEntityID`
4. Customer addresses (read model path): `Sales.Customer -> Person.Person/Person.BusinessEntity -> Person.BusinessEntityAddress -> Person.Address -> Person.StateProvince -> Person.CountryRegion`
5. Order shipping/payment links:
    - `Sales.SalesOrderHeader.ShipMethodID -> Purchasing.ShipMethod`
    - `Sales.SalesOrderHeader.BillToAddressID/ShipToAddressID -> Person.Address`
    - `Sales.SalesOrderHeader.CreditCardID -> Sales.CreditCard`
6. Sales person links:
    - `Sales.SalesOrderHeader.SalesPersonID -> Sales.SalesPerson.BusinessEntityID`
    - `Sales.SalesPerson.BusinessEntityID -> HumanResources.Employee.BusinessEntityID`
7. Special offer/product relation: `Sales.SpecialOffer (1) -> (many) Sales.SpecialOfferProduct -> Production.Product`


## Domain-Driven Design: Multi-Table + Multi-Service Interaction

### 1) One Domain Use Case, Multiple Tables (Inside Sales Service)

In DDD, the application handles one use case through an aggregate boundary, but persistence can span multiple tables in a single local transaction.

Example: `POST /orders`

1. Validate command and business rules in the Order aggregate.
2. Write order header: `Sales.SalesOrderHeader`.
3. Write order lines: `Sales.SalesOrderDetail`.
4. Write reason links if provided: `Sales.SalesOrderHeaderSalesReason`.
5. Optionally compute/attach pricing context from `Sales.SpecialOfferProduct` and `Sales.CurrencyRate`.
6. Commit once (single DB transaction).

Rule: keep invariants inside the Sales aggregate transaction; do not include remote service calls in this transaction.

### 2) Domain Events vs Integration Events

- Domain events (inside Sales domain): `OrderCreated`, `OrderSubmitted`, `OrderCancelled`.
- Integration events (across services): `sales.order.created.v1`, `sales.order.submitted.v1`.

### 2.1) Main Tables by Domain and In-Domain Events

The table below focuses on domain events raised inside the Sales service boundary (before mapping to integration events).

| Domain (Sales Service) | Main Tables Involved | Domain Events (Inside Same Domain) |
|------------------------|----------------------|-------------------------------------|
| Orders | `Sales.SalesOrderHeader`, `Sales.SalesOrderDetail`, `Sales.SalesOrderHeaderSalesReason` | `OrderCreated`, `OrderUpdated`, `OrderSubmitted`, `OrderCancelled`, `OrderShipped` |
| Customers | `Sales.Customer`, `Sales.Store` | `CustomerCreated`, `CustomerUpdated`, `CustomerDeactivated` |
| Invoices (Sales view/projection) | `Sales.SalesOrderHeader`, `Sales.SalesOrderDetail`, `Sales.CurrencyRate` | `InvoiceRequested`, `InvoiceGenerated`, `InvoiceVoided` |
| Territories | `Sales.SalesTerritory`, `Sales.SalesTerritoryHistory` | `TerritoryAssigned`, `TerritoryReassigned` |
| Sales Persons / Quota | `Sales.SalesPerson`, `Sales.SalesPersonQuotaHistory`, `Sales.SalesTerritory` | `SalesPersonAssignedToOrder`, `SalesQuotaChanged` |
| Payment Methods | `Sales.CreditCard`, `Sales.PersonCreditCard` | `PaymentMethodAdded`, `PaymentMethodRemoved` |
| Special Offers | `Sales.SpecialOffer`, `Sales.SpecialOfferProduct` | `SpecialOfferCreated`, `SpecialOfferActivated`, `SpecialOfferDeactivated`, `SpecialOfferUpdated` |

Notes:

- Domain events above are internal business events inside Sales bounded context.
- Only after application-layer mapping should they be published as integration events (for example `sales.order.submitted.v1`).

Typical mapping:

1. Aggregate raises a domain event.
2. Application layer maps it to an integration event contract.
3. Contract is stored in an Outbox table in the same transaction as business data.
4. Background publisher sends it to RabbitMQ.
5. Consumer services process it idempotently.

### 3) How Sales API Interacts with Other Services

Use asynchronous events for cross-service workflows and avoid direct synchronous dependency for critical paths.

- Sales -> Inventory/Product service:
    - Publish `sales.order.submitted.v1` with order lines.
    - Product/Inventory reserves stock and emits `inventory.reserved.v1` or `inventory.rejected.v1`.
- Sales -> Billing/Invoice service:
    - Publish `sales.order.shipped.v1`.
    - Billing creates invoice and emits `billing.invoice.created.v1`.
- Sales -> Notification service:
    - Publish `sales.order.status.changed.v1`.
    - Notification sends email/SMS.

Sales service consumes downstream events to update local status fields (for example order state) without owning remote tables.

### 4) Pattern for Consistency Across Services

Use Saga/Process Manager for long-running business transactions:

1. Start saga on `OrderSubmitted`.
2. Wait for `inventory.reserved`.
3. Wait for `payment.authorized`.
4. Mark order confirmed; publish `sales.order.confirmed.v1`.
5. If any step fails, run compensation (`sales.order.cancelled.v1`).

This gives eventual consistency across microservices while preserving strong consistency inside Sales DB transaction boundaries.

### 5) Recommended Ownership Boundary

- Sales service owns: `Sales.*` tables and Sales business invariants.
- Sales service reads reference data from events or replicated read models for external domains (`Product`, `Employee`, shipping metadata).
- Avoid cross-service table joins at runtime; join inside Sales DB only.

### 6) Concrete Event Contract Table

| Event Name | Producer | Consumer(s) | Payload Fields | Idempotency Key |
|------------|----------|-------------|----------------|-----------------|
| `sales.order.created.v1` | Sales API | Billing, Notification, Reporting | `eventId`, `occurredAtUtc`, `orderId`, `orderNumber`, `customerId`, `salesPersonId`, `currencyCode`, `totalDue`, `lineItems[]:{lineId,productId,orderQty,unitPrice,unitPriceDiscount}` | `orderId:eventType:version` |
| `sales.order.submitted.v1` | Sales API | Inventory/Product, Billing, Notification | `eventId`, `occurredAtUtc`, `orderId`, `customerId`, `shipMethodId`, `requestedShipDate`, `lineItems[]:{productId,orderQty}` | `orderId:eventType:version` |
| `inventory.reserved.v1` | Inventory/Product Service | Sales API | `eventId`, `occurredAtUtc`, `orderId`, `reservationId`, `status`, `reservedItems[]:{productId,reservedQty}` | `orderId:reservationId:eventType` |
| `inventory.rejected.v1` | Inventory/Product Service | Sales API, Notification | `eventId`, `occurredAtUtc`, `orderId`, `reasonCode`, `reasonMessage`, `failedItems[]:{productId,requestedQty,availableQty}` | `orderId:reasonCode:eventType` |
| `billing.payment.authorized.v1` | Billing Service | Sales API | `eventId`, `occurredAtUtc`, `orderId`, `paymentId`, `authorizationCode`, `authorizedAmount`, `currencyCode` | `orderId:paymentId:eventType` |
| `billing.payment.failed.v1` | Billing Service | Sales API, Notification | `eventId`, `occurredAtUtc`, `orderId`, `paymentId`, `failureCode`, `failureMessage` | `orderId:paymentId:eventType` |
| `sales.order.confirmed.v1` | Sales API | Notification, Reporting | `eventId`, `occurredAtUtc`, `orderId`, `confirmedAtUtc`, `customerId`, `totalDue` | `orderId:eventType:version` |
| `sales.order.cancelled.v1` | Sales API | Inventory/Product, Billing, Notification, Reporting | `eventId`, `occurredAtUtc`, `orderId`, `cancelledAtUtc`, `cancelReason`, `compensationRequested` | `orderId:eventType:version` |
| `sales.order.shipped.v1` | Sales API | Billing, Notification, Reporting | `eventId`, `occurredAtUtc`, `orderId`, `shipmentId`, `shippedAtUtc`, `carrier`, `trackingNumber` | `orderId:shipmentId:eventType` |
| `billing.invoice.created.v1` | Billing Service | Sales API, Notification, Reporting | `eventId`, `occurredAtUtc`, `orderId`, `invoiceId`, `invoiceNumber`, `invoiceDateUtc`, `invoiceTotal` | `invoiceId:eventType:version` |

Implementation notes:

- Include `eventId` (GUID/ULID) in every event and enforce unique constraint on consumer inbox table by `eventId`.
- Use the idempotency key in producer outbox dedup and consumer inbox dedup.
- Treat contracts as immutable per version (`.v1`); breaking changes require `.v2`.



## Roadmap

- [ ] Add EF Core + SQL Server
- [ ] Implement Orders vertical slice (Create, GetById, List)
- [ ] Implement Customers slice
- [ ] Integrate RabbitMQ publisher
- [ ] Add Redis distributed cache
- [ ] Add health checks
- [ ] Add OpenTelemetry tracing


Group	Methods
Orders	GET, POST, PUT, PATCH, DELETE + actions (submit, cancel, ship)
Line Items	GET, POST, PUT, DELETE
Customers	GET, POST, PUT, PATCH, DELETE
Customer Addresses	GET, POST, PUT, DELETE
Invoices	GET, POST, PATCH, DELETE + PDF download
Territories	GET, POST, PUT, DELETE
Sales Persons / Quota	GET, PUT
Payment Methods	GET, POST, DELETE
Special Offers	GET, POST, PUT, PATCH, DELETE
Health	GET (health, ready, live)

