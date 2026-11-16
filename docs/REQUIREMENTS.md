# Requirements map

Where to verify each item of the competition spec (F = functional, I = integrations, Q = quality). Overview in the [README](../README.md).

| ID | Where / how |
|---|---|
| F1.1 | `/signup` creates workspace + Owner; session cookie (httpOnly, 7-day sliding) survives refresh; Log out ends it |
| F1.2 | Team & roles → invite (temp password), levels; Owner row is locked; only Owner edits Admins |
| F1.3 | Team & roles → permission matrix per role (view/create/edit/delete/export, rules, integrations, audit…) |
| F1.4 | Log in as `atta@` (Sales role lacks `crm.view_value`): values show “hidden” on board, contact page, dashboard, CSV export |
| F1.5 | Every API route goes through `api(perm, …)` → 403 JSON. e.g. `PUT /api/rules` as atta → 403 |
| F1.6 | All queries filter by session workspace; guessed IDs from another workspace → 404 |
| F2.1 / F2.8 | Contact page: company, opportunities, leads, WhatsApp conversation, payments, appointments, owner, activity |
| F2.2 / F2.3 | Pipelines & rules → contact fields + per-pipeline opportunity fields (Residential vs Commercial differ) |
| F2.4 | Residential “Proposal” requires Value + System size: move a card without them → blocked with message |
| F2.5 | Pipelines & rules → add / rename / reorder / delete stages (delete with opportunities asks where to move them); Pipeline board |
| F2.6 | Contacts → combine search, tag, stage, owner, source, dates, custom field → “Save list”; lists re-evaluate live |
| F2.7 | Activity history on contact page (notes, stage changes, assignments, edits, messages, payments, appointments) |
| F3.1 | `/f/<slug>` web form, inbound WhatsApp, Leads → manual entry; source badge on every lead |
| F3.2 | Team / My profile → Available, Busy, On leave, Offline |
| F3.3 | Pipelines & rules → round-robin among Available employees under “max open leads” |
| F3.4 | Live countdown on Leads, lead page, contact page; Slack DM + in-app banner on assignment |
| F3.5 | Escalation chain editor: any number of levels (next available / backup / manager) with seconds per level |
| F3.6 | Set someone On leave → unacknowledged leads move instantly; acknowledged stay + flagged in Recovery (policy shown on Team page) |
| F3.7 | Recovery queue with reason badges |
| F3.8 | Per-workspace advisory lock + version check + deadline re-check in `escalateLead()` |
| F3.9 / I4 | Slack DM to the new owner with lead, source, reason and an “Open lead” button; per-user routing by Slack email (My profile); failures retried and listed on Integrations |
| F4.x | Audit log page (filters: type, user, date); Postgres trigger rejects UPDATE/DELETE/TRUNCATE on `audit_logs` |
| F5.x | Dashboard: 8 figures per Section 7 definitions, drill-down links, personal view for Users, customize (saved per user) |
| I1.x | Meta WhatsApp Cloud API webhook `/api/webhooks/whatsapp/<ws>` (X-Hub-Signature-256 verified on raw body, idempotent on message id), sent/delivered/read/failed statuses (never downgraded), outbox retries, retry button, opt-out (incl. customer “STOP”). Twilio is unavailable in Pakistan; the adapter in `whatsapp.ts` is the only provider-specific code |
| I2.x | Payment link from opportunity (optionally sent on WhatsApp); Stripe webhook verified on raw body, de-duplicated by event id; paid / failed / expired / refunded; collected vs projected on Payments page |
| I3.x | My profile → Connect Google (OAuth, offline refresh); busy check + “Proceed anyway”; reschedule/cancel update Google; times stored UTC + IANA zone |
| I5.x | Integrations page: status, last worked, recent errors, limits, sync history with filters; secrets AES-256-GCM encrypted, masked |
| Q1–Q14 | bcrypt(12) passwords; login lockout after 5 failures/15 min; Origin-check CSRF; zod validation; React escaping; optimistic locking (409 on concurrent edit); pagination; loading/success/error on every action; 2.5 s live refresh |
