# Requirements

> [!TIP]
> This guide reflects my own opinionated development environment, using a macOS host and an Ubuntu VM, but feel free to adapt it to your own needs and preferences.

- macOS (Apple Silicon)
- [UTM](https://getutm.app/) with QEMU virtualization
- [Ubuntu Server](https://ubuntu.com/server) (ARM64)
- SSH access between host and VM
- [Mutagen](https://mutagen.io/) for bidirectional file synchronization
- [Oh My Pi](https://omp.sh/) as the AI coding agent

Optional, for network hardening:

- [LuLu](https://objective-see.org/products/lulu.html) for host-level network restrictions
- [NordVPN](https://nordvpn.com/) for host-level VPN connectivity

The instructions focus on a single-developer workflow and avoid unnecessary infrastructure or automation.
