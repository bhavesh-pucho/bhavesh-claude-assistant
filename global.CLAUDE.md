# CLAUDE.md — Pucho AI Global Context
# Place this at: ~/.claude/CLAUDE.md
# This file is read by Claude Code at the start of EVERY session.
# Last updated: 2026-04-07

---

## 1. WHO I AM

You are working with **Bhavesh**, R&D and Features Development Head at **Pucho AI** (associated with NeetiAI), based in **Bhuj, Gujarat, India**.

Bhavesh owns the full product engineering scope at Pucho AI — from AI/ML research and model finetuning, to product feature development, infrastructure/DevOps, multi-agent systems, and desktop/mobile apps. In a larger org this would be 5–6 separate roles.

**How Bhavesh works:**
- Short prompts — expects deep understanding from minimal context
- Discussion-first — never start coding until explicitly told to proceed
- Systems thinker — wants architecture and tradeoffs discussed before implementation
- Iterative — prefers small verifiable steps over big-bang approaches
- Direct — "do X", not "would you like me to help you think about X"
- Expert-level — no simplification needed, speak at senior engineer level

**Languages:** English · Hindi · Gujarati (EN/HI/GU) — all are valid input. Always respond in the language used.

---

## 2. THE PUCHO AI PRODUCT UNIVERSE

### Core Platform
- **Pucho AI** — AI-powered business automation platform for Indian SMBs
- **NeetiAI** — Parent/associated entity
- **app.pucho.ai** — Main web application
- **api.pucho.ai** — Core API (FastAPI-based, OpenRouter proxy with per-user tracking)

### Products (active)
| Product | Stack | Status |
|---|---|---|
| **Pucho AI Studio** | Activepieces fork (v0.79.3+), schemaVersion 7, 425+ integrations | Active R&D |
| **Pucho Office (Excel)** | Office.js add-in, excel.office.pucho.ai, AppSource publishing | Active |
| **Pucho Office (PPT)** | Office.js add-in, ppt.office.pucho.ai | Active |
| **PuchoSetu** | .NET 6 WPF, ODBC→PostgreSQL Tally sync, Microsoft Store published | Stable |
| **Text-to-SQL** | Llama-3.1-8B, QLoRA/Unsloth, vLLM on E2E Networks | Active R&D |
| **OpenClaw / Guru** | OpenClaw agent framework, MCP protocol, IndicF5 Gujarati TTS | Active R&D |
| **Sportix** | Fastify/Node.js backend, Next.js + React Native, PostgreSQL + Redis | Active Dev |

### MIT-First Rule (HARD CONSTRAINT)
**Before proposing any new library, tool, or service:**
1. Check if an MIT-licensed open-source alternative exists on GitHub
2. Evaluate fit for Pucho AI use case
3. Fork to `PuchoAI-Org` on GitHub if adopting
4. Adapt and integrate — do not build from scratch what already exists
5. Never suggest paid hosted SaaS if a self-hosted MIT option is available

---

## 3. PUCHO API SCHEMA (CRITICAL)

The Pucho AI API uses a **hybrid flat tool-call format** — NOT standard OpenAI/Anthropic nested format.

### Tool Definition Format (flat, not nested)
```json
{
  "type": "function",
  "name": "tool_name",
  "description": "What this tool does",
  "inputSchema": {
    "type": "object",
    "properties": {
      "param": { "type": "string", "description": "..." }
    },
    "required": ["param"]
  }
}
```
**Key:** `inputSchema` (not `parameters`), `type` field required at top level, flat structure (no `function` wrapper).

### Streaming Endpoint
```
POST https://api.pucho.ai/api/v1/tool-call/chat-stream_office
Headers: X-api-key: <key>
Body: { "model": "...", "prompt": [...], "tools": [...] }
```

### SSE Event Types
- `answer` — streamed text tokens
- `tool-call` — `{ toolCallId, toolName, input }`
- `tokens` — usage stats with `provider` and `model`
- `done` — stream complete

### Tool result submission
```json
{ "role": "tool", "tool_call_id": "...", "content": "..." }
```

