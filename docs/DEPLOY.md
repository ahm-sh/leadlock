# Deploy (free tier)

1. Neon → create project → copy the **pooled** connection string.
2. Render → New → Web Service → this repo. Build `npm ci && npm run build`, start `npm start` (runs migrations first). Env: `DATABASE_URL`, `APP_URL` (the onrender.com URL), `ENCRYPTION_KEY`, `GOOGLE_CLIENT_ID`, `GOOGLE_CLIENT_SECRET`, `NODE_VERSION=22`.
3. `npm run seed` once against the Neon URL (optional demo data).
4. cron-job.org → GET `$APP_URL/api/health` every 5 minutes.
5. In the app → Integrations: connect WhatsApp (Meta), Stripe (webhook endpoint is created automatically) and Slack. Paste the shown WhatsApp Callback URL + Verify token into Meta → WhatsApp → Configuration and subscribe to `messages`. Each employee links Slack on My profile.
6. Google Cloud: OAuth client (Web) with redirect URI `$APP_URL/api/integrations/google/callback`; add demo Gmail accounts as test users.

