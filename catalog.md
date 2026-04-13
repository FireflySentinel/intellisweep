# Cleanup Catalog — macOS

This file is read by the /clean skill during the audit phase. It contains the
knowledge base of paths to scan, security patterns to detect, and tools to flag.

Community contributions welcome. See CONTRIBUTING.md (coming soon).

## Cache paths

Scan these directories. All are under user space (~/). Safe to delete — they
regenerate on demand.

### Package manager caches
| Path | Tool | Notes |
|------|------|-------|
| `~/Library/Caches/Homebrew/` | Homebrew | Old downloads. `brew cleanup` equivalent |
| `~/.npm/_cacache/` | npm | Package cache |
| `~/Library/pnpm/` | pnpm | Content-addressable store |
| `~/.bun/install/cache/` | bun | Package cache |
| `~/Library/Caches/pip/` | pip | Python package cache |
| `~/.cargo/registry/` | Cargo | Rust crate cache |
| `~/go/pkg/mod/cache/` | Go | Module cache |
| `~/.gradle/caches/` | Gradle | Build cache |
| `~/.cocoapods/repos/` | CocoaPods | Spec repos |
| `~/.gem/` | RubyGems | Gem cache |
| `~/Library/Caches/Yarn/` | Yarn | Package cache |

### IDE and editor caches
| Path | Tool | Notes |
|------|------|-------|
| `~/Library/Application Support/Cursor/Cache/` | Cursor | Browser cache |
| `~/Library/Application Support/Cursor/CachedData/` | Cursor | Extension cache |
| `~/Library/Application Support/Cursor/CachedExtensionVSIXs/` | Cursor | Old extension packages |
| `~/Library/Application Support/Cursor/WebStorage/` | Cursor | Web storage |
| `~/Library/Application Support/Code/Cache/` | VS Code | Browser cache |
| `~/Library/Application Support/Code/CachedData/` | VS Code | Extension cache |
| `~/Library/Application Support/Code/CachedExtensionVSIXs/` | VS Code | Old extension packages |
| `~/Library/Application Support/Code/Service Worker/` | VS Code | Service worker cache |
| `~/Library/Caches/JetBrains/` | JetBrains IDEs | Shared cache |

### Build tool caches
| Path | Tool | Notes |
|------|------|-------|
| `~/Library/Developer/Xcode/DerivedData/` | Xcode | Build artifacts. Can be huge |
| `~/Library/Developer/CoreSimulator/` | Xcode | iOS simulators |
| `~/Library/Developer/Xcode/Archives/` | Xcode | Old app archives |
| `~/Library/Developer/Xcode/iOS DeviceSupport/` | Xcode | Device support files |
| `~/.android/cache/` | Android | Build cache |
| `~/.gradle/wrapper/dists/` | Gradle | Wrapper distributions |

### Browser and automation caches
| Path | Tool | Notes |
|------|------|-------|
| `~/Library/Caches/ms-playwright/` | Playwright | Browser binaries |
| `~/Library/Caches/camoufox/` | Camoufox | Browser automation |
| `~/Library/Caches/Google/Chrome/` | Chrome | Browser cache |
| `~/Library/Caches/Firefox/` | Firefox | Browser cache |
| `~/Library/Caches/Cypress/` | Cypress | Test browser binaries |

### AI tool caches
| Path | Tool | Notes |
|------|------|-------|
| `~/Library/Application Support/Claude/Cache/` | Claude Desktop | App cache |
| `~/Library/Application Support/Claude/Code Cache/` | Claude Desktop | Code cache |
| `~/Library/Application Support/GitHub Copilot/` | Copilot | Extension cache |

### Application caches (general)
| Path | Tool | Notes |
|------|------|-------|
| `~/Library/Application Support/Slack/Cache/` | Slack | Regenerates automatically |
| `~/Library/Application Support/Slack/Service Worker/` | Slack | Service worker cache |
| `~/Library/Caches/com.spotify.client/` | Spotify | Music cache |
| `~/Library/Application Support/discord/Cache/` | Discord | App cache |
| `~/Library/Caches/node-gyp/` | node-gyp | Native addon build cache |
| `~/Library/Caches/vscode-cpptools/` | cpptools | IntelliSense cache |

### System caches
| Path | Tool | Notes |
|------|------|-------|
| `~/Library/Logs/` | System | Log files. Check size before clearing |
| `~/Library/Caches/com.apple.dt.Xcode/` | Xcode | Apple dev cache |

## Dev tool and runtime paths

These are NOT caches. They are installed tools and SDKs. Removal means the tool
is gone and must be reinstalled. Risk level: moderate.

### Version managers and runtimes
| Path | Tool | Check |
|------|------|-------|
| `~/.nvm/` | NVM (Node) | Check `nvm ls` for installed versions |
| `~/.fnm/` | fnm (Node) | Check `fnm ls` |
| `~/.pyenv/` | pyenv (Python) | Check `pyenv versions` |
| `~/.cargo/` | Rust | Entire Rust toolchain |
| `~/go/` | Go | GOPATH workspace |
| `~/flutter/` | Flutter SDK | Check `flutter --version` |
| `~/Library/Android/sdk/` | Android SDK | Check for Android Studio |
| `~/.cocoapods/` | CocoaPods | Spec repos + gem |
| `~/.rbenv/` | rbenv (Ruby) | Check `rbenv versions` |
| `~/miniconda3/` | Miniconda | Python env manager |
| `~/anaconda3/` | Anaconda | Full Python distribution |
| `/opt/homebrew/anaconda3/` | Anaconda (brew) | Brew-installed Anaconda |
| `/opt/anaconda3/` | Anaconda | System-level install |
| `~/.bun/` | Bun | Runtime + package manager |

