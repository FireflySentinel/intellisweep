# IntelliSweep Catalog — macOS

This file defines what Claude Code must NEVER touch (safeguards), what it should
recognize when it finds things (knowledge base), and what to flag proactively
(security patterns, deprecations).

The catalog is a reference, not a script. Claude Code explores the machine freely
and uses this file to make informed decisions about what it discovers.

Community contributions welcome. See CONTRIBUTING.md (coming soon).

---

## SAFEGUARDS — Never touch these

These paths must NEVER be deleted, modified, or suggested for removal. If Claude Code
encounters them during exploration, skip them silently or note them as protected.

### User data (destructive = unrecoverable)
| Path | Why |
|------|-----|
| `~/Documents/` | User documents |
| `~/Desktop/` | User desktop files |
| `~/Pictures/` | User photos |
| `~/Movies/` | User videos |
| `~/Music/` | User music (iTunes/Apple Music library) |
| `~/Zotero/` | Research library (academic users) |

### Active development (deletion = lost work)
| Path | Why |
|------|-----|
| Any directory with a `.git/` and uncommitted changes | Active project with unsaved work |
| Any directory with recent git commits (< 30 days) | Actively maintained project |
| `~/Library/Application Support/*/User/` | IDE settings, keybindings, snippets (Cursor, VS Code, etc.) |

### Credentials and identity (deletion = lockout)
| Path | Why |
|------|-----|
| `~/.ssh/` | SSH keys. Deletion = locked out of servers and Git. ALERT only, never delete. |
| `~/.gnupg/` or `~/.gpg/` | GPG keys. Used for Git commit signing. |
| `~/.aws/` | AWS credentials and config. ALERT only. |
| `~/.kube/` | Kubernetes config. ALERT only. |
| `~/.netrc` | HTTP auth credentials. ALERT only. |
| `~/.docker/config.json` | Docker registry auth tokens. ALERT only. |
| `~/.npmrc` | npm auth tokens. ALERT only. |
| `~/.pypirc` | PyPI credentials. ALERT only. |
| `~/Library/Keychains/` | macOS Keychain. System-critical. |

### System and tool config (deletion = broken environment)
| Path | Why |
|------|-----|
| `~/.claude/` | Claude Code config and skills. NEVER delete. |
| `~/.gstack/` | gstack config and analytics. NEVER delete. |
| `~/.zshrc`, `~/.bashrc`, `~/.zprofile`, `~/.bash_profile` | Shell configs. May EDIT to fix broken entries, never delete the file. |
| `~/.gitconfig` | Git config. |
| `~/.config/` (root level) | XDG config directory. Individual subdirs may be cleanable, but never delete the directory itself. |
| `~/Library/Preferences/` | macOS app preferences. Deletion breaks app settings. |
| `~/Library/Keychains/` | macOS Keychain data. |

### Active app data (deletion = data loss)
| Path | Why |
|------|-----|
| `~/Library/Application Support/Claude/vm_bundles/` | Active VM bundle required for Claude Desktop. Only flag if MULTIPLE versions exist (suggest removing old ones, keeping the latest). If only one version, skip entirely. |
| `~/Library/Application Support/Cursor/User/` | Cursor settings, extensions, keybindings. Keep this even when clearing Cursor caches. |
| `~/Library/Application Support/Code/User/` | VS Code settings, extensions, keybindings. |
| Any `~/Library/Containers/*/Data/` where the parent app is currently installed | App may need its container data. Check if the app is still in /Applications/ before suggesting removal. |

### System paths (SIP-protected, cannot modify anyway)
| Path | Why |
|------|-----|
| `/System/` | macOS system. SIP-protected. |
| `/usr/` (except `/usr/local/`) | System binaries. |
| `/Library/` (system-level) | System-level library. Requires sudo. |
| `/Applications/` | Installed apps. Suggest uninstall via brew or app's own uninstaller, never `rm`. |

---

## KNOWLEDGE BASE — Recognize and advise

When Claude Code discovers these during exploration, use this info to provide
informed cleanup advice. This is NOT a scan checklist. Claude Code may find items
not listed here and should use its own judgment for those.

