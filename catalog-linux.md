# IntelliSweep Catalog — Linux

> **Status: Experimental.** This catalog has not been tested on real long-lived Linux
> machines. Community contributions and bug reports welcome.

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

### Credentials and identity
| Path | Why |
|------|-----|
| `~/.ssh/` | SSH keys. ALERT only. |
| `~/.gnupg/` | GPG keys. |
| `~/.aws/` | AWS credentials. ALERT only. |
| `~/.kube/` | Kubernetes config. ALERT only. |
| `~/.netrc` | HTTP credentials. ALERT only. |
| `~/.docker/config.json` | Docker registry auth. ALERT only. |
| `~/.npmrc` | npm auth tokens. ALERT only. |
| `~/.pypirc` | PyPI credentials. ALERT only. |

### System and tool config
| Path | Why |
|------|-----|
| `~/.claude/` | Claude Code config and skills. NEVER delete. |
| `~/.gstack/` | gstack config. NEVER delete. |
| `~/.bashrc`, `~/.zshrc`, `~/.profile`, `~/.bash_profile` | Shell configs. May EDIT, never delete. |
| `~/.gitconfig` | Git config. |
| `~/.config/` (root) | XDG config directory. Individual subdirs may be cleanable, never the root. |
| `~/.local/share/keyrings/` | GNOME keyring. |

### Active development
| Path | Why |
|------|-----|
| Any directory with `.git/` and uncommitted changes | Active project with unsaved work |
| Any directory with recent git commits (< 30 days) | Actively maintained project |

---

## KNOWLEDGE BASE — Recognize and advise

### Cache patterns (Safe — regenerates on demand)
| Pattern | What it is |
|---------|-----------|
| `~/.cache/` (most subdirs) | XDG cache. Generally safe to clear. |
| `~/.cache/pip/` | pip cache |
| `~/.cache/yarn/` | Yarn cache |
| `~/.cache/pnpm/` | pnpm cache |
| `~/.cache/Homebrew/` | Linuxbrew cache |
| `~/.cache/JetBrains/` | JetBrains IDE cache |
| `~/.cache/google-chrome/` | Chrome cache |
| `~/.cache/mozilla/` | Firefox cache |
| `~/.cache/ms-playwright/` | Playwright browsers |
| `~/.cache/cypress/` | Cypress browsers |
| `~/.local/share/Trash/` | Trash. Emptying is permanent. |
| `~/.npm/_cacache/` | npm cache |
| `~/.bun/install/cache/` | bun cache |
| `~/.cargo/registry/` | Rust crate cache |
| `~/go/pkg/mod/cache/` | Go module cache |
| `~/.gradle/caches/` | Gradle cache |
| `*/node_modules/` | npm/pnpm/bun deps. Reinstall from lockfile. |

### Dev tool patterns (Moderate — requires reinstall)
| Pattern | What to check |
|---------|--------------|
| `~/.nvm/`, `~/.fnm/`, `~/.pyenv/`, `~/.rbenv/` | Version managers. Check versions installed. |
| `~/.cargo/` (whole dir) | Full Rust toolchain. |
| `~/.local/share/flutter/` or `~/flutter/` | Flutter SDK. |
| `~/Android/Sdk/` | Android SDK. |
| `~/anaconda3/`, `~/miniconda3/` | Python distributions. |
| `~/.local/share/JetBrains/Toolbox/` | JetBrains Toolbox apps. |

### Package manager detection
| Check | Package manager |
|-------|----------------|
| `which apt` | apt (Debian/Ubuntu) |
| `which dnf` | dnf (Fedora/RHEL) |
| `which pacman` | pacman (Arch) |
| `which zypper` | zypper (openSUSE) |
| `which nix` | Nix |
| `which brew` | Linuxbrew |

Use the detected package manager for inventory checks (`apt list --installed`, etc.)

---

## SECURITY PATTERNS — Alert only

Same regex patterns as macOS catalog. Scan:
`~/.bashrc`, `~/.zshrc`, `~/.profile`, `~/.bash_profile`, `~/.zshrc.local`

---

## EXPLORATION HINTS

- `du -sh ~/.cache/*/ | sort -hr` — XDG cache breakdown
- `du -sh ~/.local/share/*/ | sort -hr` — XDG data breakdown
- `du -sh ~/.config/*/ | sort -hr` — XDG config breakdown (some apps store data here)
- `find ~ -maxdepth 4 -name "node_modules" -type d` — scattered node_modules
- `docker system df` — Docker disk usage (if Docker installed)
- `journalctl --disk-usage` — systemd journal size
- `snap list` — installed snaps (can be large)
- `flatpak list` — installed Flatpaks
