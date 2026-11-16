# Configure SSH Authentication

Configure key-based SSH authentication between the macOS host and the Ubuntu VM without exposing the host's existing SSH credentials.

## Generate a Dedicated SSH Key

On the macOS host, generate a new Ed25519 key pair:

```bash
ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519_ubuntu_vm -C "ubuntu-vm"
```

> [!IMPORTANT]
> **Keep the private key on the host.** Do not reuse your GitHub SSH key or enable SSH agent forwarding.

## Install the Public Key

Copy the public key to the Ubuntu VM:

```bash
ssh-copy-id -i ~/.ssh/id_ed25519_ubuntu_vm.pub -p 2222 <vm-user>@127.0.0.1
```

Replace `<vm-user>` with the Ubuntu username created during installation.

If `ssh-copy-id` is unavailable on macOS, use:

```bash
cat ~/.ssh/id_ed25519_ubuntu_vm.pub | ssh -p 2222 <vm-user>@127.0.0.1 \
  'umask 077; mkdir -p ~/.ssh; cat >> ~/.ssh/authorized_keys'
```

## Configure SSH Alias

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

Test the connection:

```bash
ssh uvm
```

Verify that authentication succeeds using the dedicated SSH key.

## Disable Password Authentication

This step is optional but recommended for additional security.

> [!IMPORTANT]
> Do not disable password authentication until key-based SSH access has been verified. Keep the existing session open until the new connection succeeds.
>
> The VM's local account password remains available for `sudo` and serial-console recovery.

Create a dedicated SSH configuration file inside the VM:

```bash
sudo tee /etc/ssh/sshd_config.d/01-key-only.conf > /dev/null <<'EOF'
PasswordAuthentication no
KbdInteractiveAuthentication no
PermitRootLogin no
PubkeyAuthentication yes
EOF
```

Validate the configuration:

```bash
sudo sshd -t
```

Check the effective settings:

```bash
sudo sshd -T | grep -E '^(passwordauthentication|kbdinteractiveauthentication|permitrootlogin|pubkeyauthentication) '
```

Expected values:

```text
permitrootlogin no
pubkeyauthentication yes
passwordauthentication no
kbdinteractiveauthentication no
```

If the effective settings differ, check for conflicting SSH configuration files before proceeding.

Restart the SSH service:

```bash
sudo systemctl restart ssh
```

Keep the existing SSH session open and verify that a new connection from the macOS host succeeds:

```bash
ssh -o PasswordAuthentication=no -o KbdInteractiveAuthentication=no uvm
```
