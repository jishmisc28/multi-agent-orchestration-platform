# Deployment Guide

Run the platform on **free tiers** suitable for a public demo.

| Component | Recommended | Free tier |
|-----------|-------------|-----------|
| Web UI + API | [Vercel](https://vercel.com) | Hobby |
| Database | [Neon](https://neon.tech) | Free Postgres + pgvector |
| Redis (optional) | [Upstash](https://upstash.com) | Free Redis |
| Worker (Slack + queue) | [Railway](https://railway.app) or [Render](https://render.com) | Free / trial |

---

## 1. Neon database

1. Create a Neon project and database.  
2. Enable **pgvector** (Neon supports it on Postgres 16).  
3. Copy connection string → `DATABASE_URL` (include `?sslmode=require`).  
4. Run migrations once from your machine:

```bash
DATABASE_URL="postgresql://..." npm run db:migrate
```

---

## 2. Vercel (Next.js app)

1. Import the **application** repository (private or separate from this docs repo).  
2. Framework preset: **Next.js**.  
3. Environment variables:

| Variable | Required |
|----------|----------|
| `DATABASE_URL` | Yes |
| `GROQ_API_KEY` | Yes |
| `GROQ_MODEL` | `llama-3.3-70b-versatile` |
| `GEMINI_API_KEY` | Yes |
| `EMBEDDING_PROVIDER` | `gemini` |
| `NEXT_PUBLIC_APP_URL` | `https://your-app.vercel.app` |
| `REDIS_URL` | Optional (Upstash URL) |
| `SLACK_*` | Optional |

4. Deploy → copy production URL into `submission/README.md`.

**Note:** Vercel serverless functions are not suitable for the Slack Socket Mode worker (long-lived connection). Run the worker elsewhere.

---

## 3. Upstash Redis (optional)

1. Create a Redis database on Upstash.  
2. Set `REDIS_URL` on Vercel and on the worker.  
3. Enables queued workflows and live SSE across processes.

Without Redis, workflows still run **inline** on the API instance.

---

## 4. Worker (Slack + BullMQ)

Deploy `npm run worker` as a **long-running service**:

### Railway (example)

1. New project → deploy from repo.  
2. Start command: `npm run worker`  
3. Same env vars as Vercel (`DATABASE_URL`, `GROQ_*`, `GEMINI_*`, `SLACK_*`, `REDIS_URL`, `NEXT_PUBLIC_APP_URL`).  
4. No HTTP port required for Socket Mode.

### Render (example)

- **Background Worker** service type.  
- Build: `npm install && npm run build` (or install only if worker uses tsx).  
- Start: `npm run worker`

---

## 5. Slack app checklist

1. **Socket Mode** enabled; `SLACK_APP_TOKEN` (`xapp-`).  
2. **Bot token** `SLACK_BOT_TOKEN` (`xoxb-`).  
3. Scopes: `chat:write`, `im:history`, `im:write`, `app_mentions:read`.  
4. **App Home → Messages tab** — allow users to send messages.  
5. Event: `message.im` (and `app_mention` if using channels).  
6. Reinstall app to workspace after changes.

---

## 6. Post-deploy smoke test

```bash
curl -s https://your-app.vercel.app/api/agents | head
```

In the UI:

1. Run **Research & Report**.  
2. Open **Monitor** for that run.  
3. DM Slack bot (if worker is up).

---

## 7. Updating the public docs repo

This folder (`submission/`) is pushed to [multi-agent-orchestration-platform](https://github.com/jishmisc28/multi-agent-orchestration-platform). After deploy:

1. Set live URL in `submission/README.md`.  
2. Run `./submission/push-to-github.sh` from the project root.

---

## Cost summary

Typical demo stays within **$0/month** on Vercel + Neon free tiers; worker on Railway/Render free credits. Watch Groq/Gemini API usage limits.
