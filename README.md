# LeadLock

**Every lead has exactly one accountable owner.**

NaSCon '26 Web Dev Competition · built in one weekend to a 12-page product spec

A multi-tenant CRM for 5–30 person sales teams. Leads arrive from a web form, WhatsApp or manual entry; LeadLock assigns them, starts a timer, and escalates up a configurable chain until someone acts. Nothing is dropped and nothing is double-owned.

## How it works

- **Escalation engine.** Round-robin across *Available* employees under a max-open-leads cap, an acknowledgement window, then next available → backup → manager. Per-workspace advisory lock + version check + deadline re-check in `escalateLead()`, so two expiring timers never escalate the same lead twice.
- **Timers are rows.** A worker in the same Node process ticks every second over Postgres rows, so timers survive restarts. An outbox with retry/backoff delivers every external call.
- **Real integrations.** WhatsApp Cloud API (signature verified on the raw body, idempotent on message id), Stripe (webhooks de-duplicated by event id, collected vs projected revenue), Google Calendar over OAuth 2.0 (busy check, UTC + IANA zones), Slack escalation DMs.
- **Enforced on the server.** Every route goes through one `api(perm, …)` wrapper: permission matrix, field-level value masking, workspace isolation (guessed IDs → 404). Secrets are AES-256-GCM encrypted. A Postgres trigger rejects UPDATE/DELETE on the audit log.

```
src/app/api/**          HTTP: auth, zod validation, permission checks
src/server/leads.ts     assignment, acknowledgement, escalation, recovery
src/server/crm.ts       custom fields, stage-gated required fields, masking, saved filters
src/server/worker.ts    timers + outbox
src/server/integrations/{whatsapp,stripe,google,slack}.ts
```

## Run

```bash
cp .env.example .env        # DATABASE_URL, ENCRYPTION_KEY (openssl rand -hex 32)
npm install
npm run db:migrate && npm run seed && npm run dev
```

Seeds a demo workspace with 300 contacts. Logins `owner@sunrise.demo` / `atta@sunrise.demo` (password `demo12345`); public lead form at `/f/sunrise-solar`.

Spec coverage, item by item: [docs/REQUIREMENTS.md](docs/REQUIREMENTS.md) · free deploy: [docs/DEPLOY.md](docs/DEPLOY.md)

---

Next.js 15 · TypeScript · PostgreSQL (Neon) + Drizzle · Zod · Render
