# Testing Guide

/intellisweep is a Claude Code skill (prompt engineering, not compiled code). It cannot be
unit tested. This document defines manual test scenarios for QA validation.

Run each scenario before releasing a new version. Report results in the PR.

## Scenario 1: Typical developer Mac

**Setup**: A Mac with 6+ months of dev tool usage. Multiple languages installed,
at least one stale tool, some caches.

**Steps**:
1. Run `/intellisweep`
2. Verify: Audit completes in < 5 minutes
3. Verify: Findings appear in 3+ categories
4. Verify: Total recoverable space is 10GB+ (typical dev Mac)
5. Verify: Evidence strings are accurate (dates match `stat` output, sizes match `du`)
6. Select 2-3 safe items, confirm cleanup
7. Verify: Space is freed (check `df -h /` before and after)
8. Verify: Report is generated at `~/.devclean-report-YYYY-MM-DD.md`

**Pass criteria**: Audit is accurate, cleanup works, report matches reality.

## Scenario 2: Near-full disk (< 5GB free)

**Setup**: A Mac with < 5GB free space and several large caches/stale tools.

**Steps**:
1. Run `/intellisweep`
2. Verify: Audit completes (may be slower due to disk pressure)
3. Select a mix of safe + moderate items
4. Verify: Safe items are cleaned FIRST (no backup)
5. Verify: Moderate items are cleaned one-by-one with rolling space
6. Verify: No "disk full" errors during backup operations
7. Verify: Each backup is created before its corresponding deletion

**Pass criteria**: Rolling cleanup works without hitting disk-full errors.

## Scenario 3: No Homebrew installed

**Setup**: A Mac without Homebrew (fresh install or Homebrew removed).

**Steps**:
1. Run `/intellisweep`
2. Verify: No crash or error when brew commands fail
3. Verify: Output mentions "Homebrew not installed, skipping brew-related checks"
4. Verify: Other categories still scanned (caches, shell config, etc.)

**Pass criteria**: Graceful degradation, no crash, other checks still work.

## Scenario 4: Backup and restore round-trip

**Steps**:
1. Run `/intellisweep`
2. Select a moderate-risk item (e.g., an old SDK)
3. Confirm deletion
4. Verify: manifest.json exists at `~/.devclean-backup/YYYY-MM-DD/manifest.json`
5. Verify: manifest contains correct original path, size, risk level
6. Ask to restore the item
7. Verify: Item is restored to original location
8. Verify: `ls -la` shows correct permissions and structure
9. If the tool had a version command, verify it still works

**Pass criteria**: Round-trip preserves data integrity, permissions, and structure.

## Scenario 5: Partial cancellation

**Steps**:
1. Run `/intellisweep`
2. Select 5+ items for cleanup
3. After 2-3 items are cleaned, cancel (Ctrl+C or tell Claude to stop)
4. Verify: Items cleaned before cancel are gone
5. Verify: Items not yet cleaned are untouched
6. Verify: Backup exists for cleaned moderate/destructive items
7. Verify: Report shows partial progress ("3 of 5 items cleaned")

**Pass criteria**: Partial state is consistent, no data corruption.

## Scenario 6: Clean machine

**Setup**: A relatively fresh Mac with minimal dev tools.

**Steps**:
1. Run `/intellisweep`
2. Verify: Audit completes quickly (< 5 minutes)
3. Verify: Few or no findings
4. Verify: Skill says something like "Your machine is pretty clean" rather than
   presenting an empty triage report

**Pass criteria**: Handles the "nothing to do" case gracefully.

## Scenario 7: Dry-run

**Steps**:
1. Run `/intellisweep --dry-run`
2. Verify: Full triage report is presented
3. Verify: No cleanup is offered or performed
4. Verify: No files are modified, deleted, or backed up
5. Verify: Ends with "Run `/intellisweep` to proceed with cleanup"

**Pass criteria**: Zero side effects in audit-only mode.

## Edge cases to verify

- [ ] Path with spaces (e.g., `~/Library/Application Support/`)
- [ ] Very large directory (> 10GB) — timeout handling
- [ ] .zshrc with no secrets — security section is empty or says "no issues found"
- [ ] Multiple shell configs (.zshrc + .bashrc + .zprofile) — all scanned
- [ ] Second run on same day — backup dir gets `-2` suffix
- [ ] Backup prune with no old backups — handles gracefully
