# Install Mutagen

[Mutagen](https://mutagen.io/) provides continuous, bidirectional file synchronization between the macOS host and the Ubuntu VM over SSH.

Unlike a mounted shared folder, Mutagen maintains independent copies of the project files on both systems. This allows development tools, Git, and file watchers to operate on the VM's native filesystem.

Install Mutagen using Homebrew:

```bash
brew install mutagen-io/mutagen/mutagen
```

Verify the installation:

```bash
mutagen version
```

> [!NOTE]
> Mutagen synchronization sessions will be configured after setting up the Ubuntu VM and SSH access. No synchronization

## Why Mutagen instead of shared folders?

UTM's VirtFS/9P shared folders cause significant performance degradation for filesystem-intensive Git operations, particularly `git status`. Keeping project files on the VM's native filesystem and synchronizing them with Mutagen eliminated this bottleneck.
