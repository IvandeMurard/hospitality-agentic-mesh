<h1 align="center">Hospitality Agentic Mesh</h1>
<p align="center">
  <em>Specialized AI agents for hotel operations, communicating via MCP — each owning one domain, none replacing the humans who run the property.</em>
</p>

<p align="center">
  <a href="https://ivandemurard.com/aetherix">Case study</a> ·
  <a href="https://github.com/IvandeMurard/tacet-app">Tacet (public repo)</a> ·
  <a href="https://www.linkedin.com/in/ivandemurard/">Contact</a>
</p>

---

## What this is

This is the **public meta-repo** of a multi-repo system I've been building solo since late 2025: a network of specialized AI agents ("nodes") for hotel operations. Each node owns a strict bounded context, exposes its capabilities as [MCP](https://modelcontextprotocol.io) tools, and **recommends rather than acts** — a human manager validates every decision.

The main execution node (Aetherix) lives in a **private repo** — it targets a real-property pilot and carries security posture, eval baselines and PMS integration details that don't belong in public. This repo documents the architecture, the engineering practices, and links to everything that *is* public. Deeper access (code walkthrough, live demo) available on request.

## The Mesh

```mermaid
graph LR
    subgraph Nodes["Execution nodes (bounded contexts)"]
        A["Aetherix<br/>F&B: covers forecast,<br/>staffing, daily receipts"]
        T["Tacet<br/>Environment: noise, events,<br/>construction risk"]
        AN["Anima<br/>Guest cognition<br/>(design stage)"]
    end
    O["Orchestrator<br/>(the only node<br/>with decision logic)"]
    PMS["PMS / POS<br/>(Apaleo, Mews)"]
    M["Hotel manager<br/>(WhatsApp — validates everything)"]

    O <-->|MCP tools| A
    O <-->|MCP tools| T
    O -.->|planned| AN
    PMS --> A
    A -->|recommendations| M
    M -->|feedback| A
```

**Design principles** — the parts I'd defend in any architecture review:

- **Execution nodes never orchestrate.** Aetherix doesn't know when to run; an orchestrator decides. Perception signals and decision logic are separated by contract.
- **Push-first, UI-less.** The manager gets a daily WhatsApp "receipt" — forecast, staffing recommendation, reasoning. One tap to accept or reject. That reply is training signal.
- **Human-in-the-loop is structural, not a toggle.** No auto-actions. Every guardrail trip is logged with a typed reason.
- **Glue, not replacement.** PMS-agnostic canonical schema behind adapters; intelligence delivered inside tools hotels already use.

## What's built vs. what's vision

Honest inventory — this is a solo project and the mesh narrative is a north star, not a shipped platform.

| Component | Status | Evidence |
|---|---|---|
| **Aetherix** — F&B execution node | **Built** (private): ~16.5k LOC app / ~18.6k LOC tests, staging live on Fly.io, 11 ADRs | Case study; walkthrough on request |
| Forecast pipeline (Prophet + weather/events/occupancy regressors) | Built; **real-data benchmark in progress** (synthetic MAPE retired as circular — see below) | Benchmark notebook will land here |
| Daily Receipts over WhatsApp (Twilio) + inbound feedback parsing | Built, on staging | Demo video planned |
| MCP server (atomic tools) | Built (v0) | |
| Eval-by-design: **blocking CI gate** on forecast eval, deterministic Prophet-in-CI | Built — 3 identical CI runs, 0.00pp variance before flipping the gate to blocking | Write-up planned |
| Runtime guardrails: dead-man's switch, error-rate alerting, feedback→training bounds, tiered-confidence circuit breaker, output sanity | Built | |
| **Tacet** — environmental risk node | **Prototype** (~1.4k LOC, public): polar acoustic heatmap, shielding from building footprints | [tacet-app](https://github.com/IvandeMurard/tacet-app) |
| **Orchestrator** | Proto-stub only | |
| **Anima** — guest cognition node | Design documents only | |
| Federated multi-property memory ("Hive") | Designed, not shipped | |

**Why the synthetic MAPE was retired:** the original demo benchmark (15.09% MAPE) generated synthetic covers *from the same regressors Prophet consumes* — the model was recovering its own generative formula. The current 90-day plan replaces it with backtests on real public data (Kaggle Recruit Restaurant dataset) against a naive same-weekday baseline, plus a shadow-mode backtest on a real restaurant's POS export.

## Engineering practices I'd bring to a team

- **Evals as merge gates, not dashboards** — golden dataset + offline gate in CI (exit codes block merge), separated by contract from runtime guardrails.
- **Typed failure reasons** — every guardrail trip carries a machine-readable reason; "it degraded gracefully" is verifiable, not folklore.
- **ADR discipline** — 31 indexed architectural decisions over 8 months, each with context and rollback notes.
- **Incident response, practiced** — a real leaked-secrets incident (production `.env` committed) handled end-to-end: history rewrite, 11/11 credential rotation, GitHub Support purge, post-mortem. The post-mortem is the LinkedIn post I'm most proud of.
- **Tests outweigh code** — 1.13:1 test-to-app LOC ratio on the main node.

## Current focus (90-day plan, started July 2026)

1. **Proof:** real-data forecast benchmark · closed-loop demo on Apaleo sandbox (forecast → recommendation → feedback → recalibration) · observability (Logfire traces, LLM cost per recommendation) · F&B manager interviews.
2. **Visibility:** this repo · technical write-up on the blocking eval gate · demo video.

## Stack

FastAPI · Python (async, Pydantic v2) · Supabase Postgres + pgvector (HNSW) · Prophet · Claude (multi-LLM provider abstraction) · Mistral embeddings · Redis (Upstash) · Twilio WhatsApp · Apaleo PMS (OAuth2) · Fly.io · GitHub Actions (CI + eval gate + schema-drift gate)

## License

MIT — see [LICENSE](LICENSE). The private node repos carry their own terms.
