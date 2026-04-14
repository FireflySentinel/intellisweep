# Testing Guide

/intellisweep is a Claude Code skill (prompt engineering, not compiled code). It cannot be
unit tested. This document defines manual test scenarios for QA validation.

Run each scenario before releasing a new version. Report results in the PR.

## Scenario 1: Typical developer Mac

**Setup**: A Mac with 6+ months of dev tool usage. Multiple languages installed,
at least one stale tool, some caches.

**Steps**:
1. Run `/intellisweep`, select "scan and clean", "all of the above", "quick"
2. Verify: Audit completes in < 5 minutes
3. Verify: Findings appear in 3+ categories, sorted by permanence
4. Verify: Evidence strings are accurate (dates match `stat`, sizes match `du`)
5. Verify: Safe items offered as batch, moderate items require individual confirm
6. Clean a few items
7. Verify: Live disk space shown after each deletion
8. Verify: Log written to `~/.intellisweep/log-YYYY-MM-DD.md`
9. Verify: Log contains deleted item paths, sizes, and reinstall commands

**Pass criteria**: Accurate findings, correct permanence labels, log is complete.

## Scenario 2: Near-full disk (< 5GB free)

**Setup**: A Mac with < 5GB free space.

**Steps**:
1. Run `/intellisweep`, select "scan and clean", "free disk space", "quick"
2. Verify: Audit completes (may be slower under disk pressure)
3. Verify: Items < 100MB are filtered out (disk space goal)
4. Verify: Safe items cleaned first without issue
5. Verify: Moderate items deletable one by one, no disk-full errors

**Pass criteria**: Works on a nearly-full disk without errors.

## Scenario 3: No Homebrew installed

**Setup**: A Mac without Homebrew.

**Steps**:
1. Run `/intellisweep`
2. Verify: No crash when brew commands fail
3. Verify: Output mentions skipping brew-related checks
4. Verify: Other categories still scanned

**Pass criteria**: Graceful degradation.

## Scenario 4: Deletion confirmation flow

**Steps**:
1. Run `/intellisweep`, select "scan and clean"
2. Verify: Safe items show batch option [Clean all] [Let me pick] [Skip]
3. Choose "Let me pick" — verify each safe item shows [Delete] [Skip]
4. Verify: Moderate items always show individual [Delete] [Skip]
5. Verify: Possible orphans show [Delete] [Skip] [What is this?]
6. Verify: Security alerts have NO delete option (alert only)

**Pass criteria**: Correct confirmation tier per risk level.

## Scenario 5: Partial cancellation

**Steps**:
1. Run `/intellisweep`, select several items for cleanup
2. After 2-3 items cleaned, cancel (Ctrl+C or tell Claude to stop)
3. Verify: Cleaned items are gone, others untouched
4. Verify: Log shows what was cleaned so far

**Pass criteria**: Partial state is consistent.

## Scenario 6: Clean machine

**Setup**: A relatively fresh Mac with minimal dev tools.

**Steps**:
1. Run `/intellisweep`
2. Verify: Audit completes quickly
3. Verify: Few or no findings
4. Verify: Handles "nothing to find" gracefully

**Pass criteria**: No empty tables, no confusing output.

## Scenario 7: Scan only (dry run)

**Steps**:
1. Run `/intellisweep`, select "scan only"
2. Verify: Full triage report presented
3. Verify: No cleanup offered, no files deleted
4. Verify: No log file created (nothing was deleted)

**Pass criteria**: Zero side effects.

## Scenario 8: Secret detection safety

**Steps**:
1. Add a test secret to a shell config (e.g., `export TEST_KEY="sk-test123..."`)
2. Run `/intellisweep`
3. Verify: Security alert shows file and line number
4. Verify: The actual secret value NEVER appears in Claude's output
5. Verify: Output says something like `~/.zshrc:18:OpenAI/Anthropic key`
6. Clean up the test secret

**Pass criteria**: File:line:type only. Secret value never in stdout or Claude context.

## Scenario 9: Log completeness

**Steps**:
1. Run `/intellisweep` and clean 3+ items of different risk levels
2. Read `~/.intellisweep/log-YYYY-MM-DD.md`
3. Verify: Every deleted item listed with path, size, permanence
4. Verify: "How to reinstall" column has actual commands (not empty)
5. Verify: Skipped items listed with reason
6. Verify: Security alerts listed (if any were found)

**Pass criteria**: Log is a complete record of what happened.

## Edge cases to verify

- [ ] Path with spaces (e.g., `~/Library/Application Support/`)
- [ ] Very large directory (> 10GB) — should timeout, not hang
- [ ] .zshrc with no secrets — security section empty or "no issues found"
- [ ] Multiple shell configs (.zshrc + .bashrc + .zprofile) — all scanned
- [ ] Possible orphan with ambiguous bundle ID — shows raw ID, lets user decide
- [ ] iCloud Drive present — `find` does NOT trigger placeholder downloads
