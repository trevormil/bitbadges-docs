# Installation

## One-Liner (Recommended)

The fastest way to install everything. Detects your OS and architecture automatically, downloads the chain binary, and installs the SDK CLI.

```bash
curl -fsSL https://raw.githubusercontent.com/BitBadges/bitbadgeschain/master/install.sh | sh
```

This installs:
1. **`bitbadgeschaind`** — chain binary to `/usr/local/bin/`
2. **`bitbadges-cli`** — SDK CLI via npm or bun (whichever is available)

### Options

```bash
# Testnet binary
curl -fsSL https://raw.githubusercontent.com/BitBadges/bitbadgeschain/master/install.sh | sh -s -- --testnet

# Specific version
curl -fsSL https://raw.githubusercontent.com/BitBadges/bitbadgeschain/master/install.sh | sh -s -- --version v29

# Custom install directory
curl -fsSL https://raw.githubusercontent.com/BitBadges/bitbadgeschain/master/install.sh | sh -s -- --install-dir ~/.local/bin

# Skip sudo
curl -fsSL https://raw.githubusercontent.com/BitBadges/bitbadgeschain/master/install.sh | sh -s -- --no-sudo
```

### Supported Platforms

| Platform | Architecture | Status |
|----------|-------------|--------|
| Linux | x86_64 (amd64) | Supported |
| Linux | ARM64 (aarch64) | Supported |
| macOS | Intel (amd64) | Supported |
| macOS | Apple Silicon (arm64) | Supported |
| Windows | x86_64 (via Git Bash, MSYS2, WSL) | Supported |

## SDK CLI Only (npm / bun)

If you only need the SDK CLI (API access, review tools, docs) without the chain binary:

```bash
# npm
npm install -g bitbadgesjs-sdk

# bun
bun install -g bitbadgesjs-sdk
```

Verify:

```bash
bitbadges-cli --help
```

## Chain Binary Only (From Source)

If you only need the chain binary and want to build from source (requires Go 1.24+):

```bash
git clone https://github.com/BitBadges/bitbadgeschain.git
cd bitbadgeschain
make build-mainnet-linux/amd64   # or your platform
```

Pre-built binaries are also available on the [GitHub Releases page](https://github.com/BitBadges/bitbadgeschain/releases).

## Configuration

### API Key

Get an API key from the [BitBadges Developer Portal](https://bitbadges.io/developer), then configure it:

```bash
bitbadges-cli config set apiKey YOUR_KEY
```

Or use environment variables:

```bash
export BITBADGES_API_KEY=YOUR_KEY
```

### Config File

The CLI reads `~/.bitbadges/config.json`. Manage it with:

```bash
# Set values
bitbadges-cli config set apiKey YOUR_KEY
bitbadges-cli config set apiKeyTestnet YOUR_TESTNET_KEY
bitbadges-cli config set network testnet
bitbadges-cli config set url https://custom-api.example.com/api/v0

# View current config
bitbadges-cli config show

# Remove a value
bitbadges-cli config unset apiKeyTestnet
```

Supported keys: `apiKey`, `apiKeyTestnet`, `apiKeyLocal`, `network` (`mainnet` | `testnet` | `local`), `url`.

### Environment Variables

| Variable | Description |
|---|---|
| `BITBADGES_API_KEY` | Default API key (all networks) |
| `BITBADGES_API_KEY_TESTNET` | Testnet-specific API key |
| `BITBADGES_API_KEY_LOCAL` | Local-specific API key |
| `BITBADGES_API_URL` | Custom base URL (overrides config) |

### Network Flags

| Flag | Description |
|---|---|
| `--testnet` | Use testnet API |
| `--local` | Use local API (`http://localhost:3001`) |
| `--url <url>` | Custom API base URL (overrides all others) |

### Resolution Order

**API key:** `--api-key` flag > network-specific env var > `BITBADGES_API_KEY` env var > network-specific config key > default config `apiKey`.

**Base URL:** `--url` flag > `--local` > `--testnet` > `BITBADGES_API_URL` env > config file > default production URL.

## Verify Installation

```bash
# Check chain binary
bitbadgeschaind version

# Check SDK CLI
bitbadges-cli sdk status

# Check API connectivity
bitbadges-cli sdk status --testnet
```

## Shell Completion

Generate bash/zsh completions for tab-completion:

```bash
eval "$(bitbadges-cli completion)"
```

Add that line to your `.bashrc` or `.zshrc` for persistent completions.