### Cache patterns (Safe risk — regenerates on demand)
| Pattern | What it is | Safe to delete? |
|---------|-----------|-----------------|
| `~/Library/Caches/*/` | App caches | Yes, apps regenerate them |
| `~/Library/Caches/Homebrew/` | Brew downloads | Yes. Equivalent of `brew cleanup` |
| `~/.npm/_cacache/` | npm package cache | Yes |
| `~/Library/pnpm/` | pnpm content store | Yes, but active projects may need reinstall |
| `~/.bun/install/cache/` | bun cache | Yes |
| `~/Library/Caches/pip/` | pip cache | Yes |
| `~/.cargo/registry/` | Rust crate cache | Yes, redownloads on build |
| `~/go/pkg/mod/cache/` | Go module cache | Yes |
| `~/.gradle/caches/` | Gradle cache | Yes, rebuilds on next build |
| `~/Library/Caches/ms-playwright/` | Playwright browsers | Yes, redownloads on test run |
| `~/Library/Developer/Xcode/DerivedData/` | Xcode build artifacts | Yes. Can be huge. |
| `~/Library/Developer/CoreSimulator/` | iOS simulators | Yes if not doing iOS dev |
| `*/node_modules/` | npm/pnpm/bun deps | Yes if lockfile exists (reinstall with `npm i`) |
| IDE `Cache/`, `CachedData/`, `CachedExtensionVSIXs/`, `WebStorage/`, `Service Worker/` | Editor caches | Yes. Keep `User/` directory (settings). |
| `~/Library/Logs/` | System logs | Generally safe. Check size first. |

### Dev tool patterns (Moderate risk — requires reinstall)
| Pattern | What to check |
|---------|--------------|
| `~/.nvm/`, `~/.fnm/`, `~/.pyenv/`, `~/.rbenv/` | Version managers. Check how many versions installed, when last used. |
| `~/.cargo/` (whole dir, not just registry) | Full Rust toolchain. Only suggest if user confirms no Rust projects. |
| `~/flutter/` | Flutter SDK. Check git log for last activity. |
| `~/Library/Android/sdk/` | Android SDK. Check if Android Studio or Flutter is actively used. |
| `~/anaconda3/`, `~/miniconda3/`, `/opt/homebrew/anaconda3/` | Python distributions. Check if conda is referenced in shell config. |
| `~/OrbStack/` | Docker alternative data. Only if not using Docker. |
| `~/.cocoapods/` | CocoaPods. Only needed for iOS/Flutter dev. |

### App container patterns (Moderate to Destructive)
| Pattern | What to check |
|---------|--------------|
| `~/Library/Containers/*/` | Sandboxed app data. Check if parent app is still installed. If app was deleted, container data is orphaned and safe to remove. |
| `~/Library/Application Support/*/` | App data. Some is cache (safe), some is user data (destructive). Check subdirectory names for `Cache`, `Caches`, `WebStorage` vs `User`, `Data`, `Library`. |
| `~/Library/Application Support/Steam/` | Game data. Only if user isn't gaming on this machine. |

---

## SECURITY PATTERNS — Alert only, never remediate

### Secret detection regex

Scan shell config files: `~/.zshrc`, `~/.bashrc`, `~/.zprofile`, `~/.zshrc.local`,
`~/.bash_profile`, `~/.profile`

| Pattern | Type |
|---------|------|
| `sk-[a-zA-Z0-9]{20,}` | OpenAI API key |
| `sk-ant-[a-zA-Z0-9-]{20,}` | Anthropic API key |
| `ghp_[a-zA-Z0-9]{36}` | GitHub personal access token |
| `gho_[a-zA-Z0-9]{36}` | GitHub OAuth token |
| `AKIA[0-9A-Z]{16}` | AWS access key ID |
| `xoxb-[0-9-]+` | Slack bot token |
| `xoxp-[0-9-]+` | Slack user token |
| `(API_KEY\|SECRET\|TOKEN\|PASSWORD\|PRIVATE_KEY)=["'][^"']{8,}["']` | Generic secret |

**IMPORTANT**: Report file path and line number only. NEVER echo the actual secret value.

### SSH key audit
Flag keys where: `.pub` file mtime > 2 years AND not referenced in `~/.ssh/config`.
Flag RSA keys < 2048 bits as weak.

### Credential file age
For each file in the Safeguards credential list that exists, report its age.
Old credentials that haven't been rotated are a security risk.

---

## EXPLORATION HINTS — Where to look for big wins

These aren't paths to check. They're patterns that tend to reveal hidden space hogs
during discovery. Claude Code should explore these areas and use judgment.

- `du -sh ~/Library/Application\ Support/*/` — app data often grows silently (Slack 1.4GB, Steam 1.9GB)
- `du -sh ~/Library/Containers/*/` — sandboxed apps can accumulate GB of data (games, messaging apps)
- `find ~ -maxdepth 4 -name "node_modules" -type d` — scattered across old projects
- `find ~ -maxdepth 3 -name ".venv" -o -name "venv"` — Python virtual environments in old projects
- `brew list --cask` — installed GUI apps, some may be abandoned
- `brew list --formula | wc -l` — if > 100, likely has unused formulas
- `ls ~/Downloads/` — forgotten downloads, old installers
