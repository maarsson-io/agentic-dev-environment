# Configure LuLu Firewall

[LuLu](https://objective-see.org/products/lulu.html) is used to restrict outbound network connections from the UTM/QEMU virtual machine.

This setup assumes LuLu is already installed and enabled on the macOS host.

In LuLu, configure the following rules for **QEMULauncher** (`com.utmapp.QEMULauncher`):

| Remote address | Port | Action |
|---|---|---|
| Any address | Any | Allow |
| `192.168.0.0/16` | Any | Block |
| `172.16.0.0/12` | Any | Block |
| `169.254.0.0/16` | Any | Block |
| `10.0.0.0/8` | Any | Block |
| `100.64.0.0/10` | Any | Block |
| `100.64.0.2` | `53` | Allow |

The rules are intended to allow general internet connectivity while restricting access to private, link-local, and Tailscale/CGNAT address ranges.

The `100.64.0.2:53` exception permits DNS resolution through the resolver used by NordVPN in the tested environment. Ensure this exception is effective despite the broader `100.64.0.0/10` block rule.

> [!IMPORTANT]
> LuLu rules are an additional restriction, not a complete VM isolation boundary. Their effectiveness depends on how LuLu evaluates overlapping rules and how QEMU forwards guest traffic.
>
> Verify network access and restrictions after installation. Do not assume that the presence of these rules proves that private networks are inaccessible.
