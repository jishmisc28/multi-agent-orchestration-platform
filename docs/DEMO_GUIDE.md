# Demo Guide

A **5–8 minute** walkthrough of the hosted Multi-Agent Orchestration Platform.

Replace `YOUR_APP_URL` with your deployed Vercel URL.

---

## 1. Dashboard (30s)

Open **YOUR_APP_URL**.

- Show agent count and workflow count.
- Mention stack: LangGraph runtime, Postgres memory, Slack channel.

---

## 2. Agents (1 min)

Go to **Agents**.

- Open **Slack Concierge** — role, system prompt, tools, Slack enabled.
- Open **Researcher** / **Writer** — different prompts and tools.
- Optional: create a small test agent (name + prompt) to show CRUD.

---

## 3. Workflows (2 min)

Go to **Workflows**.

- Open **Research & Report** — visual graph with connected nodes (Researcher → condition → Writer → End).
- Click **Edit workflow** briefly — blue connection handles, add/delete nodes (don’t save random changes unless intended).
- **Run** the workflow with topic e.g. `AI agent orchestration trends`.
- Copy the run ID or use the redirect to Monitor.

---

## 4. Live Monitor (2 min)

Open the run in **Monitor**.

- Watch steps appear (or refresh if SSE/redis not wired on host).
- Point out **inter-agent messages** and **token totals** per step.
- Note status: running → completed.

---

## 5. Channels (1 min)

Go to **Channels**.

- Show persisted Slack thread (if worker was running during Slack tests).
- Open a thread — user vs assistant messages with timestamps.

*If empty:* explain Slack requires the worker process (see DEPLOYMENT.md).

---

## 6. Slack (optional, 1 min)

In Slack, DM the bot:

- `hello` — concierge chat reply.
- `run workflow market trends in AI` — workflow started; link to monitor.

---

## 7. Wrap-up (30s)

- Configurable agents + visual workflows + real tools + async execution + monitoring.
- Architecture details: [ARCHITECTURE.md](./ARCHITECTURE.md).

---

## Recording tips

- 1080p browser window, dark theme already on brand.
- Pre-run one workflow so Monitor has a completed example as backup.
- Keep API keys off screen.
