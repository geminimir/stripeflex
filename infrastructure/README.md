# Infrastructure – Deployments, Config, and Ops

This directory contains everything related to provisioning, configuration, and running the Payments Risk Intelligence platform in dev, staging, or production environments.

---

## 🏗 Responsibilities

- Define and provision services using Docker and Docker Compose
- Configure PostgreSQL, Redis, and any supporting services
- Provide standardized scripts for local development and CI/CD
- Manage secrets and environment variables via `.env` files or secrets managers

---

## 📦 Stack

- **Docker Compose** for local orchestration
- **PostgreSQL** for event storage
- **Redis** for transient state and job queues
- **Node.js** backend (via sibling `../backend`)
- (Optional) **nginx** for request routing and TLS termination

---

## 📁 Structure

```text
infrastructure/
├── docker-compose.yml       # Full service stack for local dev
├── Dockerfile               # Backend service container
├── postgres/                # Postgres init scripts and config
├── redis/                   # Redis config and docker entry
├── scripts/                 # Dev/test automation helpers
└── .env.example             # Reference environment config
```

---

## 🚀 Quick Start (Local)

```bash
# Start all services
docker-compose up --build

# Run database migrations
docker exec backend-app npm run migrate

# Check health
curl http://localhost:3000/health
```

Ports:
- Backend: `http://localhost:3000`
- Postgres: `localhost:5432`
- Redis: `localhost:6379`

---

## 🌍 Deployment Strategy

> Currently optimized for **local development**. Production readiness involves:

- Moving secrets into a vault (e.g., AWS Secrets Manager)
- Setting up CI/CD pipelines (GitHub Actions, Railway, etc.)
- Adding metrics/observability stack (Prometheus, Grafana)
- Enabling HTTPS & domain management via nginx + certbot

---

## 🔒 Secrets Management

For local: `.env`  
For production: integrate with AWS Parameter Store, Doppler, or Hashicorp Vault

---

## 🧪 Test Utilities

```bash
# Reset DB
./scripts/reset-db.sh

# View logs
docker-compose logs -f backend

# Run test job
docker exec backend-app npm run test
```

---

## ✅ Checklist

- [x] Isolated development stack
- [x] Reproducible database state
- [x] Health check & logging support
- [ ] Production-grade Dockerfile
- [ ] Terraform for cloud infra (future)

---

## 📁 Related

- [`../backend/`](../backend/) – core business logic
- `copilot/` – (future) admin UI for observability and ops