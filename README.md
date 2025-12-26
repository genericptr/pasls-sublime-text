# LSP-pasls

Pascal language support for Sublime Text's LSP plugin, powered by the [Pascal Language Server](https://github.com/genericptr/pascal-language-server).

## Features

- Code completion
- Go to definition/declaration
- Find references
- Document symbols
- Workspace symbols
- Signature help
- Document highlighting
- Diagnostics (syntax checking)
- Code formatting (via executeCommand)
- Code completion (via executeCommand)

## Installation

### Prerequisites

1. **Sublime Text 4** (build 4107 or later)
2. **LSP package** - Install via Package Control:
   - Open Command Palette (`Cmd+Shift+P` on macOS, `Ctrl+Shift+P` on Windows/Linux)
   - Run `Package Control: Install Package`
   - Select `LSP`

3. **Free Pascal Compiler** and optionally **Lazarus** installed on your system

### Installing LSP-pasls

#### Via Package Control (if published)

1. Open Command Palette
2. Run `Package Control: Install Package`
3. Select `LSP-pasls`
4. The Pascal Language Server binary will be automatically downloaded from GitHub releases

#### Manual Installation

1. Clone or download this repository
2. Create a directory named `LSP-pasls` in your Sublime Text `Packages` directory:
   - macOS: `~/Library/Application Support/Sublime Text/Packages/LSP-pasls`
   - Linux: `~/.config/sublime-text/Packages/LSP-pasls`
   - Windows: `%APPDATA%\Sublime Text\Packages\LSP-pasls`
3. Copy all files from this repository into that directory
4. Restart Sublime Text
5. The Pascal Language Server binary will be automatically downloaded from GitHub releases

### Binary Installation

