# intellisweep

AI-powered dev environment cleanup for Mac. A [Claude Code](https://claude.ai/download) skill.

## Table of contents

- [What it does](#what-it-does)
- [How it works](#how-it-works)
- [Install](#install)
- [Usage](#usage)
- [Principles](#principles)
- [Requirements](#requirements)
- [Contributing](#contributing)
- [Uninstall](#uninstall)
- [Disclaimer](#disclaimer)
- [License](#license)

## What it does

Finds stale dev tools, broken configs, orphaned app data, large caches, and hardcoded secrets on your Mac. Tells you what's permanent and what refills tomorrow. Cleans with your approval, one item at a time.

| Category | Examples |
|----------|---------|
| Stale dev tools | SDKs unused for months, runtimes with no recent activity |
| Broken configs | Dead PATH entries, lazy-loaders referencing deleted binaries |
| Orphaned app data | Containers and support files from apps you already deleted |
| Large caches | Homebrew, Playwright, npm/pnpm stores, Xcode derived data |
| AI tool caches | Claude VM bundles, Cursor WebStorage, Copilot cache |
| Security flags | Hardcoded API keys in shell config, old SSH keys, stale credentials |
| Directory reorganization | Scattered projects in any directory, group by purpose into subcategories |

## How it works

IntelliSweep explores the current directory instead of checking a static list. It runs `du` across the working directory, finds what's actually taking space, then reasons about each item: when was it last used? Is the parent app still installed? Is the config referencing something that no longer exists?

Run from `~/` for the full machine experience (Library caches, shell config secrets, orphaned app data, dev tool inventory). Run from any other directory to scope all scans to that directory only.

Findings are sorted by permanence, not size. A 500MB stale SDK that's gone forever ranks above a 2GB browser cache that refills by tomorrow.

A safeguard list protects things that must never be touched: your documents, SSH keys, active project data, IDE settings, and credentials. Everything outside the safeguards is fair game for discovery.

**Directory reorganization** scans the current directory for scattered projects and groups them by purpose. Run it from `~/` to organize your home directory, from a projects folder to restructure repos, or from anywhere else. It detects project types from git remotes, `SKILL.md`, `package.json`, and other markers, then proposes grouping that fits the context. Everything is proposed before anything moves, symlinks are fixed automatically, and every move is logged.

## Install

```bash
git clone https://github.com/FireflySentinel/intellisweep.git ~/.claude/skills/intellisweep
```

No build step, no dependencies.

## Usage

Type `/intellisweep` in Claude Code. Three quick questions configure the run:

1. **Scan only or clean?** — choose whether to just look or actually remove things
2. **What's bugging you?** — disk space, messy environment, security, directory organization, or all
3. **Quick or deep?** — about 5 minutes for big wins, 10 for everything

Safe items (caches that regenerate) are cleaned directly by the AI. Everything else is written to a shell script you review and run yourself. The AI never executes `rm` on your SDKs, tools, or app data.

## Principles

- **Open source, free forever.** No ads, no VIP tiers, no subscription. MIT licensed.
- **No cleanup theater.** Every finding shows permanence. We'd rather show 4GB of real wins than 12GB that refills by Tuesday.
- **The AI cleans caches. You clean everything else.** Safe items (caches that regenerate) are deleted directly. Stale SDKs, tools, and app data are written to a script you review and run. The LLM never runs `rm` on non-recoverable files.
- **Every deletion is logged.** What was removed, how big it was, and how to reinstall it.
- **No telemetry, no phoning home.** IntelliSweep itself makes zero network requests. The source is a few text files you can read in 5 minutes. Note: Claude Code sends your conversation (including command output like directory sizes and path names) to Anthropic's API for processing. This is how Claude Code works for all skills, not specific to IntelliSweep.
- **No fear-based messaging.** Your machine isn't "at risk" because it has a browser cache. We tell you what's there and let you decide.

## Requirements

- macOS (tested). Linux and Windows catalogs exist but are experimental and community-contributed.
- [Claude Code](https://claude.ai/download) (requires an Anthropic subscription)
- Standard macOS tools (du, stat, find, df)
- Homebrew (optional, graceful degradation if missing)

This is a Claude Code skill. The runtime is Claude Code. The intelligence is in the prompt. A bash script can clear caches, but it won't detect orphaned app data from deleted apps, won't know an SDK hasn't been used in 6 months, won't find hardcoded API keys in your shell config, and won't know not to delete your SSH keys.

## Contributing

Add paths for tools not yet covered by editing `catalog.md` and submitting a PR.

## Uninstall

```bash
rm -rf ~/.claude/skills/intellisweep
```

To also remove logs:

```bash
rm -rf ~/.intellisweep
```

## Disclaimer

This tool deletes files from your machine. While it asks for confirmation before every action and logs what it removes, **you are responsible for reviewing what gets deleted.** We recommend running a scan-only pass first (`select "Just looking"`) before cleaning anything. The authors are not responsible for any data loss. Use at your own risk.

## License

MIT
