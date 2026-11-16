# Install AI Coding Agent

This environment uses [Oh My Pi (OMP)](https://omp.sh/), a terminal-based AI coding agent.

OMP runs entirely inside the Ubuntu VM, with access limited to the VM environment and the files intentionally synchronized from the macOS host.

## Install Oh My Pi

Download the official installation script:

```bash
curl -fsSL https://omp.sh/install -o /tmp/install-omp.sh
```

Install the prebuilt binary:

```bash
sh /tmp/install-omp.sh --binary
```

Add the local binary directory to your `PATH` and reload the shell configuration:

```bash
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

Verify the installation:

```bash
omp --version
```

## Initial Setup

Start Oh My Pi:

```bash
omp
```

Follow the on-screen instructions to complete the initial configuration, authenticate with your preferred AI provider, and select a model.

Once configured, start OMP inside a synchronized project directory:

```bash
cd ~/Code
omp
```

Verify that:

- OMP can access the intended project directory.
- Authentication works.
- The selected model responds.
- No unexpected files are created or modified.

## Security Considerations

> [!IMPORTANT]
> OMP can read and modify files, execute shell commands, and communicate with external services. Treat it as potentially untrusted software, even when running inside the VM.

- Keep OAuth credentials and API keys outside the synchronized `~/Code` directory.
- Do not provide the VM with macOS SSH keys, GitHub credentials, or production secrets.
- Review changes on the macOS host before committing or publishing them.
- Do not assume that VM isolation prevents all network access or data exfiltration.
- Only expose projects and resources intentionally required for development.
