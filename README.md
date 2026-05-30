# Multi-Agent Orchestration Platform

A platform to **create AI agents**, **configure behavior** (prompts, tools, memory, guardrails), **compose visual workflows** with conditional branching, and **monitor execution** in real time. Agents run on a **LangGraph** runtime with **PostgreSQL + pgvector** memory; humans can interact via **Slack** (Socket Mode).

| | |
|---|---|
| **Author** | Abhranshu ([@jishmisc28](https://github.com/jishmisc28)) |
| **Live demo** | **[multi-agent-orchestration-platform.vercel.app](https://multi-agent-orchestration-platform.vercel.app)** |
| **Walkthrough** | **[Demo video](./demo/demo.mp4)** (3 min — platform tour) |
| **Architecture** | [docs/ARCHITECTURE.md](./docs/ARCHITECTURE.md) |

---

## Repository contents

This public repository focuses on **documentation and design**:

| Document | Description |
|----------|-------------|
| [README.md](./README.md) | Overview, stack, capabilities, quick start |
| [docs/ARCHITECTURE.md](./docs/ARCHITECTURE.md) | Block diagrams, data flows, schema, tradeoffs |
| [docs/DEMO_GUIDE.md](./docs/DEMO_GUIDE.md) | End-to-end walkthrough for the live deployment |
| [docs/DEPLOYMENT.md](./docs/DEPLOYMENT.md) | Free-tier hosting (Vercel, Neon, optional worker) |

Use the **hosted link** above to explore the running application (agents, workflows, monitor, channels).

---

## Architecture (summary)

```
┌─────────────┐     ┌──────────────┐     ┌─────────────────┐
│  Next.js UI │────▶│  REST API    │────▶│  PostgreSQL     │
│  Agents     │     │  /api/*      │     │  + pgvector     │
│  Workflows  │     └──────┬───────┘     │  agents, memory │
│  Monitor    │            │             │  messages, logs │
└─────────────┘            ▼             └─────────────────┘
                    ┌──────────────┐
                    │ BullMQ/Redis │  async workflow jobs
                    └──────┬───────┘
                           ▼
                    ┌──────────────┐
                    │ LangGraph    │  recall → execute → store
                    │ runtime      │  multi-agent graph walker
                    └──────┬───────┘
                           ▲
                    ┌──────┴───────┐
                    │ Slack Bolt   │  Socket Mode
                    └──────────────┘
```

Full diagrams: [docs/ARCHITECTURE.md](./docs/ARCHITECTURE.md)

---

## Stack

| Layer | Technology | Rationale |
|-------|------------|-----------|
| **Runtime** | LangGraph (TypeScript) | Explicit agent graphs, conditions, handoffs |
| **Memory** | PostgreSQL + pgvector (768-dim) | Single store for relational + vector data |
| **Queue** | BullMQ + Redis | Async workflows; inline fallback without Redis |
| **UI** | Next.js 15 | API and dashboard in one codebase |
| **Channel** | Slack Socket Mode | No public webhook required for development |
| **LLM** | Groq (`llama-3.3-70b-versatile`) | Fast inference, generous free tier |
| **Embeddings** | Google Gemini | Vector memory (Groq has no embedding API) |
| **Hosting** | Vercel + Neon | Common free-tier pairing for OSS demos |

---

## Capabilities

| Area | Highlights |
|------|------------|
| **Agents** | CRUD, system prompts, models, tools, channels, memory & guardrails config |
| **Workflows** | React Flow editor, start/agent/condition/end nodes, saved graphs |
| **Templates** | Research & Report, Customer Support Escalation |
| **Runtime** | Real tool execution (search, calculator, file write), inter-agent messages |
| **Monitoring** | Per-run steps, logs, token totals, SSE live updates |
| **Channels** | Slack Concierge, persisted conversation history in UI |

---

## Try the hosted app

1. Open the **live demo URL** (top of this page).
2. **Dashboard** — overview of agents and workflows.
3. **Workflows → Research & Report → Run** — multi-agent pipeline.
4. **Monitor** — inspect steps, handoffs, and token usage.
5. **Channels** — conversation history when Slack is connected.
6. **Agents** — create or edit agents; enable Slack on the Concierge.

Slack live chat requires a **long-running worker** process — see [docs/DEPLOYMENT.md](./docs/DEPLOYMENT.md).

---

## Local development

Typical setup (Node 20+, Neon `DATABASE_URL`, `GROQ_API_KEY`, `GEMINI_API_KEY`):

```bash
npm install
npm run db:migrate
npm run dev          # http://localhost:3000
npm run worker       # BullMQ + Slack Socket Mode
```

Optional local data stores:

```bash
docker compose up -d   # Postgres :5433, Redis :6380
```

Environment variables: see `.env.example` in the application project.

---

## Documentation

| Document | Purpose |
|----------|---------|
| [docs/ARCHITECTURE.md](./docs/ARCHITECTURE.md) | System design, sequences, data model |
| [docs/DEMO_GUIDE.md](./docs/DEMO_GUIDE.md) | Feature walkthrough script |
| [docs/DEPLOYMENT.md](./docs/DEPLOYMENT.md) | Vercel, Neon, Redis, worker hosting |

---

## License

Apache-2.0 — see [LICENSE](./LICENSE).
