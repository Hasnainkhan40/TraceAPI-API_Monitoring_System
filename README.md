<div align="center">

# 📡 Scalable API Monitoring System

### Production-Grade Backend — Built from Scratch

*Node.js · RabbitMQ · MongoDB · PostgreSQL · Docker*

<br/>

[![Node.js](https://img.shields.io/badge/Node.js-Express-339933?style=flat-square&logo=node.js&logoColor=white)](https://nodejs.org)
[![RabbitMQ](https://img.shields.io/badge/RabbitMQ-Queue%20%2B%20DLQ-FF6600?style=flat-square&logo=rabbitmq&logoColor=white)](https://www.rabbitmq.com)
[![MongoDB](https://img.shields.io/badge/MongoDB-Raw%20Events-47A248?style=flat-square&logo=mongodb&logoColor=white)](https://www.mongodb.com)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-Metrics-4169E1?style=flat-square&logo=postgresql&logoColor=white)](https://www.postgresql.org)
[![Docker](https://img.shields.io/badge/Docker-Containerized-2496ED?style=flat-square&logo=docker&logoColor=white)](https://www.docker.com)
[![License](https://img.shields.io/badge/License-MIT-22c55e?style=flat-square)](LICENSE)

<br/>

> A real-world, production-grade API monitoring system inspired by how modern backend platforms track and analyze **millions of API requests in real time** — featuring event-driven architecture, async processing, dual-database strategy, and multi-tenant design.

</div>

---

## 📌 Overview

This project is a **complete API Monitoring System** built from scratch — designed to show how large-scale backend platforms handle:

- ⚡ High-throughput event ingestion
- 🔄 Asynchronous, event-driven processing
- 🗄️ Dual-database strategy for raw events & aggregated metrics
- 🧱 Modular Monolith architecture
- 🏢 Multi-tenant isolation with API key-based ingestion

This is not a tutorial project. It's a **production-ready system design** you can study, extend, and deploy.

---

## 🧠 System Flow

```
Client
  │
  ▼
API Server (Express + ES Modules)
  │
  ▼
RabbitMQ Queue  ──► Dead Letter Queue (DLQ)
  │                        │
  ▼                        ▼
Consumer Worker        Retry Handler
  │
  ├──► MongoDB          (Raw Events — full detail, time-series)
  │
  └──► PostgreSQL       (Aggregated Metrics — time-bucket analytics)
              │
              ▼
        Analytics API
              │
              ▼
          Dashboard
```

**The full journey of every API request:**

`Ingest` → `Queue` → `Consumer` → `Database` → `Analytics` → `Dashboard`

---

## 🎯 What You'll Learn

### System Design & Architecture
- How **API Monitoring Systems** work in real production environments
- Designing a **Scalable Event-Driven Architecture** from scratch
- **Modular Monolith** architecture pattern — clean, scalable, maintainable
- **Multi-tenant design** & data isolation strategies
- High-level system design for scalable analytics platforms

### Message Queue & Async Processing
- Asynchronous processing using **RabbitMQ** (Queue + Dead Letter Queue)
- **Background Consumer / Worker** pattern for non-blocking ingestion
- Retry handling and failure recovery with DLQ
- Role of RabbitMQ in scalability & system reliability

### Database Strategy
- **Dual-database approach** — when and why to use two databases
- **MongoDB** for raw event storage — flexible schema, fast writes
- **PostgreSQL** with time-bucket aggregation for metrics & analytics
- How raw events become aggregated, queryable metrics

### Production Patterns
- API key-based ingestion for multi-tenant systems
- End-to-end system flow for high-throughput backends
- Real production backend design patterns used at scale

---

## 🛠️ Tech Stack

| Technology | Role |
|------------|------|
| **Node.js** (Express, ES Modules) | API Server & ingestion layer |
| **RabbitMQ** | Message Queue + Dead Letter Queue (DLQ) |
| **MongoDB** | Raw event storage (flexible, high-write) |
| **PostgreSQL** | Time-bucket aggregated metrics |
| **Docker** | Containerized deployment of all services |
| **Event-Driven Architecture** | Decoupled, async system design |
| **Background Consumer Pattern** | Worker processing outside request cycle |

---

## 🗂️ Project Structure

```
api-monitoring-system/
├── src/
│   ├── ingest/                  # API ingestion layer
│   │   ├── routes/              # Ingest endpoints
│   │   ├── middleware/          # API key auth, tenant resolution
│   │   └── publisher.js         # Publishes events to RabbitMQ
│   │
│   ├── consumer/                # Background worker
│   │   ├── worker.js            # RabbitMQ consumer entry point
│   │   ├── processor.js         # Event processing logic
│   │   └── dlq.handler.js       # Dead Letter Queue retry handler
│   │
│   ├── storage/
│   │   ├── mongo/               # MongoDB raw event models & writes
│   │   └── postgres/            # PostgreSQL schema & metric upserts
│   │
│   ├── analytics/               # Analytics API layer
│   │   ├── routes/              # Query endpoints (latency, errors, RPM)
│   │   └── aggregator.js        # Time-bucket query builder
│   │
│   ├── config/                  # DB connections, RabbitMQ config
│   └── app.js                   # Express app entry point
│
├── docker-compose.yml           # RabbitMQ + MongoDB + PostgreSQL
├── .env.example
└── package.json
```

---

## 🚀 Getting Started

### Prerequisites

- **Node.js** v18+
- **Docker** & Docker Compose

### Installation

```bash
# 1. Clone the repository
git clone https://github.com/your-username/api-monitoring-system.git
cd api-monitoring-system

# 2. Install dependencies
npm install

# 3. Configure environment variables
cp .env.example .env
```

### Environment Variables

```env
# Server
PORT=3000
NODE_ENV=development

# RabbitMQ
RABBITMQ_URL=amqp://localhost:5672
QUEUE_NAME=api_events
DLQ_NAME=api_events_dlq

# MongoDB
MONGO_URI=mongodb://localhost:27017/api_monitoring

# PostgreSQL
POSTGRES_HOST=localhost
POSTGRES_PORT=5432
POSTGRES_DB=api_metrics
POSTGRES_USER=postgres
POSTGRES_PASSWORD=your_password
```

### Run with Docker

```bash
# Start all infrastructure (RabbitMQ, MongoDB, PostgreSQL)
docker-compose up -d

# Start the API server
npm run dev

# Start the background consumer worker (separate terminal)
npm run worker
```

---

## 📡 API Reference

### Ingest an API Event

```http
POST /ingest
x-api-key: your_tenant_api_key
Content-Type: application/json

{
  "endpoint": "/api/users",
  "method": "GET",
  "status_code": 200,
  "latency_ms": 142,
  "timestamp": "2025-01-15T10:30:00Z"
}
```

### Query Analytics

```http
# Requests per minute
GET /analytics/rpm?tenant=your_tenant&from=2025-01-15T00:00:00Z

# Error rate by endpoint
GET /analytics/errors?tenant=your_tenant&endpoint=/api/users

# Latency percentiles
GET /analytics/latency?tenant=your_tenant&p=95
```

---

## 🔄 How It Works — Step by Step

**1. Ingest**
Client sends API event data to the ingest endpoint with their API key. The request is validated, tenant is resolved, and the event is immediately published to RabbitMQ.

**2. Queue**
RabbitMQ holds the event in the queue. If processing fails, the event is routed to the **Dead Letter Queue (DLQ)** for retry — ensuring zero data loss.

**3. Consumer Worker**
A background worker continuously polls RabbitMQ. For each event it:
- Writes the **full raw event** to MongoDB (fast, flexible writes)
- **Upserts aggregated metrics** into PostgreSQL time-buckets (for analytics)

**4. Analytics API**
The analytics layer queries PostgreSQL for pre-aggregated metrics — returning fast, efficient results for dashboards: requests per minute, error rates, latency percentiles, status code breakdowns.

---

## 🏢 Multi-Tenant Design

Every API request is scoped to a **tenant** via API key authentication:

- Each tenant's raw events are tagged in MongoDB with their `tenant_id`
- PostgreSQL metrics are partitioned by `tenant_id` for isolated analytics
- API keys are validated at the ingest layer — unauthorized requests are rejected immediately
- Tenants can only query their own data

---

## 🐳 Docker Services

```yaml
services:
  rabbitmq:    # Message broker — ports 5672, 15672 (management UI)
  mongodb:     # Raw event storage — port 27017
  postgres:    # Metrics database — port 5432
```

Access the **RabbitMQ Management UI** at `http://localhost:15672`
Default credentials: `guest / guest`

---

## 🤝 Contributing

1. Fork the repository
2. Create your branch: `git checkout -b feature/your-feature`
3. Commit your changes: `git commit -m 'Add your feature'`
4. Push to the branch: `git push origin feature/your-feature`
5. Open a **Pull Request**

---

## 📄 License

Licensed under the **MIT License** — see [LICENSE](LICENSE) for details.

---

<div align="center">
<br/>

**⭐ Found this useful? Give it a star — it helps others find it too!**

<br/>

Built to teach real-world backend system design · Made with ❤️

</div>
