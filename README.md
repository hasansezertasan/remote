# remote

Offloading AI coding agents from my MacBook to remote machines so the laptop
stays cool, meetings stay smooth, and agents keep running when the lid closes.

## The problem

I run multiple AI coding agents (Claude Code, OpenCode, Codex, Agy, etc.)
daily through [Orca](https://www.onorca.dev) — I barely open my code editor
anymore. On macOS, these agents are resource hogs: `syspolicyd` burns CPU on
every process spawn, fans spin up, the machine thermal-throttles, and
everything slows down — meetings, browsing, everything.

Specific pain points:

- **Heat.** Summer + sustained agent workloads = the MacBook is burning. Thermal
  throttling silently kills both agent and human productivity.
- **`syspolicyd`.** macOS Gatekeeper assesses every binary agents spawn.
  Subprocess-heavy agent workloads make this a real bottleneck on Apple Silicon.
- **Connectivity.** Cellular in Turkey is unreliable. Agents crash mid-task when
  the connection to the LLM API drops. No way to resume.
- **Lid close = agent death.** Close the laptop, agent stops. Travel, commute,
  or just moving between rooms kills running work.
- **Resource contention.** Agents competing with video calls, browser tabs, and
  OS background work on the same machine.

## What I already have

Two repos for the local/spare-Mac side of this:

- **[homelab](https://github.com/hasansezertasan/homelab)** — One-shot
  bootstrap that turns a clean Apple Silicon Mac into a personal home server:
  Tailscale (mesh VPN), RustDesk (remote desktop), Hermes (AI agent with cron),
  OpenCode + OpenChamber (headless coding agent + web UI), and optionally Orca
  (`orca serve` on the tailnet). Includes macOS debloating, headless mode, and
  launchd service management.

- **[codex-controls-mac](https://github.com/hasansezertasan/codex-controls-mac)**
  — Step-by-step guide (port of
  [ykdojo/claude-controls-mac](https://github.com/ykdojo/claude-controls-mac))
  for turning a spare Mac into an always-on Codex machine with SSH, computer
  use, clipboard sync, phone control via ChatGPT Remote, and Tailscale for
  anywhere access.

Both work well for a spare Mac on the local network. This repo is about the
**cloud** side — what happens when you don't have a spare Mac, or you want
agents running on beefy remote compute, or you want them to survive any
network/power/travel disruption.

## What changed

I switched from [superset.sh](https://superset.sh) to
[Orca](https://www.onorca.dev), which supports multiple deployment modes:

| Mode | How it works |
| --- | --- |
| **Local** | Agents + UI on your laptop (the default) |
| **SSH targets** | Laptop UI drives agents on a remote host over SSH |
| **Remote Orca Server** | Persistent Orca runtime on a machine you control; multiple clients (laptop, web, mobile) pair to it; agents keep running when you disconnect |
| **Cloud VM** | Per-workspace disposable environments via `orca.yaml` recipes (Vercel Sandbox, Fly, Modal, Docker) |

See: [Ways to Run](https://www.onorca.dev/docs/ways-to-run),
[SSH](https://www.onorca.dev/docs/ssh),
[Remote Servers](https://www.onorca.dev/docs/remote-servers).

I also use:

- **[Hermes](https://github.com/NousResearch/hermes-agent)** — AI agent with
  web dashboard, desktop app, terminal chat, and a cron scheduler. Currently
  runs on my main machine. Cron jobs are the one feature that genuinely wants
  persistent remote compute — they shouldn't die when the laptop sleeps.

Want to use:

- **[Optio](https://github.com/jonwiggins/optio)** — Workflow orchestration for
  AI coding agents. Drives a ticket from creation through merged PR, watches CI,
  feeds failures back to the agent, resumes on conflicts or review feedback. Runs
  as a Helm chart (Next.js dashboard, Fastify API, BullMQ workers, Postgres,
  Redis). Deferred in homelab because it needs Kubernetes — remote compute makes
  this viable.

## What got me thinking

[Theo (t3.gg)](https://www.youtube.com/@t3dotgg) was the catalyst. His setup
and thinking around agent fleets:

- **The starter video:**
  [youtube.com/watch?v=9tGrhrVKCrE](https://www.youtube.com/watch?v=9tGrhrVKCrE)
  — How his AI coding workflow changed. Runs a headless Mac Mini on the local
  network as a continuous coding agent, laptop as thin client via T3 Code +
  Tailscale. Single-threaded sequential workflow (fresh threads per task), ~95%
  ship on first attempt across 100+ threads. Key insight: "If you're looking at
  the code more than you're looking at the conversation about the code, you're
  already behind."

- **"I made Claude smarter by writing it a letter"**
  ([LinkedIn repost](https://www.linkedin.com/posts/ct-lin-8b7a8318b_i-made-claude-smarter-by-writing-it-a-letter-share-7492945969274089474-UmuW/))
  — Theo's `AGENTS.md` starts with a personal letter ("I'm Theo. You're my
  agent. We'll be working together a lot...") instead of a sparse config list.
  Models tone-match, so conversational instructions produce conversational
  responses. He spent 16 hours hand-writing markdown files and said it was 100%
  worth it.

- **Fleet repo architecture**
  ([AGENTS.md breakdown](https://finance.biggo.com/news/63e17fcb23548c16),
  [fleet repo structure](https://www.threads.com/@readus_org/post/Db_QzsTjdNf/),
  [managing a fleet](https://www.developersdigest.tech/blog/managing-a-fleet-of-claude-agents))
  — Manages 5 machines with a "fleet repo": a git repository with two
  foundational files (`AGENTS.md` and `CLAUDE.md`) plus inventory/computer
  metadata describing each machine's specs, role, and connection method. Skills
  organized hierarchically: universal (all machines), Claude-only, and
  command-center (leader machine only). Each skill has metadata specifying
  target machines. "Apply changes to the fleet" commits and pushes to propagate
  updates across all 5 machines via SSH over Tailscale. The key shift is
  treating concurrent AI execution as an organizational system — not a single
  assistant but a fleet requiring centralized configuration, status tracking,
  and work isolation. Includes a provision-a-box skill (agent studied his
  config + bash history, wrote onboarding instructions, iterated until
  provisioning a new Linux box became a single repeatable operation) and an HTML
  fleet dashboard with color-coded tmux themes per machine.

- **T3 Code** — Open-source "harness manager" (not an agent itself) that wraps
  Claude Code, Codex, Fable, etc. and exposes them over websocket to desktop,
  web (app.t3.codes), and mobile apps.

Others adopting similar patterns:

- [@bil0090's tweet](https://x.com/bil0090/status/2091257247284273659) — adopted
  the fleet approach and open sourced it:
  [Bil0000/agents-fleet](https://github.com/Bil0000/agents-fleet).
- **[AgentsRoom](https://agentsroom.dev/ai-agent-fleet)** — Native desktop app
  as a "fleet control plane" for managing multiple AI coding agents. Centralized
  start/stop/pause, real-time status dashboards, Git worktree isolation per
  agent, token cost observability. Supports 14+ providers (Claude Code, Codex,
  Cursor, Aider, Grok, etc.). Remote fleet feature with E2E encrypted relay.
  Free download + mobile companion (iOS/Android). Key insight: "Without a
  control plane, ten agents are worth less than three well-watched ones."

Services emerging specifically for remote agent management (many surfaced in
Matt's tweet replies):

| Service | What it does | Notes |
| --- | --- | --- |
| [Omnara](https://omnara.dev) | Voice-first mobile/web command center, cloud handoff when machine goes offline | YC S25, Apple Watch support |
| [Conductor](https://www.conductor.build/) | Mac app with isolated worktrees + Vercel Sandbox cloud workspaces | $22M raised, free currently |
| [Warp Factories](https://www.warp.dev) | Cloud control plane, live steering, local handoff via Factory MCP | Closed beta |
| [Fleet (fleetctl.ai)](https://fleetctl.ai) | YAML-defined agent workflows, audit trails, per-agent budgets, Linux jails | Free 500 starts; $299/mo business |
| [vibetunnel](https://github.com/nicedoc/vibetunnel) | Browser proxy for Mac terminal, asciinema recording | Free, 4.4k GitHub stars |
| [Codeman](https://github.com/Ark0N/Codeman) | Self-hosted mission control: tmux sessions, browser streaming, auto-resume, REST API | MIT, free |
| [amux](https://github.com/amux-ai/amux) | SQLite kanban, tmux per agent, web+mobile dashboard, self-healing | MIT, Rust binary |
| [Claude Squad](https://github.com/smtg-ai/claude-squad) | TUI for tmux + worktrees, 7.9k stars | AGPL-3.0 |

And Matt Pocock's tweet that crystallized it:

> "I'm moving away from my local dev setup. Makes zero sense to me now."
> — [@mattpocockuk, Aug 22, 2026](https://x.com/mattpocockuk/status/2091194428639621284)
> (942K views, 3.5K likes)

He also noted the tension: cloud still feels too slow for quick
iteration/design. His counter: preview deploys and `/prototype` cover it.

Related:

> "We'll look back on 'one dev, many terminals' as an awkward interregnum"
> — [@mattpocockuk](https://x.com/mattpocockuk/status/2089592149385822686)

He also published his [agent skills](https://github.com/mattpocock/skills) —
reusable grilling, TDD, code review, and domain-modeling skills for AI agents.

## What this repo is for

Research, configuration, and tooling for moving my agent workloads off the
laptop and onto remote compute — whether that's a cloud VM, a spare Mac on the
tailnet, or a mix.

The `homelab` and `codex-controls-mac` repos handle the "spare Mac on the local
network" case. This repo handles everything else:

- Remote agent execution setup for cloud VMs (SSH targets, persistent servers,
  on-demand environments)
- Agent orchestrator configuration for remote modes:
  - **Orca**: Remote Server (`orca serve`), SSH worktrees, per-workspace Cloud
    VMs via `orca.yaml`
  - **T3 Code**: `t3 connect` (cloud tunnel), multi-machine load balancing
    (auto-distributes threads across connected environments), Remote SSH
    environments. See also
    [T3 Brigade Fleet](https://github.com/escoffier-labs/t3-brigade-fleet) for
    fleet-level dispatch across machines.
- Optio deployment (Helm + K8s) on remote compute
- Hermes cron on always-on remote infrastructure
- Agent fleet management patterns (Theo-style fleet repo, AgentsRoom, etc.)
- Cost analysis and provider comparison for remote compute
- Tailscale mesh configuration for multi-machine setups

## Research

- [Remote AI Agent Execution Infrastructure (Sep 2026)](research/0002-remote-agent-infrastructure.md)
  — The actual research: fleet management tools (Herdr, amux, T3 Code, Omnara,
  AgentsRoom, etc.), agent sandboxes (E2B, Fly Sprites, Modal, Daytona, Claude
  Managed Agents), orchestration (Optio, Hermes, Orca, Warp Factories), DIY
  patterns (Theo's fleet repo, tmux+Tailscale, spare Mac setups), cost analysis,
  and 25+ community articles/videos/discussions.

- [Autonomous Ticket-to-Merge Tools (Sep 2026)](research/0003-ticket-to-merge-tools.md)
  — Tools like Optio that take a ticket, assign an agent, open a PR, watch CI,
  handle review feedback, and iterate until merge. Covers Optio, Warp Factories,
  Copilot Coding Agent, Jules, Devin, Cosine/Lumen, Factory, and more.

- [Remote/Cloud Dev Environments (Sep 2026)](research/0001-remote-dev-environments.md)
  — Earlier survey of traditional CDEs (editors, IDEs, Codespaces, Coder,
  DevPod, etc.). Useful background but not the core problem — "dev environment"
  in 2026 means "where do my agents run," not "where do I edit code."
