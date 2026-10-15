# CloudTop for Non-Googlers: Persistent Cloud Workstations You Can Actually Use

**Research date:** 2026-10-05
**Status:** Active research

## What Prompted This

A friend at Google uses CloudTop. The trigger was
[HN comment 34933249](https://news.ycombinator.com/item?id=34933249)
(Galanwe, 2023-02-25), which quotes Ars Technica:

> "Google has an internal-only virtual desktop tool for thin clients called
> 'CloudTop' that it would like employees to transition to."
> ([Ron Amadeo, Ars Technica, 2023-02-24](https://arstechnica.com/gadgets/2023/02/google-has-gotten-so-cheap-employees-now-have-to-share-desks/),
> read via [Wayback](https://web.archive.org/web/2023/https://arstechnica.com/gadgets/2023/02/google-has-gotten-so-cheap-employees-now-have-to-share-desks/))

The Ars story is about desk sharing ("Cloud Office Evolution"). It says
physical workstations would no longer sit under desks, so engineers would be
pushed toward CloudTop. The HN replies, written by people who used CloudTop,
describe the product better than the article does. The best public primary
source on how it works is Google's own ACM Queue paper,
[*From Corp to Cloud: Google's Virtual Desktops*](https://storage.googleapis.com/gweb-research2023-media/pubtools/4484.pdf)
(Fata, Arida, Hahn, Beyer; ACM Queue, May–June 2018;
[Queue listing](https://queue.acm.org/detail.cfm?id=3264508)). The paper never
uses the name "CloudTop". It calls the product "cloud desktop" and "virtual
desktops". Treating the two as the same product is an inference: the Ars
description ("virtual desktop tool for thin clients") and the HN descriptions
match the paper.

> **Staleness flag:** the paper is from 2018 and the HN thread from 2023.
> Nothing public that I found says what CloudTop looks like inside Google in
> 2026. Read the feature list below as the design that is publicly documented,
> not the current internal state.

---

## What CloudTop Is

A **persistent, per-engineer Linux VM in Google's datacenter**, running the
corporate Debian-based distro (gLinux). A laptop or thin client reaches it from
anywhere. You do your real work there (editing, building, testing). The laptop
just carries the screen and keyboard.

From the paper:

- "Over one-fourth of Googlers use internal, datacenter-hosted virtual desktops
  … allows users to develop code, access internal resources, and use GUI tools
  remotely from anywhere in the world."
- "A virtual desktop instance can be sized according to the task at hand, has
  persistent user storage, and can be moved between corporate data centers to
  follow traveling Googlers."
- Originally ran on [Ganeti](http://www.ganeti.org/). Migrated to GCE from
  2016, moving about 20,000 users. The team reported a 95% drop in pager load.

From people who use it (HN replies):

- "Run a mbp as your carry around, and then everything else runs on an
  overbuilt server vm with a direct connection to shared storage. I'm able to
  sync to head + rebuild 100k+ files in < 5 mins"
  ([thorncorona](https://news.ycombinator.com/item?id=34933429)).
- "More like a super well connected SSH client … Development on an internal
  fork of VS code (web page) which sets up all the build tooling for you to use
  your cloud top as your build eng. Includes ML based completion"
  ([thorncorona](https://news.ycombinator.com/item?id=34933609)).
- "The vast majority of people use their cloudtop over ssh and a remote vscode
  connection. I only ever use a remote gui for a few minutes every X weeks"
  ([cosentiyes](https://news.ycombinator.com/item?id=34943528)).
- Works "perfectly when on coffee shop wifi"
  ([thorncorona](https://news.ycombinator.com/item?id=34933571)).

The "internal fork of VS Code" is Cider V. Laurent Le Brun, on Google's IDE
team 2020–2024, says Cider V adopted the VS Code frontend in 2020 and that "by
2023, 80% of the development in the main Google codebase happened in Cider V"
([A History of IDEs at Google, 2026-05-09](https://laurent.le-brun.eu/blog/a-history-of-ides-at-google)).

### Defining feature list

These are the features a replacement is scored against in the matrix below.

| # | Feature | Source |
| --- | --- | --- |
| **F1** | **Persistent personal VM.** Always on. Disk and home directory survive disconnects and restarts. | Paper ("persistent user storage") |
| **F2** | **Big, right-sized compute.** You choose the size, within cost caps. Specs are much larger than a laptop. | Paper ("sized according to the task", "large increases in standard CPU, RAM, and disk specs", caps "to prevent users from gratuitously creating high-specification/high-cost devices") |
| **F3** | **Zero-trust access from anywhere.** No VPN and no public IP. Device and user identity go through BeyondCorp/IAP, egress goes through NAT, and internal DNS is proxied. | Paper (BeyondCorp, instance-identity JWT to client cert, NAT gateways, in-VM DNS proxy) |
| **F4** | **Terminal-first access** (SSH, tmux) that tolerates bad networks. | HN ([cosentiyes](https://news.ycombinator.com/item?id=34943528), [surajrmal](https://news.ycombinator.com/item?id=34933605)) |
| **F5** | **Browser/remote IDE wired to the VM.** The IDE uses the VM as its build engine and includes AI completion. | HN ([thorncorona](https://news.ycombinator.com/item?id=34933609)), [Le Brun](https://laurent.le-brun.eu/blog/a-history-of-ides-at-google) |
| **F6** | **Optional full GUI desktop** for the rare GUI task, using Chrome Remote Desktop. | Paper ("use GUI tools remotely"). The CRD detail comes from HN ([surajrmal](https://news.ycombinator.com/item?id=34933605): "chrome remote desktop … is quite laggy") |
| **F7** | **Managed fleet.** Golden image, central provisioning from a request, inventory, policy-driven config, central logging. Disposable instances you can "trade in". | Paper (provisioning, gLinux image import, "trade-in" program) |
| **F8** | **Region placement** near the user, with relocation when they travel. | Paper |
| **F9** | **Fast monorepo and build backend.** Shared storage, CitC/Piper, Blaze/Forge. This is what makes "sync to head + rebuild in < 5 min" possible. | HN ([thorncorona](https://news.ycombinator.com/item?id=34933429)). Google's monorepo paper ([research.google](https://research.google/pubs/why-google-stores-billions-of-lines-of-code-in-a-single-repository/)) has the CitC details. *I couldn't read the full text (the ACM site blocked access). Unverified beyond the HN claim.* |

Known downsides reported by users (HN): the GUI feels laggy over remote
desktop ([Galanwe](https://news.ycombinator.com/item?id=34933886)), build speed
varies with noisy-neighbor VMs, and the file cache is cold every morning
([surajrmal](https://news.ycombinator.com/item?id=34933605)).

**Key takeaway for this repo:** people who like CloudTop use it as an
**always-on SSH box with a remote VS Code**, not as a streamed desktop. That
fits agent workloads directly. An agent needs a persistent machine you can
reach from anywhere, not pixels.

F9 (Piper/CitC/Blaze) is the one part that is genuinely Google-specific. It is
also mostly irrelevant for normal-sized repos.

---

## Candidate Solutions

Legend: **Y** = covers it · **P** = partial or needs add-ons · **—** = no.
Cross-references point to earlier notes where a tool is already covered, so it
isn't re-researched here.

### 1. DIY (guides and setup scripts)

#### 1a. GCE VM + Chrome Remote Desktop + IAP (the literal recreation)

This is the closest copy of CloudTop: Google's own public building blocks.

- **Guide:** [Set up Chrome Remote Desktop for Linux on Compute Engine](https://docs.cloud.google.com/architecture/chrome-desktop-remote-on-compute-engine)
  (official, updated 2025-05-12). It installs Debian, a desktop environment
  (Xfce is recommended for slow links), and the CRD package. The VM needs
  internet "either with an external IP address or through Cloud NAT", so it
  can run without a public IP, as CloudTop does.
- **Zero-trust SSH:** [IAP TCP forwarding](https://docs.cloud.google.com/iap/docs/using-tcp-forwarding)
  (updated 2026-09-30) "lets you establish an encrypted tunnel over which you
  can forward SSH, RDP, and other traffic to VM instances". This is the same
  BeyondCorp/IAP idea the paper describes (F3).
- **Golden image:** the paper itself points at GCE
  [custom images](https://cloud.google.com/compute/docs/images#custom_images).
  It says "what Google has implemented here should be directly applicable to
  other, smaller companies".
- **Cost:** plain Compute Engine rates. Spot or committed-use discounts apply.
  See the [0002 cost tables](0002-remote-agent-infrastructure.md#6-cost-analysis).
- **Gaps:** no fleet tooling (F7). You script it yourself.

#### 1b. Any VPS + Tailscale + tmux/mosh (what this repo already leans toward)

Already covered in
[0002 §4 DIY Patterns](0002-remote-agent-infrastructure.md#4-diy-patterns)
(tmux + Tailscale + SSH/Mosh, the Domenic Denicola and Jakob Serlier setups)
and in 0001
[SSH into a Cloud VM](0001-remote-dev-environments.md#ssh-into-a-cloud-vm-diy).
What's new here is the CloudTop framing:

- **F3 via Tailscale:** [Install Tailscale with cloud-init](https://tailscale.com/kb/1293/cloud-init)
  joins the VM to your tailnet on first boot (`tailscale up --auth-key=…`,
  optional `tailscale set --ssh`). The VM needs no public SSH port. Tailscale's
  identity-bound ACLs are a personal-scale BeyondCorp.
  [Free Personal plan](https://tailscale.com/pricing): 6 users, unlimited user
  devices, Tailscale SSH limited to 5 hosts.
- **F4:** tmux plus [mosh](https://github.com/mobile-shell/mosh) (GPL-3.0,
  14.5k stars, last push 2026-03) or
  [Eternal Terminal](https://github.com/MisterTea/EternalTerminal) (Apache-2.0,
  3.9k stars, active 2026-10). Both handle flaky cellular, which the README
  lists as a pain point.
- **F5:** [VS Code Remote Tunnels](https://code.visualstudio.com/docs/remote/tunnels)
  (`code tunnel`). It makes outbound connections through Microsoft dev
  tunnels, needs no SSH or public IP, and opens at
  `vscode.dev/tunnel/<machine>`. Limit: 10 tunnels per account. You can also
  self-host [code-server](https://github.com/coder/code-server) (MIT, 79.5k
  stars) or [openvscode-server](https://github.com/gitpod-io/openvscode-server)
  (MIT, 6.2k stars). Both are active as of Sep–Oct 2026.
- **F7, for one person:** Theo's fleet-repo pattern plus a `provision-a-box`
  skill ([0002](0002-remote-agent-infrastructure.md#theos-fleet-repo-pattern))
  is the individual-scale version of Google's provisioning pipeline.
- **Setup-script repos:** GitHub search turned up only tiny personal repos,
  for example [manishtrouble/myvmkit](https://github.com/manishtrouble/myvmkit)
  ("Hetzner devbox provisioning with Terraform, cloud-init, Tailscale-only
  SSH", 0 stars, 2026-07). None is mature enough to recommend. The
  [homelab](https://github.com/hasansezertasan/homelab) bootstrap already in
  this repo's README is a better base to port to Linux.

### 2. Deployables (Docker / Helm / one-click templates)

#### 2a. Coder (self-hosted CDE control plane)

Already profiled in
[0001](0001-remote-dev-environments.md#coder-open-source). Details relevant to
CloudTop:

- **F1/F7:** the official
  [`gcp-linux` template](https://github.com/coder/registry/tree/main/registry/coder/templates/gcp-linux)
  provisions "GCP VM (ephemeral)" plus "GCP Disk (persistent, mounted to root)",
  and "the full filesystem is preserved when the workspace restarts". Matching
  templates exist for `aws-linux`, `azure-linux`, `digitalocean-linux`,
  `docker`, `kubernetes` and `incus`. Templates are Terraform, which works as a
  golden image plus provisioning policy, close to the paper's design.
- **F3:** Coder's networking is built on
  [Tailscale's open-source WireGuard stack](https://github.com/coder/coder/blob/main/docs/admin/networking/index.md)
  and can use Tailscale's DERP relays.
- **F5/F6:** registry modules for `code-server`, `vscode-web`, `jetbrains`,
  `zed` and `cursor`, plus a
  [`kasmvnc`](https://github.com/coder/registry/tree/main/registry/coder/modules/kasmvnc)
  desktop module.
- **Agents:** modules for `claude-code`, plus `coder-labs/codex` and
  `coder-labs/opencode`. Also Coder Agents (the agent loop runs in the control
  plane) and Agent Relay (Claude Code and Cursor cloud sessions execute in your
  workspaces)
  ([docs/ai-coder](https://github.com/coder/coder/blob/main/docs/ai-coder/index.md)).
- **Deploy:** Docker, Helm/Kubernetes, OpenShift, RPM, plus community
  one-click targets: [Railway](https://railway.com/deploy/coder),
  [Fly.io](https://coder.com/blog/remote-developer-environments-on-fly-io)
  and [Render](https://github.com/coder/packages/blob/main/render/README.md)
  ([community installs](https://github.com/coder/coder/blob/main/docs/install/server/community.md)).
- **License and pricing:** AGPL-3.0, 16.8k stars, pushed 2026-10-05.
  [Community edition is free](https://coder.com/pricing) (unlimited
  workspaces, up to 5 active Coder Agents). Premium is per-user annual pricing.

#### 2b. Browser-streamed Linux desktop images (F6 only)

| Project | What | License / activity | Notes |
| --- | --- | --- | --- |
| [linuxserver/docker-webtop](https://github.com/linuxserver/docker-webtop) | Full desktop (XFCE/KDE/i3; Alpine/Ubuntu/Fedora/Arch/Debian) in the browser | GPL-3.0 · 4.4k stars · active 2026-10 | Now built on Selkies and served on `:3001` over HTTPS. "By default there is **no authentication**", and the GUI includes "a terminal with passwordless `sudo`". Put it behind Tailscale or an auth proxy. |
| [selkies-project/selkies](https://github.com/selkies-project/selkies) | Low-latency GPU/CPU-accelerated HTML5 desktop streaming for Docker, K8s and HPC | MPL-2.0 · 2.3k stars · active 2026-10 | "Started out first by Google engineers" (README). The most CloudTop-lineage option on this list. |
| [KasmVNC](https://github.com/kasmtech/KasmVNC) / [Kasm Workspaces](https://kasm.com/community-edition) | Web VNC server / container-desktop platform | KasmVNC GPL-2.0 · 5.3k stars | Workspaces Community Edition is free but limited to "5 Concurrent Sessions" and **non-commercial use**. Commercial tier pricing not verified (pricing page 404 on 2026-10-05). |
| [m1k1o/neko](https://github.com/m1k1o/neko) | WebRTC virtual browser/desktop in Docker | Apache-2.0 · 22.5k stars · active | Better for a shared remote browser than for a dev desktop. |
| [Apache Guacamole](https://github.com/apache/guacamole-server) | Clientless HTML5 gateway for SSH/RDP/VNC | Apache-2.0 · 4.0k stars · active | A single web entry point to several boxes. |
| [xrdp](https://github.com/neutrinolabs/xrdp) | RDP server for Linux | Apache-2.0 · 6.7k stars · active | Use with any RDP client over Tailscale. |

### 3. Paid services

#### 3a. Google Cloud Workstations (the public relative)

Covered briefly in
[0001](0001-remote-dev-environments.md#google-cloud-workstations). Feature
check against CloudTop
([overview](https://docs.cloud.google.com/workstations/docs/overview), updated
2026-09-30):

- Persistent home directory on a persistent disk.
- Custom machine types and GPUs.
- Custom container images and startup scripts.
- Code OSS in the browser, JetBrains IDEs, or local VS Code over SSH.
- Private clusters, VPC Service Controls, IAM and IAP.
- Idle and running timeouts.
- Gemini Code Assist in the editor (a Cider-like AI completion).

I found no Google statement that Cloud Workstations *is* CloudTop. Treat it as
a separate product built on the same GCE and IAP building blocks.

**Pricing** ([pricing page](https://cloud.google.com/workstations/pricing)):

- Compute Engine and disk at normal rates.
- Management fee of $0.05 per vCPU per hour while running.
- **Control plane of $0.20 per cluster per hour, charged even when idle.**
  That alone is about $146/month.
- Combined compute plus management in us-central1: e2-standard-4 at
  $0.334/hr, e2-standard-8 at $0.668/hr.

Example: one always-on e2-standard-8 for agents comes to about $488 compute
plus $146 control plane, so **about $634/month before disk**. That's poor value
for one person. It makes sense for a team sharing one cluster.

#### 3b. exe.dev (persistent agent-friendly VMs)

From David Crawshaw, Tailscale co-founder and former CTO, now exe.dev CEO.
Launched in developer preview on 2025-12-15
([launch post](https://blog.exe.dev/meet-exe.dev)).

- **Features:** `ssh exe.dev new` gives you a real KVM VM with root, a
  persistent disk, and a private-by-default HTTPS URL. Sharing is through IAM.
  Includes a built-in web agent ("Shelley") and bring-your-own LLM
  subscription ([exe.dev](https://exe.dev)).
- **Pricing** ([pricing](https://exe.dev/pricing)): Personal is **$15/month**
  for a 2 vCPU / 4 GB pool, scalable up to 16 vCPU / 32 GB, with up to 50 VMs
  and 100 GB disk per 2 vCPU. Work is from $0.21/hr ($150/month minimum),
  scales up to 512 vCPU, and adds SSO. Extra disk costs $0.08/GB/month.
  I didn't capture the exact price at larger Personal pool sizes.
- **Fit:** covers F1, F3 (private by default, SSH-native) and F4 with no
  sysadmin work. A 2 vCPU / 4 GB pool holds about one agent under 0002's
  4 GB-per-agent rule of thumb.

#### 3c. Others (mostly poor fits)

| Service | CloudTop fit | Why not the pick |
| --- | --- | --- |
| **GitHub Codespaces** ([0001](0001-remote-dev-environments.md#github-codespaces)) | F1 partial, F5 | Idle timeout is 5–240 min, and "regardless of your idle timeout setting, a codespace has a maximum lifetime of 12 hours" ([docs](https://docs.github.com/en/codespaces/setting-your-user-preferences/setting-your-timeout-period-for-github-codespaces)). That kills long-running agents and Hermes cron. |
| **AWS WorkSpaces Personal** | F1, F2, F6 | A GUI-first desktop service. Linux images: Ubuntu 22.04/24.04, Rocky 8/9, RHEL 8/9, over the DCV protocol. Amazon Linux 2 reached end of life 2026-06-30 ([bundles doc](https://docs.aws.amazon.com/workspaces/latest/adminguide/amazon-workspaces-bundles.html)). Prices are rendered by JavaScript on the [pricing page](https://aws.amazon.com/workspaces/pricing/); *not verified*. Heavier than an SSH box. |
| **Microsoft Dev Box** | F1, F2, F7 (Windows) | Windows only. **Stopped accepting new customers on 2025-11-01**; its capabilities are moving into Windows 365 ([Windows blog, 2025-09-17](https://blogs.windows.com/windowsexperience/2025/09/17/windows-365-brings-resilient-ai-driven-cloud-productivity-to-more-users/), [Directions on Microsoft](https://www.directionsonmicrosoft.com/in-brief/dev-box-merges-into-windows-365/)). Not an option. |
| **boxd, Fly Sprites, Daytona, E2B, Modal** | F1 partial | Agent sandboxes, not personal workstations. See [0002 §2](0002-remote-agent-infrastructure.md#2-agent-execution-sandboxes--infrastructure). |

### 4. OSS building blocks (per feature)

| CloudTop piece | OSS stand-in | License · activity |
| --- | --- | --- |
| BeyondCorp / IAP (F3) | [Pomerium](https://github.com/pomerium/pomerium) ("zero trust, context and identity aware access proxy") for web UIs such as OpenChamber, code-server and Orca web | Apache-2.0 · 5.0k stars · active 2026-10 |
| | [Headscale](https://github.com/juanfont/headscale) (self-hosted Tailscale control server) | BSD-3-Clause · 44.4k stars · active |
| | [Teleport](https://github.com/gravitational/teleport) (SSH/K8s/DB access with audit) | AGPL-3.0 · 21.0k stars · active 2026-09 |
| Resilient terminal (F4) | tmux, [mosh](https://github.com/mobile-shell/mosh), [Eternal Terminal](https://github.com/MisterTea/EternalTerminal) | see §1b |
| Cider V (F5) | [code-server](https://github.com/coder/code-server), [openvscode-server](https://github.com/gitpod-io/openvscode-server) | MIT · active |
| Chrome Remote Desktop (F6) | [Selkies](https://github.com/selkies-project/selkies), [KasmVNC](https://github.com/kasmtech/KasmVNC), [xrdp](https://github.com/neutrinolabs/xrdp) | see §2b |
| Fleet provisioning (F7) | [Coder](https://github.com/coder/coder) templates; [DevPod](https://github.com/loft-sh/devpod) (client-only) | Coder AGPL-3.0, active. **DevPod looks stalled:** last release v0.6.15 on 2025-03-10, last push 2025-11-14. |
| CitC (F9) | [Sapling](https://github.com/facebook/sapling) + EdenFS (Meta's virtual-FS monorepo client) | GPL-2.0 · 7.0k stars. EdenFS is "not yet supported publicly, OSS is buildable for unsupported experimentation". |
| Blaze/Forge (F9) | Bazel + [BuildBuddy](https://github.com/buildbuddy-io/buildbuddy) remote cache and remote build execution | Open-core (licence shown as NOASSERTION) · active. Only worth it for big Bazel repos. |

---

## Coverage Matrix

| Candidate | Category | F1 Persist | F2 Big compute | F3 Zero-trust | F4 SSH-first | F5 Remote IDE | F6 GUI | F7 Fleet mgmt | F8 Region | F9 Monorepo/build | Cost (solo, always-on) | Self-host burden |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| GCE + CRD + IAP (1a) | DIY | Y | Y | Y | Y | P (add `code tunnel`) | Y (CRD) | — | Y | — | GCE rates | Medium |
| VPS + Tailscale + tmux/mosh (1b) | DIY | Y | Y | Y | Y | P (add `code tunnel`) | P (add webtop/xrdp) | P (fleet repo) | Y | — | ~€57/mo Hetzner AX42 ([0002](0002-remote-agent-infrastructure.md#cloud-vm-providers-for-agent-execution)) | Medium |
| Coder Community (2a) | Deployable / OSS | Y | Y | Y | Y | Y | P (kasmvnc module) | Y | Y | — | Free + infra | High (control plane) |
| docker-webtop / Selkies (2b) | Deployable | P | host-bound | — (bring your own) | P | — | Y | — | — | — | Free + infra | Low–Medium |
| Kasm Workspaces CE (2b) | Deployable | P | host-bound | P | — | — | Y | P | — | — | Free (non-commercial, 5 sessions) | Medium |
| Google Cloud Workstations (3a) | Paid | Y (home disk) | Y | Y (IAP) | Y | Y (+Gemini) | — | Y | Y | — | ~$634/mo (e2-std-8 + control plane) | None |
| exe.dev (3b) | Paid | Y | P (pool-based) | Y | Y | P | — | P | P | — | $15/mo and up | None |
| GitHub Codespaces | Paid | P (12h max) | Y | Y | Y | Y | — | Y | P | — | per core-hour | None |
| AWS WorkSpaces Personal | Paid | Y | Y | P | P | — | Y (DCV) | Y | Y | — | *unverified* | None |
| Microsoft Dev Box | Paid | n/a: closed to new customers since 2025-11-01, Windows only | | | | | | | | | | |

No public offering reproduces **F9**. Piper, CitC and Blaze are Google's
internal monorepo stack. The nearest OSS stand-ins are Sapling/EdenFS plus
Bazel remote execution, and EdenFS is officially unsupported outside Meta.

---

## Recommendation for This Setup

The aim is to get Claude Code, Codex and OpenCode off a hot MacBook, keep
agents alive when the lid closes or cellular drops, and keep driving them from
Orca. That matches the way Googlers actually use CloudTop: **a persistent,
oversized Linux box reached over SSH, with the laptop as a thin client.** The
streamed desktop is the part users dislike most, and agents don't need it.

### Top pick: a personal CloudTop built yourself (1b + bits of 1a)

One always-on Linux VM, joined to the tailnet with
[cloud-init + Tailscale SSH](https://tailscale.com/kb/1293/cloud-init). No
public ports.

- **Hardware:** a Hetzner dedicated box (AX42, about €57/mo per 0002) or a
  GCE instance if you want IAP and Google regions.
- **Terminal:** tmux plus mosh or Eternal Terminal, for the unreliable Turkish
  cellular connection.
- **Agents:** Orca as an
  [SSH target or `orca serve`](https://www.onorca.dev/docs/ways-to-run), so the
  agents run where the LLM API connection is stable.
- **Cron:** Hermes cron on the same box.
- **Editing:** `code tunnel` or code-server for the occasional hands-on edit.
- **Desktop:** add webtop/Selkies only if a GUI is ever really needed, and put
  it behind Tailscale, because it has no auth by default.
- **Provisioning:** Theo's fleet-repo `provision-a-box` pattern plays the role
  of Google's golden image.

Why this one:

- It covers F1–F5 and F8.
- It costs roughly 10× less than Cloud Workstations for an always-on box.
- It reuses tools already in use (Tailscale, Orca, Hermes).
- It has no 12-hour lifetime cap like Codespaces.

### Runner-up: Coder Community on that same remote compute

Pick this when one box becomes several, or when Optio's Kubernetes cluster
arrives (see [0003](0003-ticket-to-merge-tools.md#1-optio)). Coder adds:

- the missing **F7**: Terraform templates with persistent disks, and
  rebuild/"trade-in" from a template;
- browser IDEs and a KasmVNC desktop as modules;
- first-party Claude Code, Codex and OpenCode modules.

It deploys through Helm on the same cluster, or one-click on Railway or Fly.
The cost is running an AGPL control plane for one user, which is overkill until
there are several workspaces.

**If you'd rather pay than run a server:** exe.dev ($15/mo and up) is the
closest managed "personal CloudTop". It gives you persistent, SSH-native,
private-by-default VMs from a Tailscale co-founder. Size the pool above
2 vCPU / 4 GB if you want more than one agent running at once.

---

## Unverified / Open Questions

- CloudTop is equated with the paper's "cloud desktop" by inference. The paper
  doesn't use the name.
- CloudTop's state inside Google in 2026 (specs, Cider V integration, whether
  it's mandatory) is not public.
- CitC/Piper details: the primary monorepo paper (ACM) couldn't be fetched.
  F9 rests on the HN comment.
- Kasm commercial pricing and AWS WorkSpaces Linux bundle prices were not
  captured.
- The price of exe.dev Personal pools above 2 vCPU / 4 GB was not captured.

---

## Sources

**The Google tool**
- HN comment 34933249 and replies: [34933249](https://news.ycombinator.com/item?id=34933249), [34933429](https://news.ycombinator.com/item?id=34933429), [34933609](https://news.ycombinator.com/item?id=34933609), [34933571](https://news.ycombinator.com/item?id=34933571), [34933605](https://news.ycombinator.com/item?id=34933605), [34933886](https://news.ycombinator.com/item?id=34933886), [34943528](https://news.ycombinator.com/item?id=34943528) (Feb 2023; read via the [HN API](https://hacker-news.firebaseio.com/v0/item/34933249.json))
- Ron Amadeo, "Google has gotten so cheap, employees now have to share desks", Ars Technica, 2023-02-24 ([original](https://arstechnica.com/gadgets/2023/02/google-has-gotten-so-cheap-employees-now-have-to-share-desks/), [archive](https://web.archive.org/web/2023/https://arstechnica.com/gadgets/2023/02/google-has-gotten-so-cheap-employees-now-have-to-share-desks/))
- Fata, Arida, Hahn, Beyer, "From Corp to Cloud: Google's Virtual Desktops", ACM Queue 16(3), 2018 ([PDF](https://storage.googleapis.com/gweb-research2023-media/pubtools/4484.pdf), [Queue](https://queue.acm.org/detail.cfm?id=3264508))
- Laurent Le Brun, "A History of IDEs at Google", 2026-05-09 ([blog](https://laurent.le-brun.eu/blog/a-history-of-ides-at-google))
- Potvin & Levenberg, "Why Google Stores Billions of Lines of Code in a Single Repository" ([research.google](https://research.google/pubs/why-google-stores-billions-of-lines-of-code-in-a-single-repository/)); full text not read

**DIY**
- [Chrome Remote Desktop on Compute Engine](https://docs.cloud.google.com/architecture/chrome-desktop-remote-on-compute-engine) · [IAP TCP forwarding](https://docs.cloud.google.com/iap/docs/using-tcp-forwarding) · [GCE custom images](https://cloud.google.com/compute/docs/images#custom_images)
- [Tailscale cloud-init](https://tailscale.com/kb/1293/cloud-init) · [Tailscale pricing](https://tailscale.com/pricing) · [VS Code Remote Tunnels](https://code.visualstudio.com/docs/remote/tunnels)
- [mosh](https://github.com/mobile-shell/mosh) · [Eternal Terminal](https://github.com/MisterTea/EternalTerminal) · [manishtrouble/myvmkit](https://github.com/manishtrouble/myvmkit)

**Deployables / OSS**
- [coder/coder](https://github.com/coder/coder) · [coder/registry](https://github.com/coder/registry) · [Coder pricing](https://coder.com/pricing) · [Coder on Railway](https://railway.com/deploy/coder)
- [code-server](https://github.com/coder/code-server) · [openvscode-server](https://github.com/gitpod-io/openvscode-server)
- [docker-webtop](https://github.com/linuxserver/docker-webtop) · [Selkies](https://github.com/selkies-project/selkies) · [KasmVNC](https://github.com/kasmtech/KasmVNC) · [Kasm Community Edition](https://kasm.com/community-edition) · [neko](https://github.com/m1k1o/neko) · [Guacamole](https://github.com/apache/guacamole-server) · [xrdp](https://github.com/neutrinolabs/xrdp)
- [Pomerium](https://github.com/pomerium/pomerium) · [Headscale](https://github.com/juanfont/headscale) · [Teleport](https://github.com/gravitational/teleport) · [Tailscale](https://github.com/tailscale/tailscale)
- [DevPod](https://github.com/loft-sh/devpod) · [Sapling](https://github.com/facebook/sapling) · [BuildBuddy](https://github.com/buildbuddy-io/buildbuddy)

**Paid**
- [Cloud Workstations overview](https://docs.cloud.google.com/workstations/docs/overview) · [pricing](https://cloud.google.com/workstations/pricing)
- [exe.dev](https://exe.dev) · [exe.dev pricing](https://exe.dev/pricing) · [Meet exe.dev](https://blog.exe.dev/meet-exe.dev)
- [Codespaces timeout docs](https://docs.github.com/en/codespaces/setting-your-user-preferences/setting-your-timeout-period-for-github-codespaces)
- [AWS WorkSpaces bundles](https://docs.aws.amazon.com/workspaces/latest/adminguide/amazon-workspaces-bundles.html) · [pricing](https://aws.amazon.com/workspaces/pricing/)
- [Windows 365 / Dev Box announcement](https://blogs.windows.com/windowsexperience/2025/09/17/windows-365-brings-resilient-ai-driven-cloud-productivity-to-more-users/) · [Directions on Microsoft](https://www.directionsonmicrosoft.com/in-brief/dev-box-merges-into-windows-365/)

GitHub star counts, licences and last-push dates come from `gh api` on
2026-10-05.