### Containers and VMs
| Path | Tool | Check |
|------|------|-------|
| `~/OrbStack/` | OrbStack | Docker alternative |
| `~/.docker/` | Docker | Docker config + data |
| `~/Library/Containers/com.docker.docker/` | Docker Desktop | Container data |

## Scattered node_modules

Search for `node_modules` directories in projects. These can be reinstalled with
`npm install` / `pnpm install` / `bun install`.

```bash
find ~ -maxdepth 4 -name "node_modules" -type d 2>/dev/null
```

For each found, report:
- Parent project path
- Size of node_modules
- Last modified date of parent project
- Whether a lockfile exists (package-lock.json, pnpm-lock.yaml, bun.lockb)

## Security patterns

### Secret detection regex

Scan these files: `~/.zshrc`, `~/.bashrc`, `~/.zprofile`, `~/.zshrc.local`,
`~/.bash_profile`, `~/.profile`

Patterns (case-insensitive where noted):
| Pattern | Type | Example |
|---------|------|---------|
| `sk-[a-zA-Z0-9]{20,}` | OpenAI API key | `sk-proj-abc123...` |
| `sk-ant-[a-zA-Z0-9-]{20,}` | Anthropic API key | `sk-ant-api03-...` |
| `ghp_[a-zA-Z0-9]{36}` | GitHub personal access token | `ghp_abc123...` |
| `gho_[a-zA-Z0-9]{36}` | GitHub OAuth token | `gho_abc123...` |
| `AKIA[0-9A-Z]{16}` | AWS access key ID | `AKIAIOSFODNN7EXAMPLE` |
| `xoxb-[0-9-]+` | Slack bot token | `xoxb-123-456-abc` |
| `xoxp-[0-9-]+` | Slack user token | `xoxp-123-456-abc` |
| `(API_KEY\|SECRET\|TOKEN\|PASSWORD\|PRIVATE_KEY)=["'][^"']{8,}["']` | Generic secret assignment | `API_KEY="abc123..."` |

**IMPORTANT**: Report file path and line number only. NEVER echo the actual secret value.

### Credential files

Check if these exist and report their age:
| Path | Type | Risk |
|------|------|------|
| `~/.netrc` | HTTP credentials (git, curl) | Flag if exists |
| `~/.aws/credentials` | AWS credentials | Flag if exists |
| `~/.docker/config.json` | Docker registry auth | Flag if contains `auth` key |
| `~/.npmrc` | npm registry auth | Flag if contains `_authToken` |
| `~/.pypirc` | PyPI credentials | Flag if exists |
| `~/.kube/config` | Kubernetes config | Flag if contains `token` or `client-certificate` |

### SSH keys

```bash
ls -la ~/.ssh/*.pub 2>/dev/null
```

For each key, report:
- Key file name and age (mtime of .pub file)
- Whether it's referenced in `~/.ssh/config`
- Whether it's an RSA key < 2048 bits (weak)

Flag keys where: .pub mtime > 2 years AND not referenced in ~/.ssh/config.

## Modernization flags

These tools are deprecated or have better modern alternatives. Flag them if
installed but do NOT auto-replace in MVP.

| Tool | Status | Replacement | Detection |
|------|--------|-------------|-----------|
| neofetch | Archived (2024) | fastfetch | `which neofetch` |
| nvm | Slow, broken shell hooks | fnm | `[ -d ~/.nvm ]` AND errors in shell init |
| pyenv | Slow for version-only use | uv | `which pyenv` AND only system + 1 version |
| yarn v1 | Legacy | pnpm or bun | `yarn --version` starts with `1.` |
| bower | Abandoned | npm/pnpm | `which bower` |

Only flag NVM if broken shell integration is detected (look for `_load_nvm` errors
or shell startup warnings).

## AI-era tools (special handling)

These are unique to the AI development era. No existing cleanup tool knows about them.

| Path | Tool | Notes |
|------|------|-------|
| `~/Library/Application Support/Claude/vm_bundles/` | Claude Desktop | Linux VM for sandboxed execution. 10-12GB. Only one version needed. DO NOT delete the active version. Only flag if multiple versions exist. |
| `~/Library/Application Support/Cursor/` | Cursor IDE | Check Cache, CachedData, CachedExtensionVSIXs, WebStorage subdirs. Keep User/ (settings). |
| `~/.claude/` | Claude Code | Config and skills. DO NOT delete. Only flag if > 500MB (unusual). |
| `~/.gstack/` | gstack | Config and analytics. DO NOT delete. |

**WARNING for Claude VM bundles**: The active VM bundle is required for Claude Desktop
to work. Only suggest cleanup if there are multiple versions. If only one exists,
skip it entirely with a note: "Claude VM bundle (XGB) — active, do not remove."
