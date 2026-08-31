# ENGINEERING.md — Environment & Standards (Ready State)

> Professional software-engineering baseline for this VM. Prepared 2026-08-29.
> Owner: Manus. Lead engineer agent: Genspark Claw (main).

## 1. Machine
- **OS:** Ubuntu 24.04.4 LTS (kernel 6.17 azure) · x86_64
- **CPU:** 4 vCPU — Intel Xeon Platinum 8370C @ 2.80GHz
- **RAM:** 15 GiB (≈14 available) · **Swap:** 8 GiB
- **Disk:** 123 GB root, ~99 GB free

## 2. Toolchain (verified present)
| Category | Tools |
|---|---|
| JS/TS | Node v24.18, npm 11.16 |
| Python | 3.12.3, pip 24 |
| Native | gcc/g++ 13.3, make 4.3 |
| VCS/CI | git 2.43, gh CLI 2.86 |
| Infra | caddy 2.11, pm2, sqlite3 3.45 |
| Search/UX | ripgrep 14, bat 0.24, jq 1.7, yq 4.53, tmux |
| Agents | openclaw 2026.7.1, opencode 1.17.18, gsk 1.7.1 |

**Install-on-demand (absent):** pnpm, yarn, bun, go, rust/cargo, docker, cmake, psql, redis.

## 3. LLM Proxy — Genspark
- **Base:** `https://www.genspark.ai/api/llm_proxy/v1` (OpenAI-compatible, litellm)
- **System key:** in `~/.openclaw/openclaw.json → models.providers.genspark-llm-proxy.apiKey` (permanent). Short-lived `gsk-…` keys pasted in chat expire (~30–40 min).

### Coding models — LIVE (verified 2026-08-29)
| Model | Status | Use |
|---|---|---|
| `claude-opus-5` | ✅ | Primary reasoning/coding |
| `claude-sonnet-5` | ✅ | Fast coding/review |
| `claude-opus-4-8` | ✅ | main agent default |
| `gpt-5.6-sol` | ✅ | Alt engine (GPT "sol") |
| `gpt-5.3-codex` | ✅ | Codex-style coding |
| `claude-haiku-4-5` | ✅ | small/utility |
| `claude-fable-5` | ✅ | Recovered 2026-08-29 05:42 — HTTP 200, reasoning on (1M ctx). Available in opencode. |

### Broken / avoid
| Model | Status |
|---|---|
| `gpt-5-codex` | ❌ 404 (not routed) — use `gpt-5.3-codex` instead. |

> Note: `claude-fable-5` was down earlier (upstream 401) and recovered same day. Genspark fixed the backend key. It is NOT the opencode default (kept `claude-opus-5` as stable default) but is selectable.

## 4. Parallel execution capability — TWO ENGINES READY ✅ (proven 2026-08-29)
Two independent coding engines confirmed running concurrently:

- **Lane A — Claude** (Claude Code, `claude-opus-5`): via `sessions_spawn` runtime=`acp` agentId=`claude`, `mode:"run"`. Allowed by policy (`acp.allowedAgents` includes `claude`).
- **Lane B — Codex** (`gpt-5.3-codex`): via **`codex exec --skip-git-repo-check "<task>"`** run directly (exec tool). Configured at `~/.codex/config.toml` → provider `genspark`, permanent system key. Verified `CODEX_OK`.
  - `codex` is NOT in `acp.allowedAgents` (policy blocks ACP spawn of it) — so drive Codex via `codex exec`, not `sessions_spawn`.
  - Sandbox: bubblewrap 0.9.0 installed (`bwrap`).
- **GPT "sol"** (`gpt-5.6-sol`) available as a 3rd engine via direct proxy call or opencode model select.
- **Lane D — Hermes Agent v0.11.0** (`/usr/local/bin/hermes` → `/opt/hermes-agent`): full standalone tool-calling agent (Nous Research). Configured 2026-08-29 → provider `anthropic`, base `genspark.ai/api/anthropic`, model `claude-opus-4-8`, permanent key in `~/.hermes/.env`. Verified `HERMES_OK` (running Claude Opus 4.8). Drive it via: `hermes chat -Q --yolo --provider anthropic -q "<task>"`. Supports `--worktree` for isolated parallel git worktrees, `acp`, `mcp`, skills, cron.
- Content agents (ACP allowed): `gsk-slides|docs|sheets|website|deep-research|video|audio|podcasts|cross-check|meeting-notes|super-agent`.

**Parallel pattern:** launch Lane A (`sessions_spawn` claude, mode:run) + Lane B (`codex exec` backgrounded) with **non-overlapping write scopes**, then reconcile. Concurrency proven: both lanes returned simultaneously in a live test.

- **opencode** default = `genspark-llm-proxy/claude-opus-5`; `claude-sonnet-5`, `gpt-5.6-sol`, `gpt-5.3-codex` registered/selectable.

### Config files touched (backups kept)
| File | Change | Backup |
|---|---|---|
| `~/.config/opencode/opencode.json` | default off broken `fable-5` → `claude-opus-5`; registered live models | `.bak.<ts>` |
| `~/.codex/config.toml` | model `gpt-5-codex`(404) → `gpt-5.3-codex`; expired key → system key | `.bak.<ts>` |

## 5. Services (running)
| Service | Port | Notes |
|---|---|---|
| OpenClaw Gateway | 18789 (loopback) → 443 Caddy | agent gateway |
| Chromium (CDP) | 9222 | browser tool; start via `systemctl --user start openclaw-browser` |
| noVNC | 6080 → 8443 | remote desktop viewer |

## 6. Quality standards (mandatory per project)
Every project MUST keep these green before commit:
1. **Type safety** — `tsc --noEmit` (strict, noUnusedLocals/Params, noImplicitReturns)
2. **Lint** — `eslint . --max-warnings=0`
3. **Unit tests** — `vitest run`
4. **Build** — `next build` (or project build)
5. **E2E + a11y** — `playwright test` incl. `@a11y` (axe-core) where applicable
6. **Security gate** — no secrets in tracked files; `.env*` git-ignored; scan for `gsk-eyJ`/keys before commit
7. **Aggregate** — a single `check:all` script that runs the above

## 7. Git / workflow
- Every project is a git repo (init done for existing ones).
- Conventional Commits (`feat: / fix: / chore: / docs: / refactor: / test:`).
- Small, reviewable commits; baseline snapshot first.
- `.gitignore` must cover: node_modules, build output, tsbuildinfo, .env*, coverage, test artifacts.

## 8. Projects
| Project | Path | Stack | State |
|---|---|---|---|
| osoul-aldiafa-v2 | `projects/osoul-aldiafa-v2` | Next.js 16 · React 19 · TS 5.5 · Tailwind 3.4 | git-initialized, all gates green (typecheck/lint/tests) |

---
_Update this file whenever the environment, models, or standards change._
