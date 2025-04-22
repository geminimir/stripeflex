
# Backend – Payments Risk Intelligence Core Service

This directory contains the core logic and services powering the Payments Risk Intelligence platform. It handles webhook ingestion, error classification, retry orchestration, segmentation, and logging of all payment-related events.

---

## 🔧 Responsibilities

- Normalize incoming webhook payloads (e.g., from Stripe)
- Classify payment errors into semantic categories
- Orchestrate idempotent retry flows via state machines
- Dynamically adjust retry logic based on customer segmentation
- Record all events into a PostgreSQL event store
- Trigger email alerts and notifications when applicable

---

## 🧠 Key Concepts

- **Idempotency**: Ensures no double-retries or duplicate logic, even under failure conditions.
- **Error Classification**: Maps raw provider codes into actionable categories (e.g., `HARD_DECLINE`, `RATE_LIMITED`).
- **Segmentation**: Adjusts retry behavior by user value, geography, or historical success rates.
- **Resilient Orchestration**: State-machine based retry pipeline with configurable policies.

---

## 📂 Structure

```text
backend/
├── api/                 # REST + webhook entrypoints
├── classifier/          # Maps raw error codes to categories
├── retry/               # State machine logic for retry flows
├── segmentation/        # Segmentation rules and profiles
├── db/                  # Database models, migrations
├── notifications/       # Email and alert logic
├── types/               # Shared enums and TypeScript types
└── utils/               # Backoff, idempotency helpers
```

---

## 🚀 Running Locally

### Requirements

- Node.js ≥ 18
- PostgreSQL
- Redis

### Commands

```bash
# Install dependencies
npm install

# Run dev server
npm run dev

# Run migrations
npm run migrate

# Run tests
npm run test
```

Environment configuration is managed via `.env`. See `.env.example` for reference.

---

## 🧪 Example Event Flow

1. A `invoice.payment_failed` webhook is received.
2. The classifier tags it as `SOFT_DECLINE`.
3. A retry is scheduled in 3 hours via the retry state machine.
4. All transitions and metadata are stored in the `event_log` table.

---

## 🛠 Extensibility

- Provider-agnostic: just extend the `classifier/` and `ingestion/` logic for new providers (e.g. Adyen, Braintree)
- Retry strategies are fully pluggable
- Notification hooks support custom channels (Slack, SMS, etc.)

---

## 🧠 Dev Notes

- All retry flows are pure functions + state machines → easy to test and reason about
- Designed for observability-first integration with Grafana, Sentry, or custom dashboards

---

## 🔍 Next Steps

- [ ] Add fraud flagging integration
- [ ] Expose retry flows via admin UI
- [ ] Create dry-run simulator for new retry strategies
