# mise-zls

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![GitHub Issues](https://img.shields.io/github/issues/jabbslad/mise-zls)](https://github.com/jabbslad/mise-zls/issues)

[ZLS](https://github.com/zigtools/zls) (Zig Language Server) plugin for [mise](https://mise.jdx.dev) (formerly rtx).

## Contents

- [Features](#features)
- [Dependencies](#dependencies)
- [Install](#install)
- [Usage](#usage)
- [Available Commands](#available-commands)
- [Configuration](#configuration)
- [Uninstall](#uninstall)
- [Troubleshooting](#troubleshooting)
- [Contributing](#contributing)
- [License](#license)

## Features

- **Automatic version discovery** - Fetches available ZLS versions from GitHub
- **Build from source** - Compiles ZLS optimized for your system
- **Version validation** - Verifies versions exist before download
- **Git commit SHA support** - Install specific commits for testing or debugging
- **Retry logic** - Handles network failures gracefully
- **Progress reporting** - Shows build and installation progress
- **Colored output** - Clear, user-friendly terminal output
- **Compatibility checking** - Validates Zig version compatibility

## Dependencies

- [mise](https://mise.jdx.dev) - The polyglot runtime manager
- [zig](https://ziglang.org/) - Required to build ZLS from source (0.11.0+ recommended)
- `git` - For cloning the ZLS repository
- `curl` or `wget` - For fetching version information

## Install

### Add the plugin

```bash
mise plugin add zls https://github.com/jabbslad/mise-zls.git
```

Or add to your `.mise.toml`:

```toml
[plugins]
zls = "https://github.com/jabbslad/mise-zls.git"
```

### Install ZLS

```bash
# List all available versions
mise list-all zls

# Install the latest version (master branch)
mise install zls@latest

# Install a specific version/tag
mise install zls@0.13.0

# Install from a specific git commit SHA
mise install zls@1a9a623d7d82d23c7d82d23c7d82d23c7d82d23c

# Set as global default
mise global zls@latest

# Or set in your project
mise local zls@0.13.0
```

## Usage

After installation, `zls` will be available in your PATH:

```bash
# Check ZLS version
zls --version

# View installed versions
mise list zls

# View current active version
mise current zls

# Switch between versions
mise use zls@0.12.0
```

### Editor Configuration

#### Neovim (with nvim-lspconfig)

```lua
require('lspconfig').zls.setup{}
```

#### VS Code

Install the [Zig Language](https://marketplace.visualstudio.com/items?itemName=ziglang.vscode-zig) extension and configure the ZLS path if needed.

#### Helix

ZLS should be automatically detected if it's in your PATH. Otherwise, add to `~/.config/helix/languages.toml`:

```toml
[[language]]
name = "zig"
language-server = { command = "zls" }
```

#### Other Editors

See the [ZLS documentation](https://github.com/zigtools/zls#editor-support) for configuration instructions for your editor.

## Available Commands

The plugin provides the following commands:

### `list-all`

Lists all available ZLS versions from GitHub, including:

- Official release versions
- Git tags
- Recent commit SHAs from master branch (5 most recent)
- `latest` (master branch)
- `master` (latest development version)

### `download`

Downloads the specified ZLS version from GitHub:

- Validates version exists before downloading
- Supports git tags, branches, and full 40-character commit SHAs
- Includes retry logic for network failures
- Shows download progress and commit information

### `install`

Builds and installs ZLS from source:

- Checks Zig compatibility
- Shows build progress
- Verifies successful installation
- Provides usage instructions

## Configuration

### Available Versions

- `latest` or `master` - Builds from the latest master branch
- Any valid git tag/branch from the [ZLS repository](https://github.com/zigtools/zls/tags)
- Any 40-character git commit SHA from the ZLS repository
- Example versions: `0.13.0`, `0.12.0`, `0.11.0`
- Example commit SHA: `1a9a623d7d82d23c7d82d23c7d82d23c7d82d23c`

### Environment Variables

The plugin respects standard mise/asdf environment variables:

- `ASDF_INSTALL_VERSION` - Version being installed
- `ASDF_INSTALL_PATH` - Installation directory
- `ASDF_DOWNLOAD_PATH` - Download/build directory

### Project Configuration

Create a `.mise.toml` in your project:

```toml
[tools]
zig = "0.13.0"
zls = "0.13.0"
```

Or use legacy `.tool-versions`:

```
zig 0.13.0
zls 0.13.0
```

## Uninstall

### Remove a specific ZLS version

```bash
# Uninstall a specific version
mise uninstall zls@0.13.0

# Uninstall all versions
mise uninstall zls
```

### Remove the plugin completely

```bash
# Remove the plugin
mise plugin remove zls

# This will:
# - Remove all installed ZLS versions
# - Remove the plugin from mise
```

### Manual cleanup (if needed)

```bash
# Remove all ZLS installations
rm -rf ~/.local/share/mise/installs/zls

# Remove the plugin
rm -rf ~/.local/share/mise/plugins/zls

# Remove from global config
mise global --remove zls
```

## How It Works

This plugin:

1. **Lists versions** - Queries GitHub API for available releases, tags, and recent commit SHAs
2. **Downloads** - Clones the ZLS repository at the specified version, tag, or commit SHA
3. **Builds** - Compiles ZLS from source using `zig build -Doptimize=ReleaseSafe`
4. **Installs** - Places the compiled binary in the mise installation directory

When installing from a git commit SHA:

- The full 40-character SHA must be provided
- The plugin validates the commit exists via GitHub API
- The entire repository is cloned (not shallow) to access the specific commit
- The commit is then checked out for building

## Troubleshooting

### Build Fails

Ensure you have a compatible version of Zig installed:

```bash
# Check Zig version
zig version

# Install/update Zig via mise
mise install zig@latest

# ZLS version compatibility:
# - ZLS 0.13.0 requires Zig 0.13.0
# - ZLS 0.12.0 requires Zig 0.12.0
# - ZLS 0.11.0 requires Zig 0.11.0
```

### Version Not Found

If a version isn't listed or fails to download:

```bash
# Refresh available versions
mise list-all zls

# Check if the version exists on GitHub
curl -s https://api.github.com/repos/zigtools/zls/tags | grep "name"
```

### Network Issues

If you're behind a proxy or have network restrictions:

```bash
# The plugin will retry downloads 3 times automatically
# You can also manually set git proxy if needed:
git config --global http.proxy http://proxy.example.com:8080
```

### ZLS Not Found After Installation

Verify the installation:

```bash
# Check installed versions
mise list zls

# Check current version
mise current zls

# Verify binary exists
ls -la ~/.local/share/mise/installs/zls/*/bin/zls

# Ensure mise shims are in PATH
mise doctor
```

### Permission Denied Errors

```bash
# Ensure scripts are executable
chmod +x ~/.local/share/mise/plugins/zls/bin/*

# Check directory permissions
ls -la ~/.local/share/mise/
```

### API Rate Limiting

If you see errors fetching versions:

```bash
# The plugin includes fallback versions if GitHub API is unavailable
# You can still install known versions like:
mise install zls@0.13.0
mise install zls@0.12.0
```

## Examples

### Setting up a new Zig project

```bash
# Create project directory
mkdir my-zig-project && cd my-zig-project

# Set local versions
mise local zig@0.13.0
mise local zls@0.13.0

# Verify installation
zig version
zls --version

# Initialize Zig project
zig init
```

### Switching between projects with different versions

```bash
# Project A uses Zig/ZLS 0.13.0
cd ~/project-a
mise install  # Installs versions from .mise.toml

# Project B uses Zig/ZLS 0.12.0
cd ~/project-b
mise install  # Automatically switches to 0.12.0
```

### Testing a specific commit

```bash
# Install ZLS from a specific commit for testing
mise install zls@abc123def456789012345678901234567890abcd

# Use it temporarily in your project
mise local zls@abc123def456789012345678901234567890abcd

# Test your code with this specific version
zls --version

# Switch back to stable version when done
mise local zls@0.13.0
```

### Using with CI/CD

```yaml
# GitHub Actions example
- name: Setup mise
  uses: jdx/mise-action@v2
  with:
    tools: |
      zig@0.13.0
      zls@0.13.0

- name: Verify installation
  run: |
    zig version
    zls --version
```

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

### Development

```bash
# Clone the plugin locally
git clone https://github.com/jabbslad/mise-zls.git
cd mise-zls

# Test the plugin locally
mise plugin add zls .
mise list-all zls
```

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Credits

- [ZLS](https://github.com/zigtools/zls) - The Zig Language Server
- [mise](https://mise.jdx.dev) - The polyglot runtime manager
- [asdf](https://asdf-vm.com) - Plugin API compatibility

## Related Projects

- [mise-zig](https://github.com/mise-plugins/mise-zig) - Zig plugin for mise
- [zigtools/zls](https://github.com/zigtools/zls) - The Zig Language Server
- [ziglang/zig](https://github.com/ziglang/zig) - The Zig programming language

