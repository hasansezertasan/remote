# Remote AI Coding Agent Execution Infrastructure

> Research compiled September 2026. The "dev environment" in 2026 is increasingly "where do my AI agents run," not "where do I edit code."

---

## Table of Contents

1. [Agent Fleet Management Tools](#1-agent-fleet-management-tools)
2. [Agent Execution Sandboxes / Infrastructure](#2-agent-execution-sandboxes--infrastructure)
3. [Agent Orchestration / Workflow Tools](#3-agent-orchestration--workflow-tools)
4. [DIY Patterns](#4-diy-patterns)
5. [Community Content](#5-community-content)
6. [Cost Analysis](#6-cost-analysis)
7. [Comparison Tables](#7-comparison-tables)

---

## 1. Agent Fleet Management Tools

Tools for managing fleets of AI coding agents -- running, monitoring, and coordinating multiple agent sessions.

### AgentsRoom

- **What**: Multi-agent terminal and IDE for piloting multiple AI coding projects in parallel, with remote fleet capabilities.
- **Remote relevance**: Encrypted remote fleet view aggregates agents running across all your machines into one unified window. Each machine runs agents locally; AgentsRoom provides the control plane.
- **Pricing**: Proprietary; pricing not publicly disclosed.
- **Open source**: No.
- **Self-hostable**: Agents run locally on each machine; the fleet view connects them.
- **Key differentiator**: 14 built-in roles, 230+ expert agents from The Agency (open-source MIT marketplace), mobile-desktop sync for controlling agents from your phone.
- **Link**: [agentsroom.dev](https://agentsroom.dev/)
- **Notable**: [Remote Fleet feature page](https://agentsroom.dev/features/remote-fleet) | [AI Coding Swarm guide](https://agentsroom.dev/ai-coding-swarm)

### Claude Squad

- **What**: Open-source Go TUI for running and managing multiple AI coding agents (Claude Code, Codex, Gemini, Aider, OpenCode, Amp) in isolated git worktrees via tmux.
- **Remote relevance**: Sessions run in tmux, so they survive SSH disconnects. Ideal for running on a remote box accessed via SSH/Tailscale.
- **Pricing**: Free.
- **Open source**: Yes, AGPL-3.0. ~7.9k GitHub stars (June 2026).
- **Self-hostable**: Yes, runs anywhere tmux runs.
- **Key differentiator**: Git worktree isolation per task, configurable agent launch commands, TUI with diff review before applying changes.
- **Link**: [github.com/smtg-ai/claude-squad](https://github.com/smtg-ai/claude-squad)
- **Notable**: [Review on vibecodinghub.org](https://vibecodinghub.org/tools/claude-squad) | [Alternatives comparison on runpane.com](https://runpane.com/alternatives/claude-squad)

### Conductor

- **What**: macOS application for running multiple Claude Code and Codex agents in parallel with a visual dashboard and diff-first review UI. Cloud Workspaces (powered by Vercel Sandbox) run agents on remote VMs so they continue when the laptop closes.
- **Remote relevance**: Cloud Workspaces automatically provision a Vercel Sandbox per agent (8-core CPU, 16 GB RAM, Amazon Linux 2023). Agents keep running in the cloud even when the Conductor app is closed. Also usable locally for parallel execution on your own Mac.
- **Pricing**: Free (you pay only your API costs). Raised $22M in funding.
- **Open source**: No.
- **Self-hostable**: macOS only.
- **Key differentiator**: Visual dashboard, each agent gets its own git worktree, diff-first review UI. Used by Linear, Vercel, Stripe, Notion.
- **Link**: [conductor.build](https://www.conductor.build/)

### T3 Code

- **What**: Open-source desktop + mobile + web app by Theo (t3.gg) for launching and managing Claude Code, Codex, Cursor, Grok Build, and OpenCode sessions.
- **Remote relevance**: `npx t3 connect` creates a persistent background instance accessible from any device without Tailscale. `npx t3 pair --tailscale` generates pairing links for Tailscale users. Mobile apps (iOS/Android) let you control remote agents from your phone.
- **Pricing**: Free, BYOK (bring your own API keys).
- **Open source**: Yes. [github.com/pingdotgg/t3code](https://github.com/pingdotgg/t3code)
- **Self-hostable**: Yes, self-hosted server component.
- **Key differentiator**: `npx t3 connect` for zero-config remote access, mobile apps, thread-based session management with inline diffs and one-click PRs.
- **Link**: [t3.codes](https://t3.codes/)
- **Notable**: [YouTube: "Theo Did It. T3 Code: The FREE AI Coding Agent You Need to Try"](https://www.youtube.com/watch?v=-7akxGb-lAM) | [BetterStack guide](https://betterstack.com/community/guides/ai/t3-code/)

### Codeman

- **What**: Self-hosted mission control for AI coding agents with real-time xterm.js terminals at 60fps, per-session token/cost tracking, and tab-based navigation.
- **Remote relevance**: Sessions run inside tmux and survive server restarts, network drops, and machine sleep. Background daemon and service install capabilities for always-on operation.
- **Pricing**: Free.
- **Open source**: Yes. [github.com/Ark0N/Codeman](https://github.com/Ark0N/Codeman)
- **Self-hostable**: Yes, designed for self-hosting.
- **Key differentiator**: 6-layer anti-flicker pipeline for smooth 60fps output, session fuzzy search (Ctrl/Cmd/Alt+K), lifecycle history tracking across restarts.
- **Link**: [github.com/Ark0N/Codeman](https://github.com/Ark0N/Codeman)

### Omnara

- **What**: Web and mobile command center for AI coding agents -- monitor, approve, and intervene from anywhere (desktop, mobile, web, Apple Watch).
- **Remote relevance**: Core value proposition is remote agent control. Run Claude Code on your computer, stay connected from anywhere. Persistent cloud sessions continue when laptop goes offline.
- **Pricing**: Not publicly disclosed. 6,000+ users, 2M+ messages sent.
- **Open source**: Yes, describes itself as "the open-source alternative to Claude Managed Agents." [github.com/omnara-ai/omnara](https://github.com/omnara-ai/omnara)
- **Self-hostable**: Yes.
- **Key differentiator**: Voice commands, Apple Watch support, parallel agent execution, persistent cloud sessions.
- **Link**: [remote.omnara.com](https://remote.omnara.com/)
- **Notable**: [Product Hunt launch](https://www.producthunt.com/products/omnara)

### Herdr (Herder)

- **What**: Open-source Rust terminal multiplexer built specifically for AI coding agents (~15k GitHub stars, #1 GitHub Trending June 30, 2026).
- **Remote relevance**: Single ~10MB binary, no Electron/cloud dependency. Runs in your existing terminal via SSH. Understands agent state (working, blocked on permission, idle, done) unlike plain tmux.
- **Pricing**: Free.
- **Open source**: Yes, open-source. Built by a single full-time developer.
- **Self-hostable**: Yes, single binary.
- **Key differentiator**: Agent-aware state tracking, CLI/socket API that agents can call to launch other agents and orchestrate multi-agent pipelines, real-time sidebar showing agent status.
- **Link**: [github.com/herdr/herdr](https://github.com/herdr/herdr)
- **Notable**: [MindStudio blog post](https://www.mindstudio.ai/blog/herder-terminal-agent-multiplexer) | [Developers Digest setup guide](https://www.developersdigest.tech/blog/herdr-setup-guide-agent-fleet-workflows) | [Mac Mini headless guide on DevelopersIO](https://dev.classmethod.jp/en/articles/reona-herdr-remote-mac-mini/)

### amux

- **What**: Open-source control plane for AI coding agents -- run, coordinate, monitor, and recover fleets of Claude Code, Codex, and Gemini CLI sessions from one dashboard and a phone.
- **Remote relevance**: Built for "start agents before bed, review PRs in the morning." Self-healing watchdog auto-restarts crashed agents, auto-compacts context overflows, sends mobile push notifications. Runs agents on a fleet of machines.
- **Pricing**: Free, MIT-licensed, no paid tier.
- **Open source**: Yes, MIT. Shipped early 2026.
- **Self-hostable**: Yes.
- **Key differentiator**: Container-isolated agent execution (never on the host), shared kanban board for task coordination, session peek for live terminal output, mobile push notifications.
- **Link**: [amux.io](https://amux.io/)
- **Notable**: [Agent multiplexer comparison](https://amux.io/guides/best-ai-agent-multiplexers-2026/) | [AI agent sandboxing guide](https://amux.io/guides/ai-agent-sandboxing/)

### Paneflow

- **What**: Native terminal workspace (Rust on Zed's GPUI) for running coding agents side by side with terminals, branches, diffs, and servers in one place.
- **Remote relevance**: Native builds for Linux, macOS Apple Silicon, and Windows. Tracks agent state (thinking, waiting, stalled, failed, done).
- **Pricing**: Free.
- **Open source**: Yes. [github.com/arthjean/paneflow](https://github.com/arthjean/paneflow)
- **Self-hostable**: Yes.
- **Key differentiator**: Built on Zed's GPUI for native performance, workspace-and-branch-tied task tracking.
- **Link**: [paneflow.dev](https://paneflow.dev/)
- **Notable**: [DEV Community: Building a native terminal for AI coding agents in Rust + GPUI](https://dev.to/arthurj-dev/building-a-native-terminal-for-ai-coding-agents-in-rust-gpui-2bg4)

### Vibe Kanban

- **What**: Cross-platform Rust CLI + web UI for orchestrating AI coding agents using a Kanban-style board with visual code review.
- **Remote relevance**: Tasks run in isolated git worktrees. MCP client/server integration. Line-by-line diffs with feedback sent back to agents.
- **Pricing**: Free, Apache-2.0 license.
- **Open source**: Yes. **Note**: The company behind it (bloop) shut down April 10, 2026. Local workspaces continue to function; cloud features were removed.
- **Self-hostable**: Yes (local mode only post-shutdown).
- **Key differentiator**: Kanban-style task management for agents, MCP protocol integration (both client and server).
- **Link**: [vibekanban.com](https://vibekanban.com/) | [VirtusLab blog](https://virtuslab.com/blog/ai/vibe-kanban)

### Nimbalyst

- **What**: Visual AI coding workspace and session manager with Monaco editor, WYSIWYG markdown, Excalidraw diagrams, and agent session tracking.
- **Remote relevance**: More of a local workspace organizer than a remote execution tool. Focused on session tracking and multi-agent workflow visualization.
- **Pricing**: Individual plan: $0 (free). Team pricing TBA.
- **Open source**: Yes, free and open-source.
- **Self-hostable**: Yes.
- **Key differentiator**: Visual canvas approach rather than terminal-based. Good for planning and documentation alongside agent execution.
- **Link**: [nimbalyst.com](https://nimbalyst.com/)

### Mux (by Coder)

- **What**: Coder's open-source coding-agent multiplexer with desktop/browser UI, isolated runtimes, git divergence views, model routing, and review tooling.
- **Remote relevance**: Browser UI enables remote access. Isolated runtimes prevent collisions across parallel agent work.
- **Pricing**: Free, open-source.
- **Open source**: Yes.
- **Self-hostable**: Yes.
- **Key differentiator**: From Coder (enterprise remote dev environment company), combines browser/desktop UI with isolated runtimes and model routing.
- **Link**: [xum.coder.com](https://xum.coder.com/)

### Other Multiplexers

| Tool | Description | License | Platform |
|------|-------------|---------|----------|
| **cmux** | Ghostty-based macOS terminal with vertical tabs and notifications | GPL-3.0 (free) + paid Founder's Edition | macOS only |
| **dmux** | tmux pane manager for AI-powered dev sessions with git worktree isolation | MIT (free) | Cross-platform |
| **workmux** | Shell script combining tmux with git worktrees for parallel agent sessions | MIT (free) | Cross-platform |
| **Termdock** | Purpose-built agent terminal with resource monitoring, health checks, web session viewer | Free tier available | Cross-platform |
| **wmux** | Windows terminal multiplexer for AI agents | Open source | Windows |

- **dmux**: [dmux.ai](https://dmux.ai/)
- **Termdock**: [termdock.com](https://www.termdock.com/)

### VibeTunnel

- **What**: Open-source terminal-sharing app that turns any browser into a terminal for monitoring builds and controlling AI agents.
- **Remote relevance**: Core use case is remote agent access. Native macOS menu-bar app + npm package for Linux/headless. Sessions stay on local machine; remote access via Tailscale, ngrok, Cloudflare Tunnels, or local network.
- **Pricing**: Free, MIT license.
- **Open source**: Yes.
- **Self-hostable**: Yes.
- **Key differentiator**: Browser-based terminal access without SSH config, Git Follow mode, session recording (asciinema), terminal-native not agent-specific.
- **Link**: [github.com/nicedoc/vibetunnel](https://github.com/nicedoc/vibetunnel) | [Medium article](https://medium.com/coding-nexus/vibetunnel-turns-any-browser-into-your-terminal-bf4c5b6baf8e)

### Apra Fleet

- **What**: Open-source MCP server that turns machines you already own into a fleet of AI agents, dispatching commands and prompts across them from a single conversation.
- **Remote relevance**: Core design is multi-machine. Any MCP-capable agent (Claude Code, Gemini, Codex, Copilot, OpenCode, Antigravity) becomes the orchestrator. Registers machines as members, dispatches via SSH, moves files, brokers credentials.
- **Pricing**: Free, Apache-2.0 license. BYOK for LLM subscriptions.
- **Open source**: Yes. [github.com/Apra-Labs/apra-fleet](https://github.com/Apra-Labs/apra-fleet)
- **Self-hostable**: Yes, entirely.
- **Key differentiator**: MCP-native architecture (no dashboard/YAML -- just tell your agent what to do), cost-tier routing across providers, stall detection and crash recovery, durable multi-hour workflows.
- **Link**: [apra-labs.github.io/apra-fleet](https://apra-labs.github.io/apra-fleet/)

---

## 2. Agent Execution Sandboxes / Infrastructure

Services specifically built for running AI agent workloads in the cloud.

### Daytona

- **What**: Composable computers for AI agents -- fast, stateful sandboxes provisioned in under 60ms.
- **Remote relevance**: Purpose-built for AI agent code execution. Pivoted from CDE to agent sandbox in February 2025.
- **Pricing**: Not publicly disclosed; enterprise contracts. Compliance-first positioning for regulated industries. Funding: $24M Series A (February 2026, FirstMark Capital).
- **Open source**: Was open-source; **moved to closed source in June 2026**, citing risk of AI-assisted vulnerability discovery. GitHub repo receives no further updates.
- **Self-hostable**: No longer for the commercial platform (managed service / enterprise deployment); legacy open-source release (frozen at v0.190.0) remains runnable but unmaintained without security updates.
- **Key differentiator**: Sub-60ms provisioning, compliance-first for enterprises, full environment control (CPU, RAM, disk, OS).
- **Link**: [daytona.io](https://www.daytona.io/) | [GitHub](https://github.com/daytonaio/daytona)
- **Notable**: [Latent Space podcast with CEO Ivan Burazin](https://www.latent.space/p/daytona) | [WorkOS interview](https://workos.com/blog/composable-computers-for-agents-daytona-ivan-burazin) | [Cerebral Valley profile](https://cerebralvalley.beehiiv.com/p/daytona-composable-computers-for-ai-agents)

### E2B

- **What**: Enterprise AI Agent Cloud -- isolated Firecracker microVM sandboxes for AI agents to execute code, analyze data, and operate virtual computers.
- **Remote relevance**: SDKs (Python, JS/TS) let agents provision sandboxes on demand with terminal, filesystem, Git, and network access.
- **Pricing**: $37M+ total funding. Pay-per-use model. Free tier available.
- **Open source**: SDK and templates are open-source; infrastructure is managed.
- **Self-hostable**: No (managed cloud), but custom templates supported.
- **Key differentiator**: Firecracker microVM boot in <200ms, 10,000+ teams, focused exclusively on "isolated computers for agents" (not a general cloud).
- **Link**: [e2b.dev](https://e2b.dev/)
- **Notable**: [AgentMarketCap comparison](https://agentmarketcap.ai/blog/2026/04/07/ai-agent-sandbox-infrastructure-e2b-modal-daytona-fly-machines-secure-code-execution) | [Northflank E2B vs Modal comparison](https://northflank.com/blog/e2b-vs-modal)

### Ona (formerly Gitpod) -- Acquired by OpenAI

- **What**: Rebranded from Gitpod (September 2025), pivoted from CDE to "mission control for AI engineering agents." Acquired by OpenAI (June 11, 2026) for Codex integration.
- **Remote relevance**: Built secure cloud execution and orchestration technology. OpenAI described the acquisition as enabling Codex to "take on longer-running work, even when laptops are closed."
- **Pricing**: N/A (now part of OpenAI/Codex).
- **Open source**: Gitpod Flex is self-hosted only (AWS initially). Classic SaaS shut down October 2025.
- **Self-hostable**: Gitpod Flex was; Ona's future under OpenAI is unclear.
- **Key differentiator**: Enterprise governance (Ona Guardrails), autonomous background agents, workflow automations. Now powers Codex's cloud execution.
- **Link**: Previously ona.dev | [OpenAI acquisition announcement](https://tech-insider.org/ie/openai-ona-acquisition-codex-2026/)
- **Notable**: [MSN coverage of pivot](https://www.msn.com/en-us/news/technology/gitpod-reinvents-itself-as-ona-in-pivot-to-ai-agent-platform/ar-AA1LNZsN)

### boxd

- **What**: Persistent, forkable KVM microVMs with sub-millisecond resume from suspend.
- **Remote relevance**: Each VM has its own kernel, network stack, and disk (not a container). Fresh boots ~30ms, forks ~160ms inheriting exact disk/processes/memory.
- **Pricing**: Not publicly disclosed.
- **Open source**: No.
- **Self-hostable**: Yes (single Rust binary runs on any KVM-capable Linux host).
- **Key differentiator**: Sub-millisecond resume, VM forking (fork inherits parent's exact state), SSH-native persistent VMs.
- **Link**: [boxd.sh](https://boxd.sh/) | [Agent sandboxes docs](https://docs.boxd.sh/agents/agent-sandboxes)
- **Notable**: [Cloud dev environment guide](https://boxd.sh/blog/cloud-dev-environment-complete-guide/)

### Modal

- **What**: Python-first serverless compute with gVisor-isolated containers, fast startup, and elastic GPU access.
- **Remote relevance**: Code-first SDK (Python, Go, JS/TS) for defining infrastructure. 100,000+ concurrent sandboxes with full observability. SOC 2 Type II, HIPAA eligible.
- **Pricing**: Pay-per-use. 10,000+ teams.
- **Open source**: No (managed platform).
- **Self-hostable**: No.
- **Key differentiator**: No YAML config (code-first), GPU support for acceleration workloads, gVisor isolation, massive concurrency (100k+ sandboxes).
- **Link**: [modal.com](https://modal.com/)
- **Notable**: [Modal blog on coding agent infrastructure](https://modal.com/resources/best-infrastructure-platforms-coding-agents) | [Northflank E2B vs Modal](https://northflank.com/blog/e2b-vs-modal)

### Fly.io Machines + Sprites

- **What**: KVM hardware-isolated VMs (Machines) controlled via REST API, plus Sprites -- Firecracker-based lightweight VMs specifically designed for coding agents (launched January 2026).
- **Remote relevance**: Machines boot in <300ms, run in 35+ regions. Sprites are persistent with 100GB NVMe storage, auto-idle billing, checkpoint/restore in ~300ms. Designed for agents that need a warm environment between sessions.
- **Pricing**: Machines: $0.0028/hour for a usable instance. Sprites: idle billing stops when not in use.
- **Open source**: No (managed platform).
- **Self-hostable**: No.
- **Key differentiator**: Sprites' suspend/resume is the killer feature for bursty agents. Full Docker ecosystem. Multi-region (35+ regions). GPU support.
- **Link**: [fly.io](https://fly.io/) | [Sprites announcement on Simon Willison's blog](https://simonwillison.net/2026/Jan/9/sprites-dev/)
- **Notable**: [SDxCentral coverage of Sprites](https://www.sdxcentral.com/news/flyio-debuts-sprites-persistent-vms-that-let-ai-agents-keep-their-state/) | [E2B vs Fly Machines comparison](https://bertomill.medium.com/e2b-vs-fly-machines-which-sandbox-runtime-is-right-for-your-ai-agents-56684a8931bb)

### Northflank

- **What**: Production infrastructure platform covering agents, APIs, workers, databases, cron, and sandboxed execution with MicroVM/gVisor isolation.
- **Remote relevance**: 2M+ isolated workloads monthly. Hit 100,000 concurrent 1-vCPU sandboxes in 24 seconds (June 2026 benchmark). BYOC deployment (run in your own cloud).
- **Pricing**: CPU $0.01667/vCPU-hour, RAM $0.00833/GB-hour, H100 GPU $2.74/hour.
- **Open source**: No.
- **Self-hostable**: Yes via BYOC (Northflank manages Kubernetes on your cloud) or BYOK (your existing Kubernetes cluster).
- **Key differentiator**: Full-stack platform (not just sandboxes), BYOC/BYOK deployment, Kata Container + gVisor isolation options.
- **Link**: [northflank.com](https://northflank.com/)
- **Notable**: [Best agent cloud platforms comparison](https://northflank.com/blog/best-agent-cloud-platforms) | [Sandboxing guide](https://northflank.com/blog/how-to-sandbox-ai-agents)

### Vercel Sandbox

- **What**: Isolated code execution environments using Firecracker microVMs, GA since January 30, 2026.
- **Remote relevance**: Positioned as "the execution layer for agents." Native Python/Node.js support. SDK and CLI for programmatic sandbox management.
- **Pricing**: Hobby tier: 5 active CPU hours, 420 GB-hours memory, 5,000 creations, 10 concurrent sandboxes (free). Paid tiers available.
- **Open source**: No (managed platform).
- **Self-hostable**: No. Single region only (US East/iad1).
- **Key differentiator**: Tight integration with Vercel AI SDK and Workflows (pause/resume for long-running agent tasks). Part of a broader Vercel ecosystem.
- **Link**: [vercel.com](https://vercel.com/)
- **Notable**: [Vercel Sandbox review](https://aiidelist.com/ide/vercel-sandbox) | [Comparison on Developers Digest](https://www.developersdigest.tech/blog/ai-agent-code-sandbox-comparison-2026)

### Blaxel

- **What**: Perpetual sandbox platform for production AI agent workloads with microVM isolation, perpetual standby, and zero idle compute cost.
- **Remote relevance**: First-class sandbox provider in the OpenAI Agents SDK. Combines sandboxes, agent hosting, and MCP server hosting.
- **Pricing**: Not publicly disclosed. Agent Runtime still marked "Soon" on the website as of September 2026 (was expected Q2 2026; has not launched).
- **Open source**: No.
- **Self-hostable**: No. Lacks BYOC, compliance controls, GPU support.
- **Key differentiator**: Integration as first-class provider in OpenAI Agents SDK, perpetual standby (zero idle cost), combined sandbox + agent + MCP hosting.
- **Link**: [blaxel.ai](https://blaxel.ai/)
- **Notable**: [Blaxel alternatives comparison](https://blaxel.ai/blog/e2b-alternatives-sandbox-environments)

### Claude Managed Agents (Anthropic)

- **What**: Composable APIs for building and deploying cloud-hosted agents at scale, with sandboxed execution, checkpointing, credential management, scoped permissions, and end-to-end tracing. Launched April 8, 2026 (public beta).
- **Remote relevance**: First frontier model provider to own the infrastructure layer for agent execution. Each agent runs in a gVisor-isolated container. Network egress default-deny.
- **Pricing**: Part of Claude Platform. Pricing via API usage.
- **Open source**: No (managed platform).
- **Self-hostable**: Split-plane. The agent loop (orchestration, context management, error recovery) stays on Anthropic infrastructure. Tool execution can run on customer infrastructure via self-hosted sandboxes (GA May 2026). Supported providers: Cloudflare, Daytona, Modal, Vercel, plus a custom sandbox client API for private cloud or air-gapped environments. ([Self-hosted sandboxes docs](https://platform.claude.com/docs/en/managed-agents/self-hosted-sandboxes))
- **Key differentiator**: Developers don't write the agent loop, provision sandboxes, or wire up error recovery, checkpointing, or credential vaulting -- Anthropic handles it all.
- **Link**: [platform.claude.com/docs/en/managed-agents/overview](https://platform.claude.com/docs/en/managed-agents/overview)
- **Notable**: [Cloudflare integration announcement](https://blog.cloudflare.com/claude-managed-agents/) | [InfoQ coverage](https://www.infoq.com/news/2026/05/code-with-claude/) | [Pluto Security analysis](https://pluto.security/blog/inside-claude-managed-agents/)

### OpenAI Codex (Cloud)

- **What**: Cloud-based software engineering agent. Each task runs in a separate cloud sandbox preloaded with your repository. Powered by GPT-5.6 Sol (August 2026).
- **Remote relevance**: Cloud-first execution model. Tasks run in parallel in isolated sandboxes. Agent can read/edit files, run tests, invoke tools. Available via ChatGPT Plus+.
- **Pricing**: Included with ChatGPT plans (Plus and up). Five-hour usage window shared.
- **Open source**: No (also has open-source Codex CLI for local use).
- **Self-hostable**: No (cloud only for sandbox mode). CLI runs locally.
- **Key differentiator**: Tight ChatGPT integration, cloud-first parallel execution, Ona acquisition enables "longer-running work, even when laptops are closed."
- **Link**: [openai.com/index/introducing-codex/](https://openai.com/index/introducing-codex/)

---

## 3. Agent Orchestration / Workflow Tools

Tools managing the agent lifecycle beyond just running sessions.

### Optio

- **What**: Open-source (MIT) orchestration system that turns tickets into merged PRs using AI coding agents. Seven-stage pipeline: Intake -> Queued -> Provisioning -> Running -> PR Opened -> CI & Review -> Merged.
- **Remote relevance**: Runs on Kubernetes (Helm chart). Pod-per-repo isolation with git worktree isolation. Autonomous feedback loops: watches PRs, feeds CI failures back to agent, keeps going until done.
- **Pricing**: Free (MIT license). Self-hosted on your infrastructure.
- **Open source**: Yes, MIT. [github.com/jonwiggins/optio](https://github.com/jonwiggins/optio)
- **Self-hostable**: Yes (Kubernetes required). Tech stack: Fastify API, Next.js dashboard, BullMQ workers, Drizzle on Postgres.
- **Supported agents**: Claude Code, Codex, GitHub Copilot, Gemini, OpenCode, Cursor.
- **Key differentiator**: Full ticket-to-merge automation. Three operational tiers: Tasks (repo work), Jobs (standalone), Agents (persistent message-driven processes). Integrates with Linear, Jira, Notion, Slack, Sentry, MCP servers.
- **Link**: [optio.host](https://optio.host/)
- **Notable**: [Show HN discussion](https://news.ycombinator.com/item?id=47520220) | [VibeCodingHub review](https://vibecodinghub.org/tools/optio)

### Hermes (Nous Research)

- **What**: Self-hosted, open-source autonomous AI agent with persistent daemon, cross-session memory, and built-in cron system. Released February 25, 2026.
- **Remote relevance**: Runs as a persistent daemon on your own infrastructure. Cron system schedules tasks with natural language or cron expressions. Delivers output to Telegram, Slack, or email.
- **Pricing**: Free, open-source.
- **Open source**: Yes. [github.com/NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent)
- **Self-hostable**: Yes, entirely.
- **Key differentiator**: Memory accumulation across sessions, chat-driven cron management (pause, resume, edit, remove jobs without CLI), cross-platform output delivery.
- **Link**: [hermes-agent.nousresearch.com](https://hermes-agent.nousresearch.com/)
- **Notable**: [Cron docs](https://hermes-agent.nousresearch.com/docs/user-guide/features/cron) | [AI Builder Club profile](https://www.aibuilderclub.com/blog/hermes-nous-research-self-improving-agent)

### Orca

- **What**: ADE (Agent Development Environment) for running a fleet of parallel agents, each in its own git worktree. Desktop, mobile, and remote runtime. 20,000+ GitHub stars (July 2026).
- **Remote relevance**: Supports SSH remote worktrees, self-hosted Orca server, and on-demand VMs. Monitor and steer agents from your phone with push notifications.
- **Pricing**: Free, MIT license. BYOK (bring your own subscriptions).
- **Open source**: Yes, MIT. [github.com/stablyai/orca](https://github.com/stablyai/orca)
- **Self-hostable**: Yes.
- **Supported agents**: 30+ CLI agents including Claude Code, Codex, OpenCode, Pi.
- **Key differentiator**: Multi-mode remote execution (SSH, self-hosted server, on-demand VM), mobile-first monitoring, worktree isolation as first-class primitive.
- **Link**: [onorca.dev](https://www.onorca.dev/)
- **Notable**: [Orca docs](https://www.onorca.dev/docs) | [First session guide](https://www.onorca.dev/docs/first-session) | [Blog: I Run 5 AI Coders in Parallel](https://blog.margrop.net/en/post/orca-parallel-ai-agent-ide-review/)

### Superset

- **What**: Source-available macOS desktop IDE and CLI for orchestrating 100+ parallel AI coding agents. YC Spring 2026 batch.
- **Remote relevance**: Primarily local execution with git worktree isolation. Agent-agnostic (Claude Code, Codex, Cursor, Gemini, Copilot, Amp, OpenCode).
- **Pricing**: Free tier (full parallel orchestration). Pro: $20/seat/month.
- **Open source**: Source-available.
- **Self-hostable**: macOS desktop app.
- **Key differentiator**: YC-backed, built-in chat/diff/editor/browser surfaces on top of orchestration, designed for coordination rather than model quality.
- **Link**: [superset.sh](https://superset.sh/)

### Warp Factories

- **What**: Cloud infrastructure for repeatable "software factory" pipelines where coding agents triage tickets, write specs, implement, review, verify, and monitor. Closed beta August 18, 2026.
- **Remote relevance**: Cloud-native agent pipelines. Customers choose coding models/harnesses (including Codex and Claude Code). Connects to Linear, Jira, Slack, Teams.
- **Pricing**: Enterprise pricing (closed beta). Warp reports 718,000 active developers, 179,000 agents/day.
- **Open source**: No.
- **Self-hostable**: Split-plane (Enterprise plan). Managed execution can run inside your own VPC; the control plane remains managed by Warp.
- **Key differentiator**: 6-stage orchestration layer. Warp's internal teams automate 20-30% of PRs using Factories. Comes with Warp Agent CLI (standalone, works in any terminal).
- **Link**: [warp.dev](https://www.warp.dev/)
- **Notable**: [Warp Agent CLI announcement](https://www.warp.dev/blog/introducing-the-warp-agent-cli-coding-agent) | [Enterprise DNA coverage](https://enterprisedna.co/resources/news/warp-factories-enterprise-ai-software-development-2026/)

### Claude Code Remote Control

- **What**: Built-in feature of Claude Code (research preview, early 2026) bridging local terminal sessions with claude.ai/code, Claude iOS app, and Claude Android app.
- **Remote relevance**: Run `/rc` or `claude --remote-control` to get a QR code/link. Phone sends messages through Anthropic API; Claude Code executes locally. Code/env/MCP stays on your machine. Headless mode for full automation.
- **Pricing**: Included with Claude Code subscription.
- **Open source**: Part of Claude Code (proprietary).
- **Self-hostable**: Runs on your machine; control plane goes through Anthropic.
- **Key differentiator**: Zero additional tooling needed. Works from phone natively. Headless mode for automation scripts.
- **Link**: [Claude Code docs](https://platform.claude.com/docs/)
- **Notable**: [explainx.ai guide](https://www.explainx.ai/blog/claude-code-mobile-remote-control-phone-guide-2026) | [SeaWork blog](https://seawork.ai/en/blogs/claude-code-from-your-phone/) | [claudefa.st setup guide](https://claudefa.st/blog/guide/development/remote-control-guide)

---

## 4. DIY Patterns

### Theo's Fleet Repo Pattern

Theo (t3.gg) manages 5+ machines with a git repo of markdown configs and skills, propagated via SSH over Tailscale. The core idea: moving past a single assistant to treat concurrent AI execution as an organizational system requiring centralized configuration, status tracking, and work isolation.

**Architecture**:
- **Two foundational files**: `agents.md` (personal letter to the agent — tone, workflow, expectations) and `claude.md` (technical config, rules, constraints). Models tone-match, so conversational instructions produce conversational responses.
- **Inventory/computer metadata**: each machine's specs, role, OS, connection method, and installed tools described in markdown so agents understand the fleet topology.
- Skills organized hierarchically: `universal/` (synced to all machines), agent-specific folders, command-center-only skills. Each skill has metadata specifying target machines.
- `provision-a-box` skill: agent studied his config + bash history, wrote onboarding instructions, iterated until provisioning a new Linux box became a single repeatable operation.
- HTML dashboard of the fleet showing machines, specs, roles, connection methods (color-coded tmux themes so SSH sessions are visually distinguishable).
- `ccusage-fleet` script runs ccusage across all machines and aggregates usage numbers.
- "Apply changes to the fleet" commits, pushes to the fleet repo, then SSHs into each machine over Tailscale to pull the latest changes (working-tree update, not just remote refs). Exact pull/checkout mechanics are not publicly documented — likely a `git pull` or post-receive hook on each machine.

**Key insight**: Theo spent 16 hours hand-writing markdown files and said it was 100% worth it — cut his prompts to one sentence. The fleet repo pattern treats machine config as code, propagated via git push.

- **Notable**: [Fleet repo structure (Threads)](https://www.threads.com/@readus_org/post/Db_QzsTjdNf/) | [Managing a Fleet of Claude Agents (Developers Digest)](https://www.developersdigest.tech/blog/managing-a-fleet-of-claude-agents) | [BigGo Finance coverage](https://finance.biggo.com/news/63e17fcb23548c16) | [Theo's 67 projects with GPT-5.6 (joaoqueiros.com)](https://www.ai.joaoqueiros.com/blog/gpt-5-6-theo-67-projects-built-kept-threw-away) | [Theo on X about ccusage-fleet](https://x.com/theo/status/2083562305254822131) | [Theo on X about fleet configuration](https://x.com/theo/status/2072483470035820870)

### tmux + Tailscale + SSH/Mosh

The bread-and-butter DIY stack for remote agent execution:

1. **Tailscale**: Free WireGuard mesh VPN connecting all machines
2. **tmux**: Persistent terminal sessions that survive disconnects
3. **SSH** (or **Mosh** for unreliable connections): Access layer
4. **Git worktrees**: Isolation between parallel agent tasks

**Why this works**: Sessions survive laptop sleep, network drops, travel. Agents keep running when you close your laptop. Reattach from any device. Free, battle-tested, composable.

**Typical setup**:
```
laptop -> Tailscale -> remote machine (Hetzner/Mac Mini/etc.)
                         └── tmux session 1: Claude Code (task A)
                         └── tmux session 2: Claude Code (task B)  
                         └── tmux session 3: Codex CLI (task C)
```

- **Notable**: [OpenReplay: Setting Up a Remote Box for Agentic Coding](https://blog.openreplay.com/remote-box-agentic-coding-setup/) | [DEV Community: Remote Coding Full Stack](https://dev.to/stevengonsalvez/remote-coding-running-ai-agents-from-anywhere-the-full-stack-4lji)

### Spare Mac / Mac Mini Setups

Multiple guides exist for turning a spare Mac into a dedicated agent server:

**Security best practices**:
- Create a fresh macOS account with no Apple ID, no personal data, no cloud sync
- Blast radius is contained to a throwaway environment
- Use LaunchAgent to keep tmux server alive inside the GUI session (needed for Apple permission inheritance)

**Key challenge**: macOS locks permissions to the desktop session visible on-screen. Fix: LaunchAgent keeps a tmux server alive inside the GUI session on a fixed socket, so every agent session inherits the GUI session's permissions.

**Access methods**: SSH over Tailscale, Claude Code Remote Control (`/rc`), VNC, Codeman web UI.

- **Notable**: [DEV Community: Run Claude Code as Autonomous Agent on Mac Mini](https://dev.to/clawlabs/how-to-run-claude-code-as-an-autonomous-agent-on-a-mac-mini-52n8) | [Developers Digest: Spare Mac Setup Guide](https://www.developersdigest.tech/blog/spare-mac-claude-code-control-guide) | [DevelopersIO: Herdr Remote Mac Mini](https://dev.classmethod.jp/en/articles/reona-herdr-remote-mac-mini/) | [Blog: My Old MacBook as an AI Assistant](https://gipyeong-lee.github.io/2026/07/19/Setting-up-your-spare-Mac-for-Claude-Code-to-control-a-step-by-step-guide.en/)

### Cloud VM + Tailscale Approaches

**Domenic Denicola's setup (July 2026)**:
- Always-on desktop PC running Linux VM host
- Ubuntu Server VM with nested virtualization
- Tailscale connecting all devices (free tier)
- Claude Code and Codex CLI configured for maximum autonomy (`approval_policy: "never"`, `bypassPermissions` enabled). **⚠️ Explicit opt-in trade-off**: these settings disable all command-approval controls. Only enable after: (1) creating a dedicated non-privileged OS account with no personal data, credentials, or cloud tokens, (2) restricting network egress to only required API endpoints, (3) ensuring the VM has no access to production infrastructure, and (4) treating the VM as fully disposable — work persists only via `git push` to a remote repository. This is not a recommended default; it is a conscious risk acceptance for isolated throwaway environments.
- Git worktrees for parallel development
- "The VM is supposed to be disposable" -- work persists via GitHub (push frequently)
- [domenic.me/agentic-coding-setup](https://domenic.me/agentic-coding-setup/)

**Jakob Serlier's pragmatic notes (January 2026)**:
- Cloud VMs for running "dangerous" AI coding agents
- tmux for session management
- Focus on isolation and blast radius containment
- [jakobs.dev](https://jakobs.dev/pragmatic-notes-running-dangerous-ai-agents-cloud-vms/)

---

## 5. Community Content

### Blog Posts

| Title | Author/Source | Date | Link |
|-------|--------------|------|------|
| Remote Coding: Running AI Agents From Anywhere (The Full Stack) | Steven Gonsalvez, DEV Community | July 2026 | [source](https://dev.to/stevengonsalvez/remote-coding-running-ai-agents-from-anywhere-the-full-stack-4lji) |
| My Agentic Coding Setup, July 2026 | Domenic Denicola | July 2026 | [source](https://domenic.me/agentic-coding-setup/) |
| Long-running agent workflows: Background and Tools | Luke Turner | August 2026 | [source](https://blog.luketurner.org/posts/long-running-agent-workflows/) |
| Pragmatic Notes on Running Dangerous AI Coding Agents in Cloud VMs | Jakob Serlier | January 2026 | [source](https://jakobs.dev/pragmatic-notes-running-dangerous-ai-agents-cloud-vms/) |
| Running AI Coding Agents on Hetzner | Pere Villega | April 2026 | [source](https://perevillega.com/posts/2026-04-02-running-ai-coding-agents-on-hetzner/) |
| How to Run Claude Code as an Autonomous Agent on a Mac Mini | Claw Labs, DEV Community | 2026 | [source](https://dev.to/clawlabs/how-to-run-claude-code-as-an-autonomous-agent-on-a-mac-mini-52n8) |
| Setting Up a Spare Mac for Claude Code: The Full Remote Control Guide | Developers Digest | 2026 | [source](https://www.developersdigest.tech/blog/spare-mac-claude-code-control-guide) |
| Keep Claude Code Agent Running 24/7 | MindStudio | 2026 | [source](https://www.mindstudio.ai/blog/keep-claude-code-agent-running-24-7/) |
| How to control Claude Code from your phone (2026) | explainx.ai | August 2026 | [source](https://www.explainx.ai/blog/claude-code-mobile-remote-control-phone-guide-2026) |
| AI Agent Sandboxing in 2026 | amux.io | 2026 | [source](https://amux.io/guides/ai-agent-sandboxing/) |
| Setting Up a Remote Box for Agentic Coding | OpenReplay | July 2026 | [source](https://blog.openreplay.com/remote-box-agentic-coding-setup/) |
| AI Agent Sandbox Infrastructure in 2026 | AgentMarketCap | April 2026 | [source](https://agentmarketcap.ai/blog/2026/04/07/ai-agent-sandbox-infrastructure-e2b-modal-daytona-fly-machines-secure-code-execution) |
| Running AI Agents 24/7 in 2026: Local vs Cloud vs Managed | DeployAgents | 2026 | [source](https://www.deployagents.co/blog/running-ai-agents-24-7-in-2026-local-vs-cloud) |
| The Code Agent Orchestra | Addy Osmani | 2026 | [source](https://addyosmani.com/blog/code-agent-orchestra/) |
| Cheap VPS for AI Agents (2026) | Hermify | 2026 | [source](https://www.hermify.io/en/blog/cheap-vps-for-ai-agent) |

### YouTube Videos

| Title | Link |
|-------|------|
| How I Built a Server That Runs AI Agents 24/7 (Full Setup) | [YouTube](https://www.youtube.com/watch?v=tCEhU9bQ-XY) |
| Theo Did It. T3 Code: The FREE AI Coding Agent You Need to Try | [YouTube](https://www.youtube.com/watch?v=-7akxGb-lAM) |
| AI Agent Terminal Comparison: Tmux vs Cmux vs Herdr vs Paneflow | [YouTube](https://www.youtube.com/watch?v=gPx6rZuQg9A) |

### Hacker News Discussions

| Title | Link |
|-------|------|
| Show HN: Optio -- Orchestrate AI coding agents in K8s to go from ticket to PR | [HN](https://news.ycombinator.com/item?id=47520220) |
| How I program with agents | [HN](https://news.ycombinator.com/item?id=44221655) |
| Agent-talk: Enabling coding agents to work together | [HN](https://news.ycombinator.com/item?id=48936534) |
| Agent orchestration for the timid | [HN](https://news.ycombinator.com/item?id=46746681) |
| Eight more months of agents | [HN](https://news.ycombinator.com/item?id=46933223) |
| Can you explain why everyone thinks we should use new tools to deploy agents? | [HN](https://news.ycombinator.com/item?id=47311500) |

### GitHub Resources

| Resource | Link |
|----------|------|
| awesome-agent-orchestrators | [github.com/andyrewlee/awesome-agent-orchestrators](https://github.com/andyrewlee/awesome-agent-orchestrators) |
| agent-fleet topic | [github.com/topics/agent-fleet](https://github.com/topics/agent-fleet) |
| List of coding agent sandboxes 2026-05 | [gist by wincent](https://gist.github.com/wincent/2752d8d97727577050c043e4ff9e386e) |

### Key Comparison Articles

| Title | Source | Link |
|-------|--------|------|
| Best Tools to Run Multiple AI Coding Agents in Parallel (2026) | codeagentswarm.com | [source](https://www.codeagentswarm.com/en/guides/best-tools-to-run-multiple-ai-coding-agents) |
| Best AI Agent Multiplexers Compared (2026): 12 Tools Ranked | amux.io | [source](https://amux.io/guides/best-ai-agent-multiplexers-2026/) |
| Best Tools for Managing Parallel AI Coding Agents in 2026 | Nimbalyst | [source](https://nimbalyst.com/blog/best-agent-management-tools-2026/) |
| The 6 Best AI Agent Sandbox Platforms (August 2026) | Mastra | [source](https://mastra.ai/articles/best-ai-agent-sandbox-platforms) |
| AI Agent Deployment: Cloud Platforms Compared | Starmorph | [source](https://blog.starmorph.com/blog/ai-agent-deployment-cloud-platforms-compared) |
| Best VPS for AI Agents in 2026 | CyberNews | [source](https://cybernews.com/vps/best-vps-for-ai-agents/) |

---

## 6. Cost Analysis

### Cloud VM Providers for Agent Execution

| Provider | Instance | Specs | Monthly Cost | Notes |
|----------|----------|-------|-------------|-------|
| **Hetzner** CX22 | Shared AMD | 2 vCPU, 4 GB RAM | ~€4/mo | Cheapest viable option for single agent |
| **Hetzner** CX33 | Shared AMD | 4 vCPU, 8 GB RAM | ~€7-14/mo | Comfortable single agent with builds |
| **Hetzner** CCX33 | Dedicated | 8 vCPU, 32 GB RAM | ~€63/mo | Multiple parallel agents |
| **Hetzner** AX42 | Dedicated bare metal | 8C/16T, 64 GB DDR5 | ~€57/mo | Best price-to-performance for frequent use |
| **Hetzner** CAX (ARM) | ARM Shared | Ampere Altra | <€4/mo | EU-only, excellent efficiency |
| **Oracle Cloud** | Always Free | 4 OCPUs, 24 GB RAM | Free | ARM-only, capacity varies by region |
| **Vultr** | Entry VPS | Variable | $2.50/mo+ | GPU options (A100, H100) available |
| **DigitalOcean** | Droplet | Variable | $4/mo+ | Best managed Kubernetes for multi-agent |
| **Contabo** VPS 10 | Shared | 4 vCPU, 8 GB RAM | ~€3.60/mo | Aggressive oversubscription |
| **Hostinger** | VPS | Variable | $6.99/mo (24mo) | AI runtime templates, guided setup |

**Key findings**:
- Hetzner remains 30-50% cheaper than DigitalOcean/Linode despite April 2026 price hikes (30-37% increase)
- For API-calling agents (calling OpenAI/Anthropic APIs), the VPS is primarily an orchestration layer -- networking speed and uptime matter most
- Single Claude Code instance needs 4-8 GB RAM minimum (budget 4 GB per parallel agent)
- CPU is less critical than RAM; workloads are I/O-bound on model communication

### Agent Sandbox Services Pricing

| Service | Pricing Model | Key Metric |
|---------|--------------|------------|
| **E2B** | Pay-per-use, free tier | <200ms microVM boot |
| **Daytona** | Enterprise contracts | <60ms provisioning |
| **Modal** | Pay-per-use | gVisor containers, GPU support |
| **Fly.io Machines** | $0.0028/hour | 35+ regions, <300ms boot |
| **Fly.io Sprites** | Auto-idle billing | Persistent, 100GB NVMe, checkpoint/restore |
| **Northflank** | $0.01667/vCPU-hour | 100k concurrent sandboxes |
| **Vercel Sandbox** | Free tier (5 CPU hours) | Single region (US East) |
| **boxd** | Not disclosed | Sub-ms resume |
| **Blaxel** | Not disclosed | Zero idle cost |

### Spot/Preemptible Instance Savings

| Provider | Savings vs On-Demand | Key Trade-off |
|----------|---------------------|--------------|
| AWS Spot | Up to 90% off | 2-minute warning before termination |
| GCP Spot (Preemptible) | Up to 91% off | 30-second shutdown window |
| Azure Spot | Up to 90% off | Variable eviction rates |

**Suitability for agent workloads**: Coding agents that checkpoint state by pushing to a remote repository (commit and push frequently) can tolerate spot instance interruptions. The agent restarts on a new instance, clones, and picks up from the last pushed commit. Requires: (1) frequent `git push` not just local commits (local commits are lost when the VM is terminated), (2) a durable attached volume or remote repo, and (3) explicit restart/resume orchestration (the cloud provider does not automatically restart your agent). Not suitable for agents that need long uninterrupted sessions without checkpointing.

### Always-On vs On-Demand vs Managed

| Strategy | Cost Range | Best For |
|----------|-----------|----------|
| **Always-on VPS** (Hetzner/Vultr) | €4-63/month | Predictable, continuous agent workloads |
| **Managed hosting** | $14-55/month | Simplicity, no maintenance |
| **On-demand with suspend/resume** (Fly Sprites) | Pay only while active | Bursty agent usage, idle 95% of time |
| **Spot instances** | 60-90% off on-demand | Fault-tolerant batch agent work |
| **Spare Mac Mini M4** | One-time ~$500-800 | Heavy daily use, amortized quickly |
| **Claude Managed Agents** | Per-API-call | Zero infrastructure management |

### Cost Optimization Strategies

1. **For occasional agent use**: Oracle Cloud Always Free (4 OCPU, 24 GB ARM) or Hetzner CX22 (~€4/mo)
2. **For daily multi-agent work**: Spare Mac Mini or Hetzner AX42 bare metal (~€57/mo)
3. **For burst parallel work**: Fly.io Sprites (auto-idle billing) or spot instances
4. **For enterprise/compliance**: Northflank BYOC or Claude Managed Agents (self-hosted option)
5. **For zero maintenance**: Claude Managed Agents or Codex Cloud (bundled with ChatGPT subscription)

---

## 7. Comparison Tables

### Agent Fleet Management Tools

| Tool | Open Source | License | Remote Access | Mobile | Pricing | Platform |
|------|-----------|---------|---------------|--------|---------|----------|
| AgentsRoom | No | Proprietary | Encrypted fleet view | Yes | Paid | Cross-platform |
| Claude Squad | Yes | AGPL-3.0 | Via SSH/tmux | No | Free | Cross-platform |
| Conductor | No | Proprietary | Cloud Workspaces (Vercel Sandbox) | No | Free (BYOK) | macOS |
| T3 Code | Yes | Open source | `npx t3 connect` | Yes (iOS/Android) | Free (BYOK) | Cross-platform |
| Codeman | Yes | Open source | Web UI + tmux | Via web | Free | Cross-platform |
| Omnara | Yes | Open source | Web + mobile + watch | Yes (Apple Watch) | Unknown | Cross-platform |
| Herdr | Yes | Open source | Via SSH | No | Free | Cross-platform |
| amux | Yes | MIT | Dashboard + mobile push | Yes | Free | Cross-platform |
| Paneflow | Yes | Open source | Limited | No | Free | Linux/macOS/Windows |
| Vibe Kanban | Yes | Apache-2.0 | Local only (post-shutdown) | No | Free | Cross-platform |
| Mux (Coder) | Yes | Open source | Browser UI | No | Free | Cross-platform |
| Apra Fleet | Yes | Apache-2.0 | MCP over SSH | No | Free (BYOK) | Cross-platform |
| VibeTunnel | Yes | MIT | Browser-based | Via browser | Free | macOS/Linux |

### Agent Sandbox Platforms

| Platform | Isolation | Boot Time | Persistent | GPU | Self-Host | Pricing |
|----------|-----------|-----------|------------|-----|-----------|---------|
| E2B | Firecracker microVM | <200ms | No (ephemeral) | No | No | Pay-per-use |
| Daytona | Docker containers | <60ms | Yes | No | Was yes (now closed source) | Enterprise |
| Modal | gVisor containers | Fast | No | Yes | No | Pay-per-use |
| Fly Machines | KVM VM | <300ms | Yes | Yes | No | $0.0028/hr |
| Fly Sprites | Firecracker microVM | Seconds | Yes (100GB) | No | No | Auto-idle |
| Northflank | Kata/gVisor | Fast | Yes | Yes (H100) | BYOC/BYOK | $0.017/vCPU-hr |
| Vercel Sandbox | Firecracker microVM | Fast | No | No | No | Free tier |
| boxd | KVM microVM | 30ms boot, sub-ms resume | Yes | No | Yes (single binary) | Unknown |
| Blaxel | microVM | Fast | Yes (perpetual standby) | No | No | Unknown |
| Claude Managed Agents | gVisor container | N/A | Checkpointed | No | Split-plane (execution only) | Per-API-call |
| Codex Cloud | Cloud sandbox | N/A | Per-task | No | No | ChatGPT sub |

### Orchestration & Workflow Tools

| Tool | Scope | Self-Hosted | Agents Supported | License |
|------|-------|-------------|-----------------|---------|
| Optio | Ticket -> PR -> Merge | Yes (K8s) | Claude, Codex, Copilot, Gemini, OpenCode, Cursor | MIT |
| Hermes | Cron + persistent daemon | Yes | Model-agnostic | Open source |
| Orca | Parallel agents + remote modes | Yes | 30+ CLI agents | MIT |
| Superset | 100+ parallel agents | macOS app | Agent-agnostic | Source-available |
| Warp Factories | 6-stage pipeline | Yes (Enterprise VPC) | Claude Code, Codex, configurable | Proprietary |
| Claude Remote Control | Session bridging | Runs locally | Claude Code | Proprietary |

---

## Summary: The 2026 Remote Agent Infrastructure Stack

The landscape has stratified into clear layers:

1. **Execution layer** (where agents physically run): Fly Sprites, E2B, Modal, Daytona, Northflank, Claude Managed Agents, Codex Cloud, or DIY VPS (Hetzner/Vultr)
2. **Fleet management layer** (monitoring and controlling multiple agents): Herdr, amux, Claude Squad, T3 Code, Codeman, Omnara, AgentsRoom, Apra Fleet
3. **Workflow/orchestration layer** (ticket-to-merge automation): Optio, Warp Factories
   - **Scheduling/daemon layer**: Hermes (cron-based persistent agent, not ticket-to-merge)
4. **Access layer** (reaching your agents remotely): Tailscale + SSH/Mosh, Claude Remote Control, T3 Connect, VibeTunnel

The cheapest viable setup is **tmux + Tailscale + Hetzner CX22 (~€4/mo)** or **Oracle Cloud Always Free** for a single agent. The most polished self-hosted experience is **Orca or T3 Code with a spare Mac**. For zero-infrastructure overhead, **Claude Managed Agents** or **Codex Cloud** eliminate all infra management. For ticket-to-merge automation, **Optio on Kubernetes** is the open-source standard.

The prediction from multiple authors: "remote access gets baked into the agent tools themselves within six months." The trend is clearly toward agents running persistently on remote compute, with developers steering from phones and laptops rather than editing code directly.