By default, LSP-pasls will automatically download and manage the `pasls` binary from [GitHub releases](https://github.com/genericptr/pascal-language-server/releases). The binary is stored in `$CACHE/Package Storage/LSP-pasls`.

**Automatic Installation (Recommended):**
- Set `"managePaslsBinary": true` in settings (default)
- The plugin will download the appropriate binary for your platform
- Updates are checked and applied automatically

**Manual Installation:**
- Set `"managePaslsBinary": false` in settings
- Build pasls yourself following the [build instructions](https://github.com/genericptr/pascal-language-server)
- Configure the full path to your binary in the `command` setting

**Binary Naming Convention:**

For automatic installation to work, GitHub releases should include platform-specific binaries:
- macOS Intel: `pasls-darwin-x86_64`
- macOS Apple Silicon: `pasls-darwin-aarch64`
- Linux: `pasls-linux-x86_64`
- Windows: `pasls-win64-x86_64.exe`

The plugin will automatically detect your platform and download the appropriate binary. If no platform-specific binary is found, you must build pasls manually and use manual installation mode.

## Configuration

You must set up the required environment variables for the Pascal compiler. The binary path is configured automatically unless you're using manual installation mode.

### Global Configuration

1. Open Sublime Text preferences: `Preferences > Package Settings > LSP > Settings`
2. Add your configuration under the `clients` section:

**Automatic Binary Management (Default):**
```json
{
  "clients": {
    "LSP-pasls": {
      "enabled": true,
      "env": {
        "FPCDIR": "/usr/local/share/fpcsrc",
        "FPCTARGET": "darwin",
        "FPCTARGETCPU": "x86_64",
        "LAZARUSDIR": "/usr/share/lazarus",
        "PP": "/usr/local/lib/fpc/3.2.2/ppcx64"
      }
    }
  }
}
```

**Manual Binary Management:**
```json
{
  "clients": {
    "LSP-pasls": {
      "enabled": true,
      "managePaslsBinary": false,
      "command": ["/path/to/pascal-language-server/pasls"],
      "env": {
        "FPCDIR": "/usr/local/share/fpcsrc",
        "FPCTARGET": "darwin",
        "FPCTARGETCPU": "x86_64",
        "LAZARUSDIR": "/usr/share/lazarus",
        "PP": "/usr/local/lib/fpc/3.2.2/ppcx64"
      }
    }
  }
}
```

### Project-Specific Configuration

You can override settings per project by adding LSP settings to your `.sublime-project` file:

```json
{
  "folders": [
    {
      "path": "."
    }
  ],
  "settings": {
    "LSP": {
      "LSP-pasls": {
        "enabled": true,
        "command": ["/path/to/pasls"],
        "env": {
          "FPCDIR": "/usr/local/share/fpcsrc",
          "FPCTARGET": "linux",
          "FPCTARGETCPU": "x86_64"
        },
        "initializationOptions": {
          "fpcOptions": [
            "-Fu/path/to/your/units",
            "-Fi/path/to/your/includes",
            "-dDEBUG"
          ],
          "symbolDatabase": "$(tmpdir)/myproject-symbols.db"
        }
      }
    }
  }
}
```

## Configuration Options

### Environment Variables

- `FPCDIR`: Path to Free Pascal source directory
- `FPCTARGET`: Target platform (e.g., `darwin`, `linux`, `win64`)
- `FPCTARGETCPU`: Target CPU architecture (e.g., `x86_64`, `aarch64`)
- `LAZARUSDIR`: Path to Lazarus directory (optional)
- `PP`: Path to Free Pascal compiler executable

### Initialization Options

All options are optional and have sensible defaults:

- `fpcOptions`: Array of compiler flags (e.g., `["-Fu/path", "-dMACRO"]`)
- `symbolDatabase`: Path to SQLite database for symbol storage (recommended for large projects)
- `maximumCompletions`: Maximum number of completions per query (default: 100)
- `insertCompletionsAsSnippets`: Insert procedure completions as snippets (default: true)
- `insertCompletionProcedureBrackets`: Insert empty brackets for procedures (default: true)
- `includeWorkspaceFoldersAsUnitPaths`: Add workspace folders to unit paths (default: true)
- `includeWorkspaceFoldersAsIncludePaths`: Add workspace folders to include paths (default: true)
- `checkSyntax`: Check syntax on open/save (default: true)
- `publishDiagnostics`: Publish syntax errors as diagnostics (default: true)
- `workspaceSymbols`: Enable workspace symbols (default: true)
- `documentSymbols`: Enable document symbols (default: true)
- `minimalisticCompletions`: Show minimal completion info (default: false)
- `showSyntaxErrors`: Show syntax errors in UI (default: true)

### Supported Macros

You can use these macros in initialization options:

- `$(tmpdir)`: System temporary directory
- `$(root)`: Workspace root directory

## Platform-Specific Examples

### macOS (x86_64)

```json
{
  "command": ["/Users/username/dev/pascal-language-server/pasls"],
  "env": {
    "FPCDIR": "/usr/local/share/fpcsrc",
    "FPCTARGET": "darwin",
    "FPCTARGETCPU": "x86_64",
    "PP": "/usr/local/lib/fpc/3.2.2/ppcx64"
  }
}
```

### macOS (Apple Silicon)

```json
{
  "command": ["/Users/username/dev/pascal-language-server/pasls"],
  "env": {
    "FPCDIR": "/opt/homebrew/share/fpcsrc",
    "FPCTARGET": "darwin",
    "FPCTARGETCPU": "aarch64",
    "PP": "/opt/homebrew/lib/fpc/3.2.2/ppcaarch64"
  }
}
```

### Linux

```json
{
  "command": ["/home/username/pascal-language-server/pasls"],
  "env": {
    "FPCDIR": "/usr/share/fpcsrc",
    "FPCTARGET": "linux",
    "FPCTARGETCPU": "x86_64",
    "LAZARUSDIR": "/usr/share/lazarus",
    "PP": "/usr/bin/fpc"
  }
}
```

### Windows

```json
{
  "command": ["C:\\dev\\pascal-language-server\\pasls.exe"],
  "env": {
    "FPCDIR": "C:\\lazarus\\fpc\\3.2.2\\source",
    "FPCTARGET": "win64",
    "FPCTARGETCPU": "x86_64",
    "LAZARUSDIR": "C:\\lazarus",
    "PP": "C:\\lazarus\\fpc\\3.2.2\\bin\\x86_64-win64\\fpc.exe"
  }
}
```

## Usage

Once configured, the language server will automatically start when you open a Pascal file (`.pas`, `.pp`, `.lpr`, `.inc`).

### Available Commands

Via Command Palette (`Cmd+Shift+P` / `Ctrl+Shift+P`):

- `LSP: Format Document` - Format the current file (requires formatter config)
- `LSP: Go to Definition` - Jump to symbol definition
- `LSP: Go to Declaration` - Jump to symbol declaration
- `LSP: Find References` - Find all references to symbol
- `LSP: Document Symbols` - Show document outline
- `LSP: Workspace Symbols` - Search symbols across workspace

### Custom Commands

The language server supports these custom commands via `LSP: Execute Server Command`:

- `pasls.completeCode` - Complete code at cursor
- `pasls.formatCode` - Format current file with Jedi Code Formatter config

## Troubleshooting

### Language Server Not Starting

1. Check LSP server status: `Tools > LSP > Troubleshoot Server Configuration`
2. Verify the `pasls` binary path is correct and executable
3. Check that all environment variables are set correctly
4. View LSP logs: `Tools > LSP > Toggle Log Panel`

### No Completions/Features Working

1. Ensure your Free Pascal installation is properly configured
2. Check that `FPCDIR` points to the FPC source directory
3. Verify `fpcOptions` includes necessary unit paths
4. Enable symbol database for better performance with large projects

### Performance Issues

1. Use a symbol database:
   ```json
   "initializationOptions": {
     "symbolDatabase": "$(tmpdir)/pasls-symbols.db"
   }
   ```
2. Adjust `maximumCompletions` if you're getting too many results
3. Set `minimalisticCompletions": true` for faster completions

## Pascal Syntax Highlighting

This package does not include syntax highlighting. You'll need a Pascal syntax package:

- Install `Pascal` or `Free Pascal` package via Package Control
- Or create your own syntax definition

## License

This package is licensed under the MIT License.

The Pascal Language Server is licensed under the GPL v2 License.

## Links

- [Pascal Language Server](https://github.com/genericptr/pascal-language-server)
- [LSP for Sublime Text](https://github.com/sublimelsp/LSP)
- [Language Server Protocol](https://microsoft.github.io/language-server-protocol/)
