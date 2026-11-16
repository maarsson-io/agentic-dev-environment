# Install UTM

The isolated virtual machine environment is provided by [UTM](https://getutm.app/).

Install it using Homebrew:

```bash
brew install --cask utm
```

Verify the installation and CLI availability:

```bash
utmctl version
```

> [!NOTE]
> While UTM also supports Apple's native virtualization framework, this setup uses **QEMU-based virtualization with hardware acceleration**. QEMU was selected because its Emulated VLAN networking mode provided working internet connectivity alongside the host's VPN, while allowing additional network restrictions through the host firewall.
