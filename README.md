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

## How it works

IntelliSweep explores your machine instead of checking a static list. It runs `du` across your home directory and Library, finds what's actually taking space, then reasons about each item: when was it last used? Is the parent app still installed? Is the config referencing something that no longer exists?

Findings are sorted by permanence, not size. A 500MB stale SDK that's gone forever ranks above a 2GB browser cache that refills by tomorrow.

A safeguard list protects things that must never be touched: your documents, SSH keys, active project data, IDE settings, and credentials. Everything outside the safeguards is fair game for discovery.

## Install

```bash
git clone https://github.com/FireflySentinel/intellisweep.git ~/.claude/skills/intellisweep
```

No build step, no dependencies.

## Usage

Type `/intellisweep` in Claude Code. Three quick questions configure the run:

1. **Scan only or clean?** — choose whether to just look or actually remove things
2. **What's bugging you?** — disk space, messy environment, security, or all of the above
3. **Quick or deep?** — about 5 minutes for big wins, 10 for everything

Safe items (caches) can be batch-cleaned in one approval. Moderate items require individual confirmation with the reinstall command shown upfront. Everything is logged.

## Principles

- **Open source, free forever.** No ads, no VIP tiers, no subscription. MIT licensed.
- **No cleanup theater.** Every finding shows permanence. We'd rather show 4GB of real wins than 12GB that refills by Tuesday.
- **Nothing is a black box.** You see every command before it runs. You know exactly what's being removed and why.
- **Every deletion is logged.** A log records what was removed, how big it was, and how to reinstall it. The log is the safety net, not a backup that doubles your disk usage.
- **No telemetry, no phoning home.** IntelliSweep itself makes zero network requests. The source is a few text files you can read in 5 minutes. Note: Claude Code sends your conversation (including command output like directory sizes and path names) to Anthropic's API for processing. This is how Claude Code works for all skills, not specific to IntelliSweep.
- **No fear-based messaging.** Your machine isn't "at risk" because it has a browser cache. We tell you what's there and let you decide.

## Requirements

- macOS (Linux support planned)
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

## License

MIT