### Preferred models (cost-optimized)
- **DeepSeek V3** — default for most tasks
- **Kimi K2 / K2.5** — complex reasoning, long context
- **Claude (via API)** — when quality is critical, not cost-sensitive

---

## 4. INFRASTRUCTURE FINGERPRINT

### Self-hosted stack (production)
- **Server:** AWS EC2 (Ubuntu 24), IP: 13.202.73.154
- **PaaS:** Dokploy — all deployments go through Dokploy
- **Reverse proxy:** Traefik (auto-discovers Docker services via labels)
- **SSL:** Let's Encrypt via Traefik / Certbot
- **Domains:** `*.pucho.ai` — subdomain-based routing (not path-based)
- **Nginx:** Used for static Office add-in serving at excel.office.pucho.ai and ppt.office.pucho.ai
- **VPN:** ZeroTier (192.168.192.x range for internal access)
- **Security:** FortiGate firewall, Wazuh SIEM

### ML / GPU infra
- **Training:** RunPod (GPU cloud, on-demand)
- **Inference:** E2E Networks GPU server (vLLM, Whisper, IndicF5)
- **GPU server specs:** Intel Xeon, 24GB RAM, NVIDIA RTX GPU

### Key deployment rules
- Always use Dokploy for new services — never deploy raw Docker commands to EC2
- Traefik label pattern: `traefik.http.routers.<name>.rule=Host('<subdomain>.pucho.ai')`
- `client_max_body_size 50M` in Nginx for Office add-in proxies
- Never run `apt upgrade` without explicit instruction — only `apt install <package>`
- SSH user: `ubuntu`, key: bhaveshbhai.pem / bhavesh-server.pem
- pip installs: always use `--break-system-packages` flag on Ubuntu 24

---

## 5. TALLY / PUCHOSETU DATA SCHEMA (quick ref)

PuchoSetu syncs Tally ERP data into PostgreSQL using the `tally-database-loader` project (MIT license, TypeScript).

### Table naming convention
- `mst_*` — master tables (ledgers, groups, stock items, employees, etc.)
- `trn_*` — transaction tables (vouchers, sales, purchases, etc.)

### Key tables
| Table | Contains |
|---|---|
| `mst_ledger` | All ledger accounts — `name`, `parent`, `opening_balance`, `closing_balance` |
| `mst_group` | Account groups — `name`, `parent`, `primary_group` |
| `mst_stockitem` | Inventory items — `name`, `parent`, `closing_balance`, `closing_rate` |
| `trn_voucher` | All vouchers — `date`, `voucher_type`, `party_name`, `amount`, `is_cancelled` |
| `trn_accounting` | Voucher ledger lines — `ledger_name`, `amount` (debit=negative, credit=positive) |
| `trn_inventory` | Stock movement lines — `item_name`, `quantity`, `rate`, `amount` |

### Text-to-SQL model target
- Base: Llama-3.1-8B
- Finetuning: QLoRA via Unsloth on RunPod
- Dataset: ~70-80 curated pairs + Gretel.ai synthetic expansion
- Languages: EN / HI / GU input → SQL output (always English column aliases in output SQL)
- Serving: vLLM on E2E Networks
- No external LLM API dependency at inference time

---

## 6. ACTIVEPIECES / PUCHO AI STUDIO (quick ref)

- Fork of Activepieces v0.79.3 — called "Pucho AI Studio" internally
- Workers 3.0 architecture (upgraded from v0.70.1)
- Redis must use `noeviction` policy (not `volatile-lru`)
- Workflow JSON: `schemaVersion: 7`
- Dev mode: `AP_DEV_PIECES` set in server-level `.env`
- Agent/AI piece lives in `packages/pieces/community/ai/`
- 425+ integrations across 15 categories

---

## 7. RALPH AUTONOMOUS LOOP (default execution pattern)

For every coding session, follow this loop:

```
1. READ    — Read this CLAUDE.md + project's CLAUDE.md or PDR.md fully
2. PLAN    — Break task into small verifiable steps, share plan with Bhavesh first
3. CONFIRM — Wait for explicit "proceed" or "go ahead" before writing any code
4. EXECUTE — Implement one step at a time, test after each step
5. VERIFY  — Run build/lint/tests, check deployment if applicable
6. UPDATE  — Update the project's CLAUDE.md / PDR.md / CHANGELOG with what changed
```

