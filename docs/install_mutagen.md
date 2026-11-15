# Install Mutagen

[Mutagen](https://mutagen.io/) provides continuous, bidirectional file synchronization between the macOS host and the Ubuntu VM over SSH.

Install it using Homebrew:

```bash
brew install mutagen-io/mutagen/mutagen
```

Verify the installation:

```bash
mutagen version
```

> [!NOTE]
> Unlike UTM's VirtFS/9P shared folders, which caused significant performance degradation for filesystem-intensive Git operations, Mutagen maintains independent copies of project files on both systems, allowing development tools to operate on the VM's native filesystem.
