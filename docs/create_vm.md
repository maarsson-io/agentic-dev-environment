# Create the Virtual Machine

Open UTM and create a new virtual machine using the following settings:

| Setting | Value |
|---|---|
| Virtualization mode | Emulate |
| Operating system | Linux |
| Architecture | ARM64 (aarch64) |
| Memory | 4 GB |
| CPU cores | 4 |
| Storage | 40 GB |
| Boot image | Ubuntu Server ARM64 ISO |
| VM name | `UbuntuVM` |
| Enable display output | Enabled during installation |

**Before starting the Ubuntu installer**, open the VM settings in UTM and configure its network:

| Setting | Value |
|---|---|
| Network Mode | Emulated VLAN |
| Guest IPv4 configuration | Automatic (DHCP) |

The VM does not require a graphical desktop environment. Keep display output enabled for installation; after SSH access has been verified, it can be replaced with a serial console using UTM's **Built-in Terminal**.

> [!IMPORTANT]
> Configure **Emulated VLAN before installing Ubuntu**. Otherwise, the installer may not detect a working network connection.

