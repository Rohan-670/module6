# CommerceSphere — E-commerce Backend

A portfolio-ready, multi-tier Spring Boot REST API demonstrating product catalogue management, customer records, and stock-aware order processing backed by relational tables.

## Stack

- Java 17+
- Spring Boot 3.5.14, Spring Web, Spring Data JPA, Bean Validation
- MySQL 8 (Docker profile) and H2 (zero-setup local demo)
- Springdoc OpenAPI / Swagger UI
- Maven, Docker Compose, Postman, JUnit + MockMvc

## Features

- CRUD categories, products, and customers
- Place orders using product IDs and quantities
- Inventory decrement is transactional; product rows are pessimistically locked while creating orders to reduce overselling risk
- Cancel orders and return stock
- Order-line unit prices are snapshots, so later product price changes do not change historical order totals
- Validation, duplicate email/SKU/category-name checks, consistent JSON error responses
- OpenAPI docs, health endpoint, seed data for H2 quick start, Docker/MySQL profile, tests, CI workflow

> **Scope note:** This is an educational backend starter, not a production-ready commerce platform. It intentionally does not implement login/authentication, payment processing, shipping, tax, refunds, rate limiting, or an immutable financial ledger. Add these before exposing it to real customers. The MySQL profile uses Hibernate `ddl-auto: update` for development convenience; production should use versioned migrations and reviewed backups.

## Start locally (H2, easiest)

Requirements: JDK 17 or later and Maven 3.9+.

```bash
mvn clean test
mvn spring-boot:run
```

The app listens at `http://localhost:8080`. H2 in-memory database and sample categories/products are created automatically. Data is lost when the process stops.

- Swagger UI: http://localhost:8080/swagger-ui.html
- OpenAPI JSON: http://localhost:8080/v3/api-docs
- Health: http://localhost:8080/api/v1/health
- H2 Console (demo only): http://localhost:8080/h2-console (JDBC URL `jdbc:h2:mem:commercesphere`, user `sa`, blank password)

## Run with Docker Compose + MySQL

Requirements: Docker with Compose v2.

```bash
cp .env.example .env
# Change MYSQL_PASSWORD and DB_PASSWORD in .env for local use.
docker compose up --build
```

The compose file points the app to the MySQL service using the `mysql` Spring profile. Use only throwaway local credentials for this demonstration. The database volume persists data across container restarts.

## Run tests

```bash
mvn test
```

A GitHub Actions workflow runs tests on pushes and pull requests.

## API overview

| Method | Endpoint | Purpose |
|---|---|---|
| GET | `/api/v1/health` | Liveness-like application status |
| GET/POST | `/api/v1/categories` | List/create categories |
| GET/PUT/DELETE | `/api/v1/categories/{id}` | Read/update/delete category |
| GET/POST | `/api/v1/products` | List (optional `?categoryId=`) / create products |
| GET/PUT/DELETE | `/api/v1/products/{id}` | Read/update/delete product |
| GET/POST | `/api/v1/customers` | List/register customers |
| GET/PUT/DELETE | `/api/v1/customers/{id}` | Read/update/delete customer |
| GET/POST | `/api/v1/orders` | List/place orders |
| GET | `/api/v1/orders/{id}` | Read order with line items |
| POST | `/api/v1/orders/{id}/cancel` | Cancel order and restore inventory |

For request/response examples, import `postman/CommerceSphere.postman_collection.json` into Postman. The collection uses `baseUrl` and sets IDs as earlier requests run.

## Repository map

```text
src/main/java/com/acme/commercesphere/
  controller/   HTTP endpoints only
  service/      business rules + transactions
  repository/   Spring Data persistence interfaces
  entity/       JPA relational model
  dto/          request/response contracts
  exception/    consistent API error handling
  config/       OpenAPI and demo seed data
src/main/resources/   local + MySQL configuration
src/test/java/        integration tests
 database/             reference SQL schema
 docs/                 architecture, database, build, walkthrough
 postman/              ready-to-import request collection
```

## Walkthrough demo video

A narrated, illustrated 2-minute walkthrough is included at [`demo/CommerceSphere-Walkthrough.mp4`](demo/CommerceSphere-Walkthrough.mp4), with the narration in [`demo/narration.txt`](demo/narration.txt). It uses designed mock interface/API frames and sample payloads; it is **not a live screen recording** of a running server. Follow `docs/demo-script.md` to record a live execution for a submission that requires runtime proof.

## Docs

- [Architecture](docs/architecture.md)
- [Database design](docs/database-design.md)
- [Build and verification](docs/build-and-test.md)
- [Example API flow](docs/api-examples.md)
- [Walkthrough narration and shot list](docs/demo-script.md)

## GitHub repository

This folder is a Git-ready project. To publish it, create an empty GitHub repository named `commercesphere-ecommerce-backend`, then run:

```bash
git init
git add .
git commit -m "Initial CommerceSphere e-commerce backend"
git branch -M main
git remote add origin https://github.com/YOUR_GITHUB_USERNAME/commercesphere-ecommerce-backend.git
git push -u origin main
```

Do not commit `.env`, credentials, or generated database files. A GitHub connector is required to create/push a repository directly from this session; the source archive and commands are included regardless.
