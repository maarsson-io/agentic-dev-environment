# Install and Configure LuLu Firewall

[LuLu](https://objective-see.org/products/lulu.html) is used to restrict outbound network connections from the UTM/QEMU virtual machine.

> [!NOTE]
> LuLu is optional. Without it, the VM will not benefit from the additional host-level network restrictions described in this guide.

## Install LuLu Firewall

Install it using Homebrew:

```bash
brew install --cask lulu
```

Launch LuLu and follow the instructions to enable its network extension and grant the required macOS permissions.

## Configure LuLu Firewall

In LuLu, configure the following rules for **QEMULauncher** (`/Applications/UTM.app/Contents/XPCServices/QEMUHelper.xpc/Contents/MacOS/QEMULauncher.app`):

| Remote address | Port | Action | Purpose |
|---|---|---|---|
| Any address | Any | ✅ Allow | Permit general outbound connectivity |
| `192.168.0.0/16` | Any | ❌ Block | Block access to private LAN addresses |
| `172.16.0.0/12` | Any | ❌ Block | Block access to private network addresses |
| `169.254.0.0/16` | Any | ❌ Block | Block access to IPv4 link-local addresses |
| `10.0.0.0/8` | Any | ❌ Block | Block access to private network addresses |
| `100.64.0.0/10` | Any | ❌ Block | Block access to CGNAT addresses, including Tailscale peers |
| `100.64.0.2` | `53` | ✅ Allow | Permit DNS resolution through NordVPN's DNS resolver |

The rules are intended to allow general internet connectivity while restricting access to private, link-local, and Tailscale/CGNAT address ranges.

The `100.64.0.2:53` DNS exception is specific to the tested NordVPN configuration and should only be added when required. Verify that it takes precedence over the broader `100.64.0.0/10` block rule.

> [!IMPORTANT]
> LuLu rules are an additional restriction, not a complete VM isolation boundary. Their effectiveness depends on how LuLu evaluates overlapping rules and how QEMU forwards guest traffic.
>
> Verify network access and restrictions after installation. Do not assume that the presence of these rules proves that private networks are inaccessible.