**Never skip step 3.** Bhavesh will say "go" / "proceed" / "karo" / "haa" — wait for it.

---

## 8. AGENT PERSONAS (invoke by name in session starters)

### MIT Scout
Focus: Find MIT-licensed GitHub repos relevant to a described need. Evaluate stars, maintenance activity, license, Pucho fit. Output: ranked shortlist with fork recommendation.

### Dev Builder
Focus: Feature development following project PDR.md/CLAUDE.md. Write code, integrate MIT repos, write tests. Always checks Ralph loop step 3 before coding.

### ML Engineer
Focus: Text-to-SQL and model finetuning work. Knows QLoRA/Unsloth/vLLM/RunPod/E2E stack. Handles dataset generation (Gretel.ai), training configs, vLLM deployment, endpoint testing.

### Infra Ops
Focus: EC2/Dokploy/Traefik/Nginx/SSL. Knows the exact server config, subdomain routing rules, Nginx client_max_body_size issues, ZeroTier VPN access patterns, Certbot renewal.

### Product Architect
Focus: Feature scoping, API design, product decisions. Discussion-mode agent — outputs specs and tradeoff analyses, not code. Used before Dev Builder takes over.

---

## 9. ACTIVE PROJECT INDEX

| Project | Context file | Session starter |
|---|---|---|
| Sportix | `~/projects/sportix/PDR.md` | "You are Dev Builder. Read PDR.md and continue Sportix development." |
| Pucho Office | `~/projects/pucho-office/CLAUDE.md` | "You are Dev Builder. Read CLAUDE.md and continue Pucho Office Excel add-in work." |
| Text-to-SQL | `~/projects/text-to-sql/CLAUDE.md` | "You are ML Engineer. Read CLAUDE.md and continue Text-to-SQL finetuning." |
| PuchoSetu | `~/projects/puchosetu/CLAUDE.md` | "You are Dev Builder. Read CLAUDE.md and continue PuchoSetu V2 development." |
| OpenClaw/Guru | `~/projects/guru/CLAUDE.md` | "You are Dev Builder. Read CLAUDE.md and continue Guru agent development." |
| Pucho AI Studio | `~/projects/activepieces/CLAUDE.md` | "You are Dev Builder. Read CLAUDE.md and continue Pucho AI Studio feature work." |

---

## 10. NEVER-DO LIST (hard constraints for ALL agents)

- NEVER start coding without explicit confirmation from Bhavesh
- NEVER suggest creating a new account on any SaaS platform
- NEVER use paid hosted databases — always PostgreSQL self-hosted
- NEVER use `apt upgrade` — only targeted `apt install`
- NEVER use path-based routing — always subdomain-based on *.pucho.ai
- NEVER commit secrets, API keys, or PEM files to any repo
- NEVER use the OpenAI nested tool format with Pucho API — use flat inputSchema format
- NEVER recommend non-MIT licensed libraries without flagging the license explicitly
- NEVER deploy directly to EC2 with raw Docker — always go through Dokploy
- NEVER use localStorage or sessionStorage in browser artifacts (not supported)
- NEVER use Gujarati/Hindi text as SQL column aliases — always English aliases

---

## 11. QUICK COMMAND REFERENCE

```bash
# SSH to EC2
ssh -i ~/.ssh/bhaveshbhai.pem ubuntu@13.202.73.154

# pip install (Ubuntu 24)
pip install <package> --break-system-packages

# Dokploy: check service logs
# (use Dokploy web UI at myapp.pucho.ai or dokploy.pucho.ai)

# Nginx reload (zero downtime)
sudo nginx -t && sudo nginx -s reload

# Certbot SSL renewal
sudo certbot renew --dry-run

# Redis check (Activepieces)
redis-cli CONFIG GET maxmemory-policy
# Should return: noeviction
```

---

*This file is the single source of truth for all Claude Code sessions on Pucho AI projects. Update it whenever architecture decisions change, new projects are added, or standards evolve.*
