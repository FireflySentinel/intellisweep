# intellisweep

AI-powered dev environment cleanup for Mac. A Claude Code skill that replaces crappy cleaner apps.

## Table of contents

- [The story](#the-story)
- [What it does](#what-it-does)
- [Install](#install)
- [Usage](#usage)
- [What it finds](#what-it-finds)
- [Principles](#principles)
- [Requirements](#requirements)
- [Roadmap](#roadmap)
- [Contributing](#contributing)
- [Uninstall](#uninstall)
- [License](#license)

## The story

For 10 years I used ad-filled Mac cleaner apps to free disk space. Then I asked Claude Code to clean my machine. In one conversation it recovered 36GB by detecting stale tools (Flutter unused for months), broken configs (NVM with shell errors), abandoned package managers (Anaconda never activated), and flagged a hardcoded API key in my .zshrc. It replaced NVM with fnm, cleaned dead shell references, and modernized my dev environment.

No static script could do this. This skill packages that experience.

## What it does

- Audits installed dev tools, caches, configs, and credential files
- Detects stale tools by checking git activity, shell history, and file timestamps
- Identifies broken configurations by analyzing shell output
- Flags security issues (hardcoded secrets, old SSH keys)
- Presents findings in risk-tiered categories with evidence
- Cleans only with explicit confirmation, one item at a time
- Backs up before destructive operations with manifest-based restore

## Install

```bash
git clone https://github.com/FireflySentinel/intellisweep.git ~/.claude/skills/intellisweep
```

That's it. No build step, no dependencies.

## Usage

```
/intellisweep              # Fast scan (< 2 min) + interactive cleanup
/intellisweep --deep       # Thorough scan (< 5 min) + cleanup
/intellisweep --dry-run    # Fast scan, no cleanup (just show findings)
```

## What it finds

| Category | Examples |
|----------|---------|
| Stale dev tools | Flutter SDK unused for 6 months, Android SDK with no IDE |
| Broken configs | Dead PATH entries, broken lazy-loaders, references to deleted tools |
| Large caches | Homebrew cache, Playwright browsers, npm/pnpm stores, Xcode derived data |
| AI tool caches | Claude VM bundles, Cursor WebStorage, Copilot cache |
| App data cruft | Orphaned containers from deleted apps, abandoned game data |
| Security flags | Hardcoded API keys, old SSH keys, stale credential files |

## Principles

- **Open source, free forever.** No ads, no VIP tiers, no subscription. MIT licensed.
- **No cleanup theater.** Every finding shows permanence. We'd rather show 4GB of real wins than 12GB that refills by Tuesday.
- **Nothing is a black box.** You see every command before it runs. You know exactly what's being removed and why. No progress bars hiding mystery deletions.
- **Everything is reversible.** Backup with manifest before any destructive operation. One command to restore. If you regret something, undo it.
- **Your data stays on your machine.** No telemetry, no analytics, no phoning home. The skill reads local files with local commands. The source is 4 text files you can read in 5 minutes.
- **No fear-based messaging.** Your machine isn't "at risk" because it has a browser cache. We tell you what's there, what it is, and let you decide.

## Requirements

- macOS (Linux support planned)
- [Claude Code](https://claude.ai/download)
- Standard macOS tools (du, stat, find, df)
- Homebrew (optional, graceful degradation if missing)

## Roadmap

**v1.0** (current) — macOS cleanup. Audit, triage, clean with backup safety model. Security alerts (flag-only).

**v1.1** — Linux support. `catalog-linux.md` with XDG paths, apt/dnf/pacman detection. CONTRIBUTING.md for community catalog contributions.

**v2.0** — `intellisweep modernize`. Tool swap workflows (NVM to fnm, pyenv to uv, neofetch to fastfetch). Config migration, shell config updates, project compatibility verification. Separated from cleanup because tool swaps are harder and riskier than deletions.

**v3.0** — Dev environment doctor. Persistent machine manifest (`~/.intellisweep/manifest.json`) that tracks your environment across runs. Deep security scanning (LaunchAgents, browser extensions). "New Mac setup" mode. Cross-machine awareness.

The long-term vision: IntelliSweep becomes Claude Code's understanding of the physical machine it runs on.

## Contributing

Want to add paths for a tool not yet covered? Edit `catalog.md` and submit a PR.
The format is a markdown table with path, tool name, and notes.

## Uninstall

```bash
rm -rf ~/.claude/skills/intellisweep
```

To also remove backup data and reports:

```bash
rm -rf ~/.devclean-backup
rm -f ~/.devclean-report-*.md
```

## License

MIT
