# IntelliSweep Catalog — Windows

> **Status: Experimental.** This catalog has not been tested on real long-lived Windows
> machines. Community contributions and bug reports welcome.
>
> **Note:** Windows support requires Claude Code on Windows (PowerShell environment).
> Commands in this catalog use PowerShell syntax where they differ from Unix.

---

## SAFEGUARDS — Never touch these

### User data
| Path | Why |
|------|-----|
| `~/Documents/` | User documents |
| `~/Desktop/` | User desktop files |
| `~/Pictures/` | User photos |
| `~/Videos/` | User videos |
| `~/Music/` | User music |
| `~/OneDrive/` | Cloud-synced files. Deletion may propagate. |

### Credentials and identity
| Path | Why |
|------|-----|
| `~/.ssh/` | SSH keys. ALERT only. |
| `~/.gnupg/` | GPG keys. |
| `~/.aws/` | AWS credentials. ALERT only. |
| `~/.kube/` | Kubernetes config. ALERT only. |
| `~/.docker/config.json` | Docker registry auth. ALERT only. |
| `~/.npmrc` | npm auth tokens. ALERT only. |
| `$env:APPDATA\Microsoft\Credentials\` | Windows credential store. |

### System and tool config
| Path | Why |
|------|-----|
| `~/.claude/` | Claude Code config and skills. NEVER delete. |
| `~/.gstack/` | gstack config. NEVER delete. |
| `$env:USERPROFILE\.gitconfig` | Git config. |
| `$env:APPDATA\Code\User\` | VS Code settings. |
| `$env:APPDATA\Cursor\User\` | Cursor settings. |
| Registry | NEVER modify Windows Registry. |

### Active development
| Path | Why |
|------|-----|
| Any directory with `.git\` and uncommitted changes | Active project with unsaved work |
| Any directory with recent git commits (< 30 days) | Actively maintained project |

---

## KNOWLEDGE BASE — Recognize and advise

### Cache patterns (Safe — regenerates on demand)
| Pattern | What it is |
|---------|-----------|
| `$env:LOCALAPPDATA\Temp\` | Windows temp files |
| `$env:LOCALAPPDATA\npm-cache\` | npm cache |
| `$env:LOCALAPPDATA\pnpm\store\` | pnpm store |
| `$env:LOCALAPPDATA\bun\install\cache\` | bun cache |
| `$env:LOCALAPPDATA\pip\Cache\` | pip cache |
| `~\.cargo\registry\` | Rust crate cache |
| `~\go\pkg\mod\cache\` | Go module cache |
| `~\.gradle\caches\` | Gradle cache |
| `$env:LOCALAPPDATA\Google\Chrome\User Data\Default\Cache\` | Chrome cache |
| `$env:LOCALAPPDATA\Microsoft\Edge\User Data\Default\Cache\` | Edge cache |
| `$env:LOCALAPPDATA\Cypress\Cache\` | Cypress browsers |
| `$env:LOCALAPPDATA\ms-playwright\` | Playwright browsers |
| `$env:APPDATA\Code\Cache\` | VS Code cache |
| `$env:APPDATA\Code\CachedData\` | VS Code cached data |
| `$env:APPDATA\Code\CachedExtensionVSIXs\` | VS Code extension cache |
| `$env:APPDATA\Cursor\Cache\` | Cursor cache |
| `$env:APPDATA\Cursor\CachedData\` | Cursor cached data |
| `$env:APPDATA\Slack\Cache\` | Slack cache |
| `$env:APPDATA\discord\Cache\` | Discord cache |
| `*/node_modules/` | npm/pnpm/bun deps. Reinstall from lockfile. |

### Dev tool patterns (Moderate — requires reinstall)
| Pattern | What to check |
|---------|--------------|
| `~\.nvm\` or `$env:NVM_HOME` | NVM for Windows |
| `~\.fnm\` | fnm |
| `~\.pyenv\` | pyenv-win |
| `~\.cargo\` | Full Rust toolchain |
| `~\flutter\` | Flutter SDK |
| `$env:LOCALAPPDATA\Android\Sdk\` | Android SDK |
| `~\anaconda3\`, `~\miniconda3\` | Python distributions |
| `$env:LOCALAPPDATA\JetBrains\Toolbox\` | JetBrains IDEs |

### Package manager detection
| Check | Package manager |
|-------|----------------|
| `Get-Command winget` | winget (modern Windows) |
| `Get-Command choco` | Chocolatey |
| `Get-Command scoop` | Scoop |

### Windows-specific large items
| Path | What it is |
|------|-----------|
| `C:\Windows\Installer\` | Windows Installer cache (caution: may break uninstallers) |
| `$env:LOCALAPPDATA\Docker\wsl\` | Docker Desktop WSL data |
| WSL distributions (`wsl --list -v`) | Linux distributions. Can be huge. Check which are active. |
| `$env:LOCALAPPDATA\Packages\` | Windows Store app data |

### Disk usage commands (PowerShell equivalents)
| Unix | PowerShell |
|------|------------|
| `du -sh` | `(Get-ChildItem -Recurse \| Measure-Object -Property Length -Sum).Sum / 1GB` |
| `df -h /` | `Get-PSDrive C \| Select Used,Free` |
| `find ~ -name X` | `Get-ChildItem -Path ~ -Recurse -Filter X` |
| `stat -f "%Sm"` | `(Get-Item path).LastWriteTime` |

---

## SECURITY PATTERNS — Alert only

Same regex patterns as macOS/Linux catalogs. Scan:
`$env:USERPROFILE\.bashrc`, `$env:USERPROFILE\.zshrc`, `$env:USERPROFILE\.profile`,
PowerShell profile: `$PROFILE`

---

## EXPLORATION HINTS

- `Get-ChildItem $env:LOCALAPPDATA -Directory | Sort-Object { (Get-ChildItem $_.FullName -Recurse -File | Measure-Object Length -Sum).Sum } -Descending | Select -First 20` — biggest AppData\Local dirs
- `Get-ChildItem $env:APPDATA -Directory | Sort-Object ...` — biggest AppData\Roaming dirs
- `wsl --list -v` — WSL distributions and their state
- `docker system df` — Docker disk usage
- `winget list` — installed apps via winget
