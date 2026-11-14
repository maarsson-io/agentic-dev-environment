# Agentic Development Environment

A reproducible setup guide for an isolated AI-assisted development environment.

## Purpose

Run AI coding agents in an isolated environment while keeping source code review, Git operations, and access to sensitive resources under human control.


## Architecture

The environment consists of two systems:

- **Host:** Code editing and reviewing changes, managing Git repositories, and accessing GitHub.
- **Virtual machine:** Runs AI coding agents, development tools, project dependencies, and tests.

```mermaid
flowchart LR
    subgraph HOST["macOS Host"]
        direction TB
        GIT["Git / GitHub CLI"]
        MUT["Mutagen"]
        SHARE["~/VMShare"]
        UTM["UTM / QEMU"]
        SHARE --- MUT
    end

    subgraph VM["Ubuntu Server VM"]
        direction TB
        SSH["SSH Server"]
        CODE["~/Code"]
        OMP["Oh My Pi"]
        DEV["PHP / Composer / Node.js"]
        DB["MySQL / Redis"]
        MCP["MCP Integrations"]
        SSH --- CODE
        OMP --> CODE
        OMP --> DEV
        OMP --> DB
        OMP --> MCP
    end

    subgraph NETWORK["Network Security"]
        direction TB
        FW["LuLu Firewall"]
        VPN["NordVPN"]
        FW --> VPN
    end

    GH["GitHub"]
    INTERNET["Internet / AI Providers"]

    MUT <-->|SSH / Two-way Sync| SSH
    UTM -.->|Hosts| VM

    GIT -->|Fetch / Push / PR| FW
    VM -->|Restricted Network Access| FW
    VPN --> GH
    VPN --> INTERNET
```

UTM provides virtualization, while Mutagen synchronizes selected folder(s) between the host and the VM over SSH.

## Security Model

The virtual machine is treated as an untrusted execution environment.

Core principles:

- AI agents do not receive the host's SSH keys or GitHub credentials.
- The VM's network access is restricted using host firewall rules.
- Production credentials and sensitive data must not be copied into the VM.
- Only projects explicitly placed in `~/VMShare` are synchronized into the VM.
- Project changes made by agents are synchronized to the host for review.
- Agent-generated changes require human review before being committed.
- Git commits, pushes, and pull requests are managed by the developer on the host.

> [!IMPORTANT]
> **Isolation is not absolute.** The VM shares the host's physical hardware, and Mutagen intentionally allows changes made inside the VM to propagate into `~/VMShare`.

## Requirements

> [!TIP]
> This guide reflects my own opinionated development environment, using a macOS host and an Ubuntu VM, but feel free to adapt it to your own needs and preferences.

- macOS (Apple Silicon)
- UTM with QEMU virtualization
- Ubuntu Server ARM64
- SSH access between host and VM
- Mutagen for bidirectional file synchronization
- LuLu for host-level network restrictions
- NordVPN for host-level VPN connectivity
- Oh My Pi as the AI coding agent

The instructions focus on a single-developer workflow and avoid unnecessary infrastructure or automation.

## macOS Host Setup

### 1. Install UTM

[UTM](https://mac.getutm.app/) provides the virtual machine environment used to run Ubuntu Server.

Install UTM using Homebrew:

```bash
brew install --cask utm
```

This setup uses **QEMU emulation** rather than Apple's native virtualization framework.

QEMU was selected to support the required virtual networking configuration, including SSH port forwarding and host-level network restrictions.

> [!NOTE]
> QEMU emulation introduces some performance overhead compared to native virtualization. For this workflow, the trade-off is acceptable because AI inference runs outside the VM, while the VM handles development tools, code execution, and tests.

### 2. Install Mutagen

[Mutagen](https://mutagen.io/) provides continuous, bidirectional file synchronization between the macOS host and the Ubuntu VM over SSH.

Unlike a mounted shared folder, Mutagen maintains independent copies of the project files on both systems. This allows development tools, Git, and file watchers to operate on the VM's native filesystem.

Install Mutagen using Homebrew:

```bash
brew install mutagen-io/mutagen/mutagen
```

Verify the installation:

```bash
mutagen version
```

> [!NOTE]
> Mutagen synchronization sessions will be configured after setting up the Ubuntu VM and SSH access. No synchronization

#### Why Mutagen instead of shared folders?

UTM's VirtFS/9P shared folders cause significant performance degradation for filesystem-intensive Git operations, particularly `git status`. Keeping project files on the VM's native filesystem and synchronizing them with Mutagen eliminated this bottleneck.
