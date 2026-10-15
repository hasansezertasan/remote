# T3 Code, bb and Orca: The Agent Layer for an "AI Shed"

**Research date:** 2026-10-05
**Status:** Active research

## What Prompted This

[0004](0004-cloudtop-alternatives.md) asked how to build a personal CloudTop:
a persistent machine you reach from a thin laptop. This note asks the follow-up
question: which **agent control plane** should run on that machine? The three
candidates the user named are "t3, bb, and orca". The inspiration was a DHH
post:

> "Every developer needs an AI shed: An always-on machine running on their
> tailscale network where the majority of their herdr agents are running."
> ([DHH, X, 2026-10-04](https://x.com/dhh/status/2106755814421565495);
> 553,602 views, 7,157 likes and 577 replies when read through the
> [fxtwitter API](https://api.fxtwitter.com/dhh/status/2106755814421565495))

His own replies in the thread set out the requirements:

- "Get a good shed, and you don't even need your laptop to be all that
  powerful. … any compile work is happening on the shed anyway. And I close
  the lid without breaking the agents."
  ([reply](https://x.com/dhh/status/2106757313269060025))
- "My shed also needs to be able to compile software. That's not going to be a
  good time on a VPS." ([reply](https://x.com/dhh/status/2106793754976047204))
- "My main shed is a @Hi_MINISFORUM MS-A2, but you don't even have to spend
  that much. A @Beelinkofficial box with 8745HS or similar will also do very
  well for most things." ([reply](https://x.com/dhh/status/2106790472035549511))
- "My sheds are all API calls. But a baller shed would also have room for local
  models." ([reply](https://x.com/dhh/status/2106757520425398632))

The "AI shed" is the CloudTop idea from 0004 (F1 persistent box, F3
private-network access, F4 a thin laptop), moved onto hardware you own. DHH
uses **herdr** as the agent layer. The user is asking about three alternatives
to herdr.

### Pinning down the three names

The post doesn't name t3, bb or orca. Of the 577 replies, the fxtwitter
conversation API returned only 44, and none of those mention bb or Orca. The
names were resolved as follows.

| Name | Resolved to | Confidence | Why |
| --- | --- | --- | --- |
| **t3** | **T3 Code** ([pingdotgg/t3code](https://github.com/pingdotgg/t3code), [t3.codes](https://t3.codes)) | High | A reply in the same thread describes a shed in exactly these terms: "a mac mini m4 maxed out running 24/7 building my ios/macos/web apps from t3 code / terminal" ([al3rez](https://x.com/al3rez/status/2106770662811643946)). This repo's README and [0002](0002-remote-agent-infrastructure.md#t3-code) already track T3 Code. Its CLI is literally `t3`. |
| **orca** | **Orca** ([stablyai/orca](https://github.com/stablyai/orca), [onorca.dev](https://www.onorca.dev)) | High | The user runs Orca every day (README; this worktree sits under `~/orca/workspaces/`, and `~/Library/Application Support/orca` exists). 0004 already recommends `orca serve` on the shed. |
| **bb** | **bb**, "the agent IDE that builds itself" ([get-bb/bb](https://github.com/get-bb/bb), [getbb.app](https://getbb.app)) | Medium | It is the only "bb" in the agent-orchestrator category. It is MIT, has run since 2026-02, and was publicised by Sawyer Hood in August 2026 ([blog](https://www.sawyerhood.com/blog/an-agentic-ide-that-builds-itself)). It has a server, host daemon, mobile app and tunnel, which puts it in the same class as the other two. *I found no link to the DHH thread*, so this is an inference. The other well-known `bb` CLI, Babashka (Clojure scripting), is not an agent tool and is ruled out. |

> **Staleness flag:** all three projects ship almost every day. T3 Code had
> three nightlies on the research date, Orca released v1.4.220 on 2026-10-04,
> and bb released desktop-v0.45.0 on 2026-10-02. Every claim below comes from
> the repos as cloned on 2026-10-05: t3code `4dbc012`, bb `0e265ce`, orca
> `d173514`, herdr `c4653a4`. Expect drift within weeks.

---

## The Shed Checklist

0004's F-list scores the *machine*. Hardware, a tailnet and tmux cover F1–F4.
The question here is what runs **on** the machine. These criteria come from
DHH's replies and the 0004 setup goals (keep agents alive when the lid closes,
cope with bad cellular, drive everything from Orca or a phone):

| # | Criterion | Why it matters for a shed |
| --- | --- | --- |
| **S1** | Runs **headless** on Linux (no desktop session) | A Beelink/Minisforum box or a Hetzner AX42 has no monitor |
| **S2** | Agents survive the **client** going away (lid closed, Wi-Fi drop) | DHH: "close the lid without breaking the agents" |
| **S3** | Agents survive a **control-plane restart or update** | These apps update almost daily |
| **S4** | Starts at **boot** as a supervised service | A shed reboots after power cuts and OS updates |
| **S5** | Reachable over **Tailscale** with no vendor account | "running on their tailscale network" |
| **S6** | Optional **managed relay** (no VPN), with a clear trust model | Phone on cellular, borrowed machine |
| **S7** | **Mobile** client | Steering from the pool ([ARendtslev](https://x.com/ARendtslev/status/2106779109431144785)) |
| **S8** | **Parallel worktrees** per task | Several agents on one repo |
| **S9** | **Several sheds** from one UI | "it becomes a zoo of home machines" ([AbylayOspan](https://x.com/AbylayOspan/status/2106771980439679101)) |
| **S10** | Optional **per-task cloud sandbox** | Burst capacity beyond the shed |

None of the three survives a **host reboot** with agent processes intact. Each
documents that it restores layout and history but not the running processes
(cited per tool below). That is a property of PTYs, not of any one product.

---

## 1. T3 Code

### What it is

> "T3 Code is an 'agent harness control surface'. It enables control of the
> agents on your machine with a best-in-class mobile app (iOS, Android), web
> app and Electron-based desktop app."
> ([README](https://github.com/pingdotgg/t3code/blob/main/README.md))

- **Maker:** T3 Tools Inc (footer of [t3.codes](https://t3.codes)). The repo
  lives under Theo's `pingdotgg` org. The top committers are juliusmarminge and
  t3dotgg (`gh api`).
- **Providers:** "Claude Code, Codex, Cursor, Grok Build, OpenCode, and Google
  Antigravity", on your own subscriptions (README). Pi and ACP agents have
  their own user docs
  ([`docs/user/providers-pi.md`](https://github.com/pingdotgg/t3code/blob/main/docs/user/providers-pi.md),
  [`providers-acp.md`](https://github.com/pingdotgg/t3code/blob/main/docs/user/providers-acp.md)).
- **Pitch:** "Wait, what are you selling me? Nothing." It aims to be
  "performant, remote-ready, and truly open" (README).

### Shed fit

- **S1/S4, the strongest of the three:** a single `t3` CLI. `t3 serve` runs
  without a browser. `t3 service install` sets up a user service. "Linux needs
  systemd user services. Setup enables lingering so T3 Code starts at boot and
  keeps running after logout." On macOS the service "starts … when you log in
  and stops it when you log out. Keep the Mac logged in and awake for
  unattended remote access." Windows background services are not supported
  ([`background-service.md`](https://github.com/pingdotgg/t3code/blob/main/docs/user/background-service.md)).
- **S3:** "Restarting interrupts running agent turns, terminals, and remote
  clients, so it asks first" (`t3 update`, same doc). Updates are opt-in, not
  forced.
- **S5:** `t3 serve --tailscale-serve` or `t3 pair --tailscale` gives an
  `https://machine.tailnet.ts.net/` pairing link. Plain `t3 serve --host
  <tailnet-ip>` works too. Each device pairs with a one-time link, and admins
  can revoke sessions
  ([`remote-access.md`](https://github.com/pingdotgg/t3code/blob/main/docs/user/remote-access.md)).
- **S6 (T3 Connect):** `t3 connect` registers the machine with a T3-run relay.
  It uses Clerk for identity and Cloudflare tunnels that T3 provisions per
  environment. "After bootstrap, clients send application traffic through the
  environment's tunnel hostname; the relay Worker does not proxy their HTTP or
  WebSocket sessions." The docs spell out the trust assumption: "a compromised
  relay signing key" is not made harmless by DPoP
  ([`internals/t3-connect.md`](https://github.com/pingdotgg/t3code/blob/main/docs/internals/t3-connect.md)).
- **Multi-route:** one machine can hold LAN, Tailscale, public URL, SSH and T3
  Connect routes. Clients try "the first route that answers" and move back to
  a faster route when it recovers (`remote-access.md`). That is the best answer
  to the "unreliable Turkish cellular" problem in the README. *Inference:* it
  does at the app layer what mosh does for the terminal.
- **S9:** "Balance new threads across machines". Auto balance places new
  threads on connected machines by CPU and memory headroom. It is off by
  default (`remote-access.md`). This is the only one of the three with
  resource-aware placement.
- **Desktop-managed SSH:** the desktop app can start or reuse a server on any
  Linux or Apple Silicon host over SSH. It downloads its runtime to
  `~/.t3/runtime`, and "projects, provider credentials, and agent work stay on
  the remote machine" (`remote-access.md`).
- **S8:** "New worktree" per thread, with automatic cleanup policies (inactive
  days, merged, no commits) and recursive submodule init
  ([`project-settings.md`](https://github.com/pingdotgg/t3code/blob/main/docs/user/project-settings.md),
  [`thread-sidebar.md`](https://github.com/pingdotgg/t3code/blob/main/docs/user/thread-sidebar.md)).
- **S7:** native iOS and Android apps, plus the hosted
  [app.t3.codes](https://app.t3.codes), which "needs an HTTPS endpoint" and
  connects directly to your server (`remote-access.md`).
- **S10:** none. I found no cloud-sandbox or per-task VM provider in the user
  docs.
- **Terminal persistence:** 5,000 lines / 8 MiB of scrollback per terminal,
  kept server-side
  ([`terminal.md`](https://github.com/pingdotgg/t3code/blob/main/docs/user/terminal.md)).

### Licence, cost, maturity

- MIT. The client and server are free. T3 Connect has an account and a
  "managed tunnel limit" error (`environment_link_limit_exceeded`), but **I
  found no published quota or price** for it.
- Telemetry: the server sends product events to PostHog (provider, model,
  token totals, no prompts or content). Opt out with
  `T3CODE_TELEMETRY_ENABLED=false`
  ([`telemetry.md`](https://github.com/pingdotgg/t3code/blob/main/docs/user/telemetry.md)).
- 25.5k stars, created 2026-02-08. The latest stable is v0.0.45 (2026-10-02),
  with nightlies several times a day. The README says: "We are very very early
  in this project. Expect bugs. We are (mostly) not accepting contributions
  yet."

---

## 2. bb

### What it is

> "bb is an agentic IDE that builds itself. It can control, customize, and
> automate itself, laying the groundwork for your own software factory. Every
> surface — the desktop app, web app, CLI, and HTTP API — is a first-class way
> to drive bb." ([README](https://github.com/get-bb/bb/blob/main/README.md))

- **Makers:** "This started as a passion project by @_ymichael", with Sawyer
  Hood as an early user turned contributor
  ([Sawyer Hood, 2026-08-05](https://www.sawyerhood.com/blog/an-agentic-ide-that-builds-itself)).
  Top human committers are ymichael and SawyerHood. An account named `codex`
  has the most commits (`gh api`).
- **Pitch:** extensibility. "much of the functionality that ships with bb was
  built using the same extension system … provider agnostic workflows, the ask
  user question tool, side chat, crons, inline previews, and remote access are
  all plugins" (same post). The repo ships 40+ plugins under `plugins/`,
  including `provider-claude-code`, `provider-codex`, `provider-pi`,
  `provider-acp`, `environment-git-worktree`, `environment-modal-sandbox`,
  `keep-awake`, `push-notifications` and `automations`.
- **Providers:** "Codex, Claude Code, Cursor, etc) and any agent that supports
  ACP, on your own subscriptions" (blog).

### Architecture: the most shed-native of the three

bb separates the **server** (SQLite, HTTP API, WebSocket) from the **host
daemon** that "provisions workspaces, runs agent provider processes" on each
execution machine
([`system-overview.md`](https://github.com/get-bb/bb/blob/main/docs/system-overview.md)).
The docs describe the shed setup almost word for word:

> "Pick a computer that stays on, such as a desktop, home server, or VM: while
> the server machine is asleep or off, nothing can reach bb and running threads
> may stop."
> ([`multiple-devices.md`](https://github.com/get-bb/bb/blob/main/docs/multiple-devices.md))

### Shed fit

- **S1:** `npx bb-app@latest` starts the server and a local host daemon and
  serves the web UI on `:38886`. "If either managed child process exits
  unexpectedly, the launcher restarts that child." `npx bb-app stop` stops a
  background instance
  ([`packages/bb-app/README.md`](https://github.com/get-bb/bb/blob/main/packages/bb-app/README.md)).
  Supported hosts: macOS, Linux, WSL2, and native Windows in alpha
  ([`platform-support.md`](https://github.com/get-bb/bb/blob/main/docs/platform-support.md)).
- **S4, split:** the *Add machine* installer puts each **host daemon** under
  launchd or a systemd user service with `--auto-update`. *I found no
  first-party boot service for the **server** itself.* On a shed you would
  write your own systemd unit around `npx bb-app` (*inference*).
- **S9, the strongest of the three:** one server dispatches threads to several
  enrolled machines (`bb thread spawn --machine …`). Each machine runs its own
  versioned daemon, and one machine can serve several bb servers. An
  experimental `bb server move --to <machine>` moves the server role, for
  example from a laptop to a new shed, without starting over
  ([`server-move-plan.md`](https://github.com/get-bb/bb/blob/main/docs/server-move-plan.md);
  behind the default-off `serverMove` experiment).
- **S5:** the documented private route is Tailscale Serve to the loopback
  listener, with `BB_APP_URL` set. "Tailscale ACLs are the access boundary for
  this route" (`multiple-devices.md`). **Security caveat:** binding to
  `0.0.0.0` exposes an API that "is unauthenticated and permits command
  execution and file reads" (same doc and README). Never expose it beyond the
  tailnet.
- **S6 (bb connect):** sign the server in to a bb account, then open its
  getbb.app URL. "The server owns the tunnel and reconnects after restart."
  Connect URLs "require the paired account owner's session"
  (`multiple-devices.md`). *Inference:* `apps/connect/wrangler.jsonc` suggests
  a Cloudflare Workers relay. **No published price or quota found.** v0.45.0
  also adds "bb cloud AI" (titles, commit messages, transcription "through
  your bb account") ([CHANGELOG](https://github.com/get-bb/bb/blob/main/CHANGELOG.md)).
- **S8:** managed worktrees with `.worktreeinclude` (copies `.env` and similar
  files), plus `.bb-env-setup.sh` / `.bb-env-teardown.sh` hooks. A five-minute
  grace period applies after archiving
  ([`worktrees.md`](https://github.com/get-bb/bb/blob/main/docs/worktrees.md)).
- **S10:** the optional Modal sandbox plugin runs "reusable BB machines in
  Modal" from an editable Dockerfile. The default image includes Codex, Claude
  Code and Pi
  ([`plugins/environment-modal-sandbox/README.md`](https://github.com/get-bb/bb/blob/main/plugins/environment-modal-sandbox/README.md)).
- **S7, immature:** `platform-support.md` says the iOS app is "developer
  builds from source … No store release yet". The 0.45.0 changelog lists the
  "Android app" as alpha. The two documents disagree, and I didn't verify a
  store listing.
- **S2/S3:** threads run in the host daemon, separate from the server. The dev
  scripts restart the server and daemon independently
  (`pnpm dev:restart-server`). *Inference:* agents probably survive a server
  restart, but I found no user-facing statement that says so.

### Licence, cost, maturity

- MIT "end to end" ([getbb.app](https://getbb.app)). The app is free. Agents
  run on your own subscriptions.
- Telemetry: anonymous counts of app starts, threads, messages and plugin
  installs. Opt out with `BB_TELEMETRY=false` (README).
- 4.1k stars, created 2026-02-24. desktop-v0.45.0 shipped 2026-10-02. The
  README says: "Core architecture is stable, but workflows and surfaces are
  still evolving." It is the smallest and youngest community of the three.

---

## 3. Orca

### What it is

> "Orca is the ADE for working with a fleet of parallel agents. Run any coding
> agent with your own subscription. Available on desktop, mobile and remote
> runtime." ([GitHub description](https://github.com/stablyai/orca))

- **Maker:** Stably AI, "Backed by Y Combinator"
  ([onorca.dev footer](https://www.onorca.dev/docs/ways-to-run)). The MIT
  `LICENSE` names "Lovecast Inc." as copyright holder. *I didn't resolve how
  the two entities relate.*
- **Already in use here.** 0002 profiles it and 0004 recommends `orca serve`
  on the shed. This section only adds what matters for a shed.

### Shed fit

Orca documents four run modes
([Ways to run Orca](https://www.onorca.dev/docs/ways-to-run); source in
[`docs/site/content/docs/ways-to-run.mdx`](https://github.com/stablyai/orca/blob/main/docs/site/content/docs/ways-to-run.mdx)):
local, **SSH target**, **Remote Orca Server**, and **Cloud VM**
(per-workspace). "Orca does not sell managed VPS hosting."

- **SSH worktrees (S2):** the laptop's Orca owns the UI, and a small relay on
  the remote owns the PTYs. "Closing the desktop app no longer kills your
  remote PTY sessions." "Keep terminals alive until reset" is on by default,
  or you can set a 60 s – 7 day timeout. Ports open on the remote are detected
  and can be forwarded with one click. Remote terminals need `make`, a C++
  compiler and `python3` on Linux so the relay can build `node-pty`
  ([SSH worktrees](https://www.onorca.dev/docs/ssh)).
- **Remote Orca Server (S1/S2/S7):** "The server keeps the projects,
  worktrees, terminals, tabs, provider accounts, and agent sessions." Laptop,
  browser and phone pair to it. The recommended path is "desktop app +
  Tailscale". Headless hosts use `orca serve --pairing-address <tailnet-ip>` (`orca-ide serve` on Linux),
  with `--mobile-pairing` for a phone QR. Each client gets "a separate,
  revocable token". **Marked "Beta"**
  ([Remote Orca Servers](https://www.onorca.dev/docs/remote-servers)).
- **S4, weakest of the three:** `orca serve` "Runs in the foreground … Stop it
  with Ctrl+C"
  ([`src/cli/specs/serve.ts`](https://github.com/stablyai/orca/blob/main/src/cli/specs/serve.ts)).
  The docs say a headless host needs "Terminal command and service
  configuration". There is no `orca service install`, so you write the systemd
  unit yourself.
- **S3:** locally, "A background daemon owns the PTYs". Agents keep running
  through Cmd-Q, auto-updater relaunches and app crashes. A host reboot ends
  every agent, but layout and scrollback come back
  ([Session restore](https://github.com/stablyai/orca/blob/main/docs/site/content/docs/model/session-restore.mdx)).
- **S6 (Orca Relay, mobile only):** "Phones and desktops never talk to each
  other directly: each opens an outbound WebSocket to a relay cell". Push goes
  through a separate gateway, and "sign-in is required for Relay only"
  ([`cloud/README.md`](https://github.com/stablyai/orca/blob/main/cloud/README.md),
  [Mobile](https://github.com/stablyai/orca/blob/main/docs/site/content/docs/mobile.mdx)).
  Desktop-to-server pairing still needs a network path you provide
  (Tailscale, LAN, SSH forward).
- **S8:** the core primitive ("each in its own worktree", README). It also
  ships worktree checkpoints, race-three-agents recipes and experimental
  **agent hibernation**, which stops idle "done" agents after 30 minutes and
  resumes them when the worktree reopens
  ([hibernation](https://github.com/stablyai/orca/blob/main/docs/site/content/docs/agents/hibernation.mdx)).
  On a RAM-limited shed this matters (0002 assumes about 4 GB per agent).
- **S9:** several SSH targets and several paired servers from one client. Mobile
  "with multiple connected desktops" asks which host should create a new
  workspace (Mobile docs). There is no load balancing.
- **S10:** Cloud VM recipes (`orca.yaml` + lifecycle scripts) for Vercel
  Sandbox, Fly, Modal, SSH hosts or Docker, with your own provider account. The
  feature sits under **Settings → Experimental** (Ways to run).

### Licence, cost, maturity

- MIT, free, no account system ("Orca has no account system", except the
  optional Relay sign-in). Telemetry is anonymous with no content. Opt out in
  Settings, or with `DO_NOT_TRACK=1` / `ORCA_TELEMETRY_DISABLED=1`
  ([Privacy & Telemetry](https://github.com/stablyai/orca/blob/main/docs/site/content/docs/telemetry.mdx)).
- 85.7k stars, created 2026-03-17. v1.4.220 shipped 2026-10-04. It is the most
  adopted and has the highest version number, but the two shed-critical modes
  are still flagged Beta (Remote Server) and Experimental (Cloud VM).

---

## Context: herdr (the tool DHH actually named)

Covered in [0002](0002-remote-agent-infrastructure.md#herdr-herder). Updates:

- The repo moved to [herdrdev/herdr](https://github.com/herdrdev/herdr)
  (Apache-2.0, 42.5k stars, v0.9.3 on 2026-09-29). It is "one rust binary, no
  electron", a terminal-native agent multiplexer, with "several machines, one
  window" over saved SSH machines
  ([README](https://github.com/herdrdev/herdr/blob/main/README.md)).
- It is honest about reboots: "after a server or machine restart, herdr
  restores the saved layout and can resume supported agent sessions; the
  original processes do not survive" (README).
- Omarchy (DHH's Linux distro) binds it to `Super + Ctrl + Return` and can sync
  themes across `herdr machine list` targets over SSH
  ([manual](https://github.com/basecamp/omarchy/blob/quattro/manual/21-tuis.md),
  [theming](https://github.com/basecamp/omarchy/blob/quattro/docs/theming.md)).
  The manual links the fork `omacom/herdr`, not upstream.

**The real split:** herdr is a **terminal** layer (TUI, SSH in, no GUI, no
phone app). T3 Code, bb and Orca are **GUI/mobile** layers with a server on
the shed. They can stack: Orca or T3 can drive agents on a shed where herdr
also runs. *Inference: I didn't test them side by side.*

---

## Comparison

Legend: **Y** = documented and shipped · **P** = partial, beta,
experimental or DIY · **—** = no.

| | T3 Code | bb | Orca | herdr (reference) |
| --- | --- | --- | --- | --- |
| Shape | Server + desktop/web/mobile clients | Server + host daemons + desktop/web/CLI/API | Desktop app (+ `orca serve`) + mobile | Terminal multiplexer (TUI) |
| **S1** Headless Linux | Y (`t3 serve`) | Y (`npx bb-app`) | Y (`orca serve`) | Y |
| **S2** Survives client disconnect | Y | Y | Y (SSH relay leases; Remote Server) | Y |
| **S3** Survives control-plane restart | — (update asks first, then interrupts turns) | P (separate daemon; *not documented*) | Y locally (PTY daemon survives quit/update/crash) | Y (client detach) |
| **S4** Boot service | **Y** (`t3 service install`, systemd linger) | P (daemons Y; server DIY) | — (foreground; DIY unit) | P (*not checked*) |
| **S5** Tailscale, no account | Y (`--tailscale-serve`) | Y (Tailscale Serve + ACLs) | Y (recommended path) | Y (SSH) |
| **S6** Managed relay | Y (T3 Connect: Clerk + Cloudflare tunnels) | Y (bb connect) | P (Orca Relay, mobile only) | — |
| **S7** Mobile | Y (iOS + Android, store) | P (iOS dev builds; Android alpha) | Y (iOS store; Android APK) | — |
| **S8** Worktrees | Y | Y (+ `.worktreeinclude`, setup/teardown) | Y (core; + hibernation) | P (panes; worktrees up to you) |
| **S9** Several sheds | **Y** (routes + resource-aware load balancing) | **Y** (one server, many machines; server move) | Y (many SSH targets / servers) | Y (SSH machines) |
| **S10** Cloud sandbox per task | — | P (Modal plugin) | P (Cloud VM recipes, experimental) | — |
| Licence | MIT | MIT | MIT | Apache-2.0 |
| Telemetry opt-out | `T3CODE_TELEMETRY_ENABLED=false` | `BB_TELEMETRY=false` | `ORCA_TELEMETRY_DISABLED=1` / `DO_NOT_TRACK=1` | *not checked* |
| Stars · created · latest | 25.5k · 2026-02 · v0.0.45 | 4.1k · 2026-02 · desktop-v0.45.0 | 85.7k · 2026-03 · v1.4.220 | 42.5k · 2026-03 · v0.9.3 |
| Self-described maturity | "very very early … Expect bugs" | "active development" | Remote Server "Beta" | stable releases |

### How this relates to CloudTop and 0004

- **None of the three replaces anything in 0004.** They are not
  workstations. They sit on top of one. In 0004's matrix they fill **F5**
  ("browser/remote IDE wired to the VM"). For agent work, that IDE is now an
  agent cockpit rather than Cider V or code-server.
- **F1 (persistence) is still the shed's job.** All three lose agent
  processes on a host reboot. A UPS ([closetheloopdev](https://x.com/closetheloopdev/status/2106768399678177381))
  and resumable sessions matter more than the choice of tool.
- **F3 (zero trust)** maps to Tailscale for all three. Their managed relays
  (T3 Connect, bb connect, Orca Relay) are each a **third-party trust anchor**,
  in the same way Microsoft dev tunnels are for `code tunnel` in 0004. Keep
  them off where the tailnet is enough.
- **F7 (fleet management):** the closest any of them gets is T3 Code's load
  balancing and bb's machine enrolment with self-updating daemons. Neither
  provisions machines. That is still Coder or a fleet repo (0004 §2a, §1b).
- **DHH vs 0004 on VPS:** DHH rejects a VPS for compile work. 0004's top pick
  is a **dedicated** Hetzner AX42, not a shared-vCPU VPS. *Inference:* his
  objection (noisy neighbours, which Grok's reply in the thread also names) is
  about shared VPS cores and doesn't apply to dedicated hardware. A mini PC at
  home and a rented dedicated box are both valid sheds.

---

## Recommendation for This Setup

The goal hasn't changed since 0004: get Claude Code, Codex and OpenCode off a
hot MacBook, keep them alive on bad cellular, and drive them from Orca and a
phone.

### Keep Orca as the cockpit, and fix its one shed gap

The user already lives in Orca, and it covers S1, S2, S5, S7 and S8 with the
largest community. On the shed:

1. Run `orca-ide serve --port 6768 --pairing-address <tailnet-ip>` under a
   **hand-written systemd user unit with linger** (`loginctl enable-linger`).
   This fills the S4 gap: `orca serve` only runs in the foreground. On Linux
   the CLI is `orca-ide`, because GNOME Orca's screen reader owns
   `/usr/bin/orca`. The bare `orca` shim exists only while the server is
   starting, so it can't be the unit's `ExecStart`. Use `orca-ide` or the
   absolute AppImage path
   ([Remote Orca Servers](https://github.com/stablyai/orca/blob/main/docs/site/content/docs/remote-servers.mdx)).
2. Pair the MacBook as a client (Remote Orca Server), and the phone with
   `--mobile-pairing`.
3. Use SSH worktrees as the fallback when the Remote Server beta misbehaves.
   Their relay already keeps PTYs alive with no timeout.
4. Turn on agent hibernation so finished agents release RAM.

### Add T3 Code on the same shed as the second surface (optional, cheap)

T3 Code is the best-engineered **headless service** of the three
(`t3 service install` with linger) and has the best **mobile/network story**
(store apps on both platforms, multi-route failover, resource-aware placement
across sheds). Both are free, MIT, and run the same provider CLIs on the same
box. Running `t3 service install` next to `orca serve` is a low-risk way to
compare them on a phone over cellular. Don't make it primary yet: v0.0.x,
"Expect bugs", and turns get interrupted on update.

### Watch bb, don't adopt it yet

bb has the most **shed-native architecture**: one server, many machines,
self-updating daemons and a server-move path. It is the right model once
there are several sheds, for example a home mini PC plus a Hetzner box plus
Modal for bursts. But it has the smallest community, no store-shipped mobile
app, an unauthenticated API if misbound, and no server boot service. Look
again when server move and mobile leave experimental and alpha.

### And herdr

If the shed is also a place you SSH into from a terminal, herdr is the
terminal-native complement, as on DHH's own setup and in Omarchy. It doesn't
replace a GUI cockpit for phone-driven work.

---

## Unverified / Open Questions

- **"bb" is an inference.** No reply returned by the API mentions it (44 of
  577 replies were retrievable), and I didn't read the 181 quote-posts. If the
  user meant a different "bb", only §2 changes.
- **T3 Connect and bb connect pricing and quotas** aren't published in the
  repos or on the landing pages. T3 has a "managed tunnel limit" error whose
  value isn't documented.
- **bb:** whether agent processes survive a server-only restart (inferred from
  the separate daemon, not stated). The mobile distribution status conflicts
  between `platform-support.md` (no store release) and CHANGELOG 0.45.0
  (Android alpha).
- **Orca:** how Stably AI (site footer) relates to Lovecast Inc. (LICENSE
  holder).
- **herdr:** boot-service support and telemetry weren't checked. They are out
  of scope.
- **Corrections to 0002:** its T3 Code entry uses `npx t3 connect` and "BYOK
  (bring your own API keys)". Current docs use `t3 connect` and provider
  *subscriptions*. Its herdr link points to `herdr/herdr`, and the repo is now
  `herdrdev/herdr` (42.5k stars, not about 15k).
- Hardware prices for the MS-A2 and Beelink 8745HS boxes DHH mentions weren't
  captured.

---

## Sources

**Prompt**
- DHH, "Every developer needs an AI shed…", X, 2026-10-04 ([post](https://x.com/dhh/status/2106755814421565495)); replies [2106757313269060025](https://x.com/dhh/status/2106757313269060025), [2106793754976047204](https://x.com/dhh/status/2106793754976047204), [2106790472035549511](https://x.com/dhh/status/2106790472035549511), [2106757520425398632](https://x.com/dhh/status/2106757520425398632); thread replies from [al3rez](https://x.com/al3rez/status/2106770662811643946), [ARendtslev](https://x.com/ARendtslev/status/2106779109431144785), [AbylayOspan](https://x.com/AbylayOspan/status/2106771980439679101), [closetheloopdev](https://x.com/closetheloopdev/status/2106768399678177381). Read via the [fxtwitter API](https://api.fxtwitter.com/2/conversation/2106755814421565495).

**T3 Code** (cloned at `4dbc012`, 2026-10-05)
- [pingdotgg/t3code](https://github.com/pingdotgg/t3code): [README](https://github.com/pingdotgg/t3code/blob/main/README.md) · [remote-access](https://github.com/pingdotgg/t3code/blob/main/docs/user/remote-access.md) · [background-service](https://github.com/pingdotgg/t3code/blob/main/docs/user/background-service.md) · [t3-connect internals](https://github.com/pingdotgg/t3code/blob/main/docs/internals/t3-connect.md) · [project-settings](https://github.com/pingdotgg/t3code/blob/main/docs/user/project-settings.md) · [terminal](https://github.com/pingdotgg/t3code/blob/main/docs/user/terminal.md) · [telemetry](https://github.com/pingdotgg/t3code/blob/main/docs/user/telemetry.md)
- [t3.codes](https://t3.codes) · [app.t3.codes](https://app.t3.codes)

**bb** (cloned at `0e265ce`, 2026-10-05)
- [get-bb/bb](https://github.com/get-bb/bb): [README](https://github.com/get-bb/bb/blob/main/README.md) · [system-overview](https://github.com/get-bb/bb/blob/main/docs/system-overview.md) · [multiple-devices](https://github.com/get-bb/bb/blob/main/docs/multiple-devices.md) · [worktrees](https://github.com/get-bb/bb/blob/main/docs/worktrees.md) · [platform-support](https://github.com/get-bb/bb/blob/main/docs/platform-support.md) · [server-move-plan](https://github.com/get-bb/bb/blob/main/docs/server-move-plan.md) · [bb-app README](https://github.com/get-bb/bb/blob/main/packages/bb-app/README.md) · [Modal sandbox plugin](https://github.com/get-bb/bb/blob/main/plugins/environment-modal-sandbox/README.md) · [CHANGELOG](https://github.com/get-bb/bb/blob/main/CHANGELOG.md)
- [getbb.app](https://getbb.app) · Sawyer Hood, "An Agentic IDE That Builds Itself", 2026-08-05 ([blog](https://www.sawyerhood.com/blog/an-agentic-ide-that-builds-itself))

**Orca** (cloned at `d173514`, 2026-10-05)
- [stablyai/orca](https://github.com/stablyai/orca): [README](https://github.com/stablyai/orca/blob/main/README.md) · [`orca serve` spec](https://github.com/stablyai/orca/blob/main/src/cli/specs/serve.ts) · [cloud/README (Orca Relay)](https://github.com/stablyai/orca/blob/main/cloud/README.md) · docs source: [session-restore](https://github.com/stablyai/orca/blob/main/docs/site/content/docs/model/session-restore.mdx), [hibernation](https://github.com/stablyai/orca/blob/main/docs/site/content/docs/agents/hibernation.mdx), [mobile](https://github.com/stablyai/orca/blob/main/docs/site/content/docs/mobile.mdx), [telemetry](https://github.com/stablyai/orca/blob/main/docs/site/content/docs/telemetry.mdx)
- Published docs: [Ways to run Orca](https://www.onorca.dev/docs/ways-to-run) · [SSH worktrees](https://www.onorca.dev/docs/ssh) · [Remote Orca Servers](https://www.onorca.dev/docs/remote-servers)

**herdr / Omarchy**
- [herdrdev/herdr](https://github.com/herdrdev/herdr) (cloned at `c4653a4`) · [herdr.dev](https://herdr.dev)
- [basecamp/omarchy](https://github.com/basecamp/omarchy): [manual/21-tuis.md](https://github.com/basecamp/omarchy/blob/quattro/manual/21-tuis.md) · [docs/theming.md](https://github.com/basecamp/omarchy/blob/quattro/docs/theming.md)

GitHub star counts, licences, creation dates, releases and top contributors
come from `gh api` on 2026-10-05. Everything else comes from local clones of the
repos at the commits listed.
