# Payments Risk Intelligence & Recovery Platform

## Overview

This is a Stripe-inspired Payments Risk Intelligence and Recovery platform designed to minimize revenue leakage caused by failed payments, retries, and infrastructure misconfigurations. It acts as an intelligent layer between payment providers and SaaS platforms, offering real-time classification, retry strategies, and actionable insights for engineering and revenue teams.

- ⚙️ **Tech Stack**: Node.js (TypeScript), Redis, PostgreSQL, Docker
- 🔄 **Core Features**: Idempotent retry flow, failure classification, dynamic segmentation
- 🌐 **Architecture**: Event-driven, state-machine based pipeline
- 🧠 **Use Case**: Reduce the 2–5% of MRR lost due to billing errors and failed charges
- 🎯 **Goal**: Demonstrate domain knowledge in payments, reliability engineering, and backend architecture (targeting Stripe and similar fintech roles)

---

## Why This Exists

Failed payments are a silent killer for SaaS businesses. Up to 5% of recurring revenue can be lost due to poor retry strategies, opaque provider errors, and non-personalized fallback flows. Most companies lack the resources to build robust internal tooling around this.

This platform bridges the gap by:
- Proactively classifying errors and tailoring retry strategies
- Providing a centralized event store and observability layer
- Integrating with any payment provider via standardized webhook ingestion

---

## Architecture

```text
+------------------+       +--------------------+       +----------------+
| Payment Provider | ----> | Webhook Ingestion  | --->  | Classification |
+------------------+       +--------------------+       +----------------+
                                                  \
                                                   \--> Retry Engine
                                                    \--> Notifications
                                                     \--> Event Store (PostgreSQL)
```

- **Webhook Ingestion**: Receives events from Stripe-like providers and normalizes them.
- **Classification Engine**: Groups errors into semantic categories (e.g., "hard decline", "network issue", "configuration error").
- **Retry Engine**: Uses a configurable state machine per customer profile. Supports idempotent retries with backoff strategies.
- **Segmentation Layer**: Dynamically personalizes retries based on customer value, geography, or history.
- **Event Store**: PostgreSQL-backed audit trail and analytics.

---

## Core Concepts

### 💡 Idempotency & Retry Safety
Retries are handled via a deterministic state machine. Each step is idempotent and safely resumable even if execution is interrupted. This is critical to avoid double-charging or mis-reporting in payment systems.

### 🧠 Classification
Errors from the payment provider are parsed and mapped to internal categories:
- `HARD_DECLINE` → do not retry
- `SOFT_DECLINE` → retry with delay
- `CONFIG_ERROR` → flag engineering
- `RATE_LIMITED` → exponential backoff
- `FRAUD_FLAGGED` → report and halt

### 🎯 Segmentation
Not all customers are equal. The retry flow dynamically adjusts for:
- `High-value` users → aggressive retry + email fallback
- `Low-value` users → conservative retries
- `New customers` → first-failure alerting

### 📈 Observability
All events are stored and exposed via a unified logging system for:
- Alerting on spikes in payment failures
- Debugging customer-specific issues
- Visualizing revenue saved

---

## Example Flow

1. Stripe sends a `invoice.payment_failed` webhook.
2. The ingestion endpoint parses it and stores the raw payload.
3. The error code is classified as a `SOFT_DECLINE`.
4. The retry state machine triggers a retry after 3 hours.
5. If that fails, a fallback email is sent and a final retry is scheduled.
6. All actions are logged and visible in the event trail.

---

## Getting Started

### Prerequisites
- Node.js ≥ 18
- Docker + Docker Compose
- PostgreSQL (can use Docker)
- Redis

### Local Setup

```bash
git clone https://github.com/your-username/payments-risk-service.git
cd payments-risk-service

# Start services
docker-compose up

# Run database migrations
npm run migrate

# Start dev server
npm run dev
```

Environment variables are managed via `.env`, see `.env.example`.

---

## Project Structure

```text
src/
├── api/                 # Webhook & REST endpoints
├── classifier/          # Error classification logic
├── retry/               # State machine-based retry engine
├── segmentation/        # Dynamic retry profiles
├── db/                  # PostgreSQL models & migrations
├── notifications/       # Email and alerting integration
├── utils/               # Shared helpers (idempotency, backoff, etc.)
└── types/               # TypeScript types and enums
```

---

## Notable Design Decisions

- **State Machines over Cron Jobs**: More deterministic and observable retries.
- **Event Sourcing**: Enables detailed forensic tracing and analytics.
- **Decoupled Classifier**: Easy to plug in provider-specific logic (e.g., Stripe, Adyen).
- **Segmentation-first Design**: Built to scale for companies with 10k+ customers.

---

## Future Enhancements

- ✅ Stripe Integration (complete)
- ⏳ Add support for Paddle, Adyen, Braintree
- 📊 Admin dashboard for failure analytics
- 📦 Retry strategy templating via JSON config
- 🔐 OAuth & access control for multi-tenant SaaS support
- 🧪 Chaos testing for retry robustness

---

## License

MIT – use and adapt freely.

---

## Contact

If this project interests you, feel free to reach out or connect on [GitHub](https://github.com/geminimir).
