### 1. Configure VM Networking

Shut down the Ubuntu VM before changing its network settings.

In UTM, open the VM settings and configure:

| Setting | Value |
|---|---|
| Network mode | Emulated VLAN |
| Network adapter | Emulated network adapter |
| Port forwarding | TCP, host `127.0.0.1:2222` → guest port `22` |

The forwarded SSH port allows the macOS host to connect to the VM without exposing the SSH service on the host's external network interfaces.

Start the VM and verify that its SSH server is running:

```bash
sudo systemctl status ssh
```

### 2. Create a Dedicated SSH Key

On the macOS host, generate a dedicated SSH key for the VM:

```bash
ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519_ubuntu_vm -C "ubuntu-vm"
```

Do not reuse your existing GitHub SSH key.

Copy the public key into the VM user's `~/.ssh/authorized_keys` file. From the macOS host:

```bash
ssh-copy-id -i ~/.ssh/id_ed25519_ubuntu_vm.pub -p 2222 <vm-user>@127.0.0.1
```

If `ssh-copy-id` is unavailable, add the contents of the `.pub` file to `~/.ssh/authorized_keys` manually.

### 3. Configure an SSH Alias

Add the following entry to the macOS host's `~/.ssh/config`:

```sshconfig
Host uvm
    HostName 127.0.0.1
    Port 2222
    User <vm-user>
    IdentityFile ~/.ssh/id_ed25519_ubuntu_vm
    IdentitiesOnly yes
    ForwardAgent no
```

Replace `<vm-user>` with the Ubuntu username created during installation.

Test the connection:

```bash
ssh uvm
```

Verify that the connection succeeds using the dedicated SSH key.

> [!IMPORTANT]
> Keep the private SSH key on the macOS host. Do not copy host SSH credentials, GitHub credentials, or other sensitive authentication material into the VM.
>
> SSH agent forwarding is explicitly disabled to prevent the VM from using the host's SSH agent.
