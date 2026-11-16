# Configure File Synchronization

[Mutagen](https://mutagen.io/) provides continuous, bidirectional file synchronization between the macOS host and the Ubuntu VM over SSH.

Files are stored independently on both systems, allowing development tools to operate on the VM's native filesystem.

## Prepare Directories

On the macOS host, create a dedicated directory for projects accessible to the AI coding agent:

```bash
mkdir -p ~/VMShare
```

On the Ubuntu VM, create the corresponding directory:

```bash
mkdir -p ~/Code
```

The host directory should contain only projects intentionally made available to the VM. Do not synchronize your entire home directory, SSH credentials, or unrelated repositories.

## Create a Synchronization Session

Run the following command on the macOS host:

```bash
mutagen sync create \
  --name=vm-code \
  --sync-mode=two-way-safe \
  --no-global-configuration \
  --no-ignore-vcs \
  ~/VMShare \
  uvm:~/Code
```

The `uvm` SSH alias must already be configured as described in [Configure SSH Authentication](configure_ssh.md).

The selected options provide:

- **Two-way-safe synchronization:** changes propagate in both directions, while conflicting changes are not automatically overwritten.
- **Independent filesystems:** the VM works with its own copy of each project.
- **Git metadata synchronization:** `.git` directories are included, allowing coding agents to inspect repository history and diffs.

> [!IMPORTANT]
> File deletions also propagate between systems. Do not use the synchronized directory as a backup, and avoid running Git operations simultaneously on both copies of a repository.

## Verify Synchronization

Check the session:

```bash
mutagen sync list
```

Monitor synchronization activity:

```bash
mutagen sync monitor vm-code
```

Create a test file on the host:

```bash
touch ~/VMShare/mutagen-test.txt
```

Verify that it appears inside the VM:

```bash
ssh uvm 'ls -l ~/Code/mutagen-test.txt'
```

Remove the test file:

```bash
rm ~/VMShare/mutagen-test.txt
```

Force synchronization and verify the deletion:

```bash
mutagen sync flush vm-code
ssh uvm 'test ! -e ~/Code/mutagen-test.txt'
```

## Manage the Session

Useful commands:

```bash
# Show synchronization status
mutagen sync list

# Force pending changes to synchronize
mutagen sync flush vm-code

# Pause synchronization
mutagen sync pause vm-code

# Resume synchronization
mutagen sync resume vm-code

# Permanently remove the session
mutagen sync terminate vm-code
```

> [!NOTE]
> Mutagen can resume synchronization when the VM becomes available again. After restarting the VM, verify the session status before beginning development work.
