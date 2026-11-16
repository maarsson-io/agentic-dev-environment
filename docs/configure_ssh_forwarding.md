# Configure SSH Port Forwarding

SSH port forwarding allows the macOS host to connect to the Ubuntu VM through a local TCP port without exposing the VM's SSH service on the host's external network interfaces.

## Configure UTM

Shut down the VM and open its network settings in UTM.

Under **Network** (Emulated VLAN) → **Port Forwarding**, add the following port forwarding rule:

| Setting | Value |
|---|---|
| Protocol | TCP |
| Guest Address | Leave empty |
| Guest Port | `22` |
| Host Address | `127.0.0.1` |
| Host Port | `2222` |

> [!IMPORTANT]
> The host address **MUST** be `127.0.0.1`, not `0.0.0.0`. This restricts the forwarded SSH port to the macOS host's loopback interface.

## Verify SSH Access

Start the VM, log in and verify that the SSH server is running:

```bash
sudo systemctl is-active ssh
```

Expected output:

```text
active
```

On the macOS host, test the forwarded connection:

```bash
ssh -p 2222 <vm-user>@127.0.0.1
```

Replace `<vm-user>` with the Ubuntu username created during installation.

Accept the VM's SSH host key after verifying its fingerprint. Enter the VM user's password when prompted.

## Configure Serial Console

Once SSH access has been verified, the graphical display can be replaced with a serial console.

1. Shut down the VM.
2. Open the VM settings in UTM.
3. Remove the **Display** device.
4. Add a new  **Serial** device and select **Pseudo-TTY Device**.
5. Start the VM from the macOS host terminal:

   ```bash
   utmctl start UbuntuVM
   ```

6. Verify that both the serial console and SSH access work.
