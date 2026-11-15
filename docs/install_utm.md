# Install UTM

[UTM](https://mac.getutm.app/) provides the virtual machine environment used to run Ubuntu Server.

Install UTM using Homebrew:

```bash
brew install --cask utm
```

This setup uses **QEMU emulation** rather than Apple's native virtualization framework.

QEMU was selected to support the required virtual networking configuration, including SSH port forwarding and host-level network restrictions.

> [!NOTE]
> QEMU emulation introduces some performance overhead compared to native virtualization. For this workflow, the trade-off is acceptable because AI inference runs outside the VM, while the VM handles development tools, code execution, and tests.
