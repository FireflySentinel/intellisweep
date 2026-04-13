# Show HN: IntelliSweep — I replaced 10 years of Mac cleaner apps with a Claude Code skill

## Title (keep under 80 chars)

Show HN: IntelliSweep — AI-powered dev environment cleanup as a Claude Code skill

## Post body

For 10 years I used ad-filled Mac cleaner apps (CleanMyMac, CCleaner, etc.) to free disk space on my MacBook. They all run a fixed checklist, scan the same paths, and charge you $40/year for the privilege.

Last week I asked Claude Code to clean my machine instead. In one conversation it recovered 36GB. Not by running a checklist, but by actually understanding my dev environment:

- Detected Flutter SDK hadn't been used since February (checked git log dates, not just file timestamps)
- Found NVM had broken shell integration (caught `_load_nvm` errors in shell startup)
- Discovered Anaconda was installed via Homebrew but never activated (the conda command was a lazy-load function pointing to a dead path)
- Flagged a hardcoded OpenAI API key in my .zshrc
- Replaced NVM with fnm, cleaned dead references from my shell config

No static script could do any of this. It requires reasoning about context: when was a tool last used? Is the configuration broken? What's the modern replacement?

I packaged this into a Claude Code skill called IntelliSweep. It's 4 text files (SKILL.md, catalog.md, TESTING.md, README.md). You install it with `git clone`. No binary, no build step, no dependencies.

What it does:
- Audits your machine using multiple staleness signals (file timestamps, git activity, shell history, brew install dates)
- Presents findings in risk-tiered categories with evidence strings
- Cleans one item at a time with explicit confirmation
- Backs up before destructive operations with a manifest for restore
- Has a --dry-run mode that shows everything without touching anything

What it doesn't do:
- No signature-based malware scanning (security findings are alert-only)
- No Windows/Linux yet (macOS only for v1, Linux planned)
- No persistent machine memory (v2 vision)

The interesting question for HN: every Mac cleanup tool is a static checklist. AI changes this from "does this path exist?" to "should this path exist?" Is this a pattern that generalizes beyond cleanup?

GitHub: https://github.com/FireflySentinel/intellisweep

## Notes for posting

- Post on a weekday morning (US time), ideally Tuesday-Thursday 9-11am ET
- The personal story angle ("10 years of crappy cleaner apps") is the hook
- The technical angle ("AI reasoning vs static checklists") is the HN bait
- The closing question invites discussion
- Have screenshots ready: the triage output showing permanent vs temporary wins
- The "no cleanup theater" angle will resonate on HN. Everyone hates CleanMyMac's inflated numbers.
- Be ready to answer: "why not just a shell script?" (the intelligence IS the product)
- Be ready to answer: "isn't this just prompt engineering?" (yes, that's the point, the runtime is Claude Code)
- Be ready to answer: "caches refill, what's the point?" (we agree! that's why we label permanence and lead with permanent wins)
