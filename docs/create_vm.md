# Create the Virtual Machine

Open UTM and create a new virtual machine using the following settings:

| Setting | Value |
|---|---|
| Virtualization mode | Emulate |
| Operating system | Linux |
| Architecture | ARM64 (aarch64) |
| Memory | 4 GB |
| CPU cores | 4 |
| Display output | Enabled during installation |
| Boot image | Ubuntu Server ARM64 ISO |
| Storage | 40 GB |
| VM name | `UbuntuVM` |


> [!NOTE]
> The VM does not require a graphical desktop environment, but you may keep display output enabled for installation. After SSH access has been verified, it can be replaced with a serial console using UTM's **Built-in Terminal**.

**Before starting the Ubuntu installer**, open the VM settings in UTM and configure its network:

| Setting | Value |
|---|---|
| Network Mode | Emulated VLAN |
