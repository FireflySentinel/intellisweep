# intellisweep

AI-powered dev environment cleanup for Mac. A Claude Code skill that replaces crappy cleaner apps.

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
/intellisweep              # Full scan + interactive cleanup
/intellisweep --dry-run    # Scan only, no cleanup (show what it would find)
```

## What it finds

| Category | Examples |
|----------|---------|
| Stale dev tools | Flutter SDK unused for 6 months, Android SDK with no IDE |
| Broken configs | Dead PATH entries, broken lazy-loaders, references to deleted tools |
| Large caches | Homebrew cache, Playwright browsers, npm/pnpm stores, Xcode derived data |
| AI tool caches | Claude VM bundles, Cursor WebStorage, Copilot cache |
| Security flags | Hardcoded API keys, old SSH keys, stale credential files |
| Modernization | Deprecated tools (neofetch, NVM with broken hooks) |

## Safety

- **Backup before delete**: Moderate and destructive items are backed up to `~/.devclean-backup/` with a manifest for easy restore
- **One at a time**: Every deletion is confirmed individually. No batch deletes.
- **Rolling cleanup**: Safe items first (frees space), then moderate items one-by-one
- **Alert-only security**: Credential files are flagged, never modified or deleted
- **Dry-run mode**: `/intellisweep --dry-run` shows everything without touching anything

## Requirements

- macOS (Linux support planned)
- [Claude Code](https://claude.ai/download)
- Standard macOS tools (du, stat, find, df)
- Homebrew (optional, graceful degradation if missing)

## Contributing

Want to add paths for a tool not yet covered? Edit `catalog.md` and submit a PR.
The format is a markdown table with path, tool name, and notes.

## License

MIT
