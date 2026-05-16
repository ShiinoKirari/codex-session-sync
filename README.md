# Codex Session Sync

`codex-session-sync` copies local Codex conversation records from one model provider name to another while preserving the original provider's data.

This is useful when you switch Codex between the official provider and another local provider configuration, and the conversation list appears split by provider.

## What It Does

- Reads Codex local state from `~/.codex/state_5.sqlite`.
- Copies matching conversation rows to a target provider name.
- Updates an existing target-provider copy when the source-provider conversation is newer.
- Copies replay JSONL files instead of editing the source files in place.
- Rewrites copied replay metadata so the copied conversations belong to the target provider.
- Copies dynamic tool metadata needed for old conversations to reopen cleanly.
- Skips internal guardian approval threads by default.
- Uses dry-run mode by default.
- Creates a backup before any `--apply` write.

## Usage

Show help and current local providers:

```sh
ruby codex-session-sync
```

Preview a sync from `openai` to `otherapi`:

```sh
ruby codex-session-sync --from openai --to otherapi
```

Apply the sync:

```sh
ruby codex-session-sync --from openai --to otherapi --apply
```

Use a custom Codex home:

```sh
ruby codex-session-sync --from openai --to otherapi --codex-home /path/to/.codex --apply
```

Include internal guardian approval threads:

```sh
ruby codex-session-sync --from openai --to otherapi --include-guardian --apply
```

## Safety Notes

The script is designed to copy or update target-provider conversations, not move them. Source provider rows and source replay files are left intact.

If a target-provider copy already exists, the script compares `updated_at_ms`. It updates the target copy only when the source conversation is newer; newer target conversations are not rolled back.

Before writing changes, the script creates a backup under:

```text
/private/tmp/codex-session-sync-backup-<timestamp>
```

Run without `--apply` first and inspect the planned clone count before making changes.

## Requirements

- Ruby
- `sqlite3` command-line tool
- Local Codex data under `~/.codex`
