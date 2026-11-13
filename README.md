# claude-session-tools

Small macOS scripts for managing Claude Code sessions from the VS Code extension. Python 3 standard library only.

## claude-empty-trash

The extension can only archive a session, which hides it and leaves the transcript on disk. This script deletes transcripts under `~/.claude/projects/`.

- **Interactive:** `claude-empty-trash` lists the sessions of the current directory, `--all` lists every project. Pick numbers or ranges, type `delete` to confirm. `--list` only prints.
- **Trash group:** `claude-empty-trash --purge-trash` deletes every session in a panel group named `Trash` and empties the group. It runs only while VS Code is closed, so open sessions are not written back. Add `--dry-run` to list what would go.

### Automatic purge

`launchd/com.example.claude-empty-trash.plist` runs `--purge-trash` whenever VS Code's state database changes (written on quit) and every 5 minutes as a fallback. Each deletion is logged to `~/Library/Logs/claude-empty-trash.log`.

```
mkdir -p ~/bin ~/Library/LaunchAgents
cp bin/claude-empty-trash ~/bin/
sed "s#__HOME__#$HOME#g" launchd/com.example.claude-empty-trash.plist > ~/Library/LaunchAgents/com.example.claude-empty-trash.plist
launchctl bootstrap gui/$(id -u) ~/Library/LaunchAgents/com.example.claude-empty-trash.plist
```

Before the first run, `claude-empty-trash --purge-trash --dry-run` shows what the Trash group holds.

## claude-session-export

Exports sessions as readable Markdown, optionally zipped. Sessions are matched by sidebar title (case-insensitive) or session id prefix.

```
claude-session-export "UX mocks review" "!251 review" --zip ~/Downloads/review.zip
claude-session-export 38ab1584 --out ~/Downloads
claude-session-export --list
```
