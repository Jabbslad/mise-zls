# mise-zls

[![GitHub License](https://img.shields.io/github/license/jabbslad/mise-zls)](LICENSE)
[![GitHub Issues](https://img.shields.io/github/issues/jabbslad/mise-zls)](https://github.com/jabbslad/mise-zls/issues)

[ZLS](https://github.com/zigtools/zls) (Zig Language Server) plugin for [mise](https://mise.jdx.dev) (formerly rtx).

## Contents

- [Dependencies](#dependencies)
- [Install](#install)
- [Usage](#usage)
- [Configuration](#configuration)
- [Contributing](#contributing)
- [License](#license)

## Dependencies

- [mise](https://mise.jdx.dev) - The polyglot runtime manager
- [zig](https://ziglang.org/) - Required to build ZLS from source
- `git` - For cloning the ZLS repository

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
# Install the latest version (master branch)
mise install zls@latest

# Install a specific version/tag
mise install zls@0.11.0

# Set as global default
mise global zls@latest

# Or set in your project
mise local zls@latest
```

## Usage

After installation, `zls` will be available in your PATH:

```bash
# Check ZLS version
zls --version

# Use with your editor/IDE
# The ZLS binary will be at: ~/.local/share/mise/installs/zls/<version>/bin/zls
```

### Editor Configuration

#### Neovim (with nvim-lspconfig)

```lua
require('lspconfig').zls.setup{}
```

#### VS Code

Install the [Zig Language](https://marketplace.visualstudio.com/items?itemName=ziglang.vscode-zig) extension and configure the ZLS path if needed.

#### Other Editors

See the [ZLS documentation](https://github.com/zigtools/zls#editor-support) for configuration instructions for your editor.

## Configuration

### Available Versions

- `latest` or `master` - Builds from the latest master branch
- Any valid git tag/branch from the [ZLS repository](https://github.com/zigtools/zls/tags)

### Environment Variables

The plugin respects standard mise/asdf environment variables:

- `ASDF_INSTALL_VERSION` - Version being installed
- `ASDF_INSTALL_PATH` - Installation directory
- `ASDF_DOWNLOAD_PATH` - Download/build directory

## How It Works

This plugin:

1. **Downloads** - Clones the ZLS repository at the specified version
2. **Builds** - Compiles ZLS from source using `zig build -Doptimize=ReleaseSafe`
3. **Installs** - Places the compiled binary in the mise installation directory

## Troubleshooting

### Build Fails

Ensure you have a compatible version of Zig installed:

```bash
# Check Zig version
zig version

# Install/update Zig via mise
mise install zig@latest
```

### ZLS Not Found

Verify the installation:

```bash
# Check installed versions
mise list zls

# Check current version
mise current zls

# Verify binary exists
ls -la ~/.local/share/mise/installs/zls/*/bin/zls
```

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Credits

- [ZLS](https://github.com/zigtools/zls) - The Zig Language Server
- [mise](https://mise.jdx.dev) - The polyglot runtime manager
- [asdf](https://asdf-vm.com) - Plugin API compatibility

## Related Projects

- [mise-zig](https://github.com/mise-plugins/mise-zig) - Zig plugin for mise
- [zigtools/zls](https://github.com/zigtools/zls) - The Zig Language Server