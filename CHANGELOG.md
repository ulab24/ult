# Changelog

## [0.1.3] - 2026-11-09

The leader key is now `^B`, so detaching works on a Mac out of the box. Sessions can be grouped into workspaces that show as tabs.

### Added

- Workspaces: a named group of sessions, local and remote together. `ult new --workspace NAME`, `ult workspace <session> <name|->`, a `workspace` field on every session in `ult ls --json` and the protocol (`docs/PROTOCOL.md`). Sessions started from inside a workspace join it.
- A workspaces screen (`w` in the list): every workspace with its sessions, machines, state and last activity; create, add sessions, remove, rename, ungroup, attach and kill from it.
- Tabs: while attached, the status row lists the workspace's running sessions. `^B 1` to `^B 9`, `^B .` and `^B ,` switch between them.

### Changed

- The default leader key is `^B` instead of `^Space`: `^B d` detaches, `^B l` lists, `^B n` makes a new session. macOS uses `^Space` for its input-source shortcut, so detach never reached ult there. Press the leader twice to send `^B` to the program. Keep the old key with `prefix = ctrl-space` in the config; a `prefix` you already set is unchanged.

### Fixed

- `ult attach` ignored the `prefix` setting and always used the default; it now follows the config like the TUI does.

## [0.1.2] - 2026-11-09

Pick a folder instead of typing it: the new-session dialog gets a folder browser that also works for remote machines. Release notes are now structured, and each release publishes a feed for the website.

### Added

- Folder browser in the new-session dialog. Press `^O` on the Directory field to choose from the directories your sessions already use, or browse the machine's folders: `enter` opens a folder, `backspace` goes up, `space` uses the folder you are in, `.` shows hidden folders, `~` goes home. It works for remote machines too; they need ult v0.1.2 as well.
- A `dirs` request in the daemon protocol (`docs/PROTOCOL.md`), also forwarded by `ult bridge` for the GUI.
- Release notes list what changed by section, with install instructions, a download table, the signing key fingerprint and a full-changelog link.
- Each stable release publishes `releases.json` (and `CHANGELOG.md`) to the public repository, so the ulab24.com site can show every release's notes.

### Security

- `dirs` returns folder names only, never files. It accepts absolute or `~` paths, caps a listing at 1000 entries, and is refused for agent session tokens.

## [0.1.1] - 2026-11-09

### Added

- `transparent = on` in the config: the TUI draws no background of its own (base and status bars use the terminal's), so a transparent or blurred terminal shows through.

## [0.1.0] - 2026-11-08

First release. Everything built so far is in this one entry.

- Core: `ult daemon` on a private Unix socket with a single-instance lock; framed protocol with size and depth limits; crash-safe journal (fsynced checksummed records, atomic snapshots, clean-stop marker, scrollback ring with torn-tail repair); sessions on PTYs in their own process group; recovery after `kill -9` or power loss with restart policies `ask`, `auto`, `never`.
- CLI: `new`, `ls`, `attach`, `detach`, `kill`, `resume`, `skip`, `stop`, `version`, `config path|show`, `remote-info`, `mute|unmute`.
- TUI: session list across machines, detail panel, filter, new-session and kill dialogs, help, machines view, recovery view, read-only scrollback viewer with search, attach view with one status row (`^Space d|l|n|[|j`); warm dark and light themes, OSC 11 detection, 16/256/truecolor, `NO_COLOR`; motion with `ULT_MOTION=0` and an ASCII fallback.
- Agents: status (working, blocked, idle, finished) from hooks (Claude Code) or PTY activity; `ult hook`, `ult install-hooks|remove-hooks` (merge, backup, idempotent, `--dry-run`); resume per kind; desktop notifications (notify-send, osascript); `y`/`n` approve when the prompt is recognised.
- Remote machines over the system ssh (`machine NAME = user@host`): merged list, `machine:session` targets, `ult new --machine`, a dropped link never ends a session and recovers by itself.
- Agent-driven sessions: `ult mcp` (eight `ult_*` tools) and `ult spawn|status|read|send|approve|wait|close|revoke|policy`; ownership, depth 3, 8 live children, rate limits, human-attach pause, audit log, enforced in the daemon.
- Hardening: `ult service install|uninstall|status|restart` (systemd user unit, launchd agent), `ult doctor [--json]`, fuzz targets in the gate, soak test with goroutine and fd leak checks, independent review with fixes.
- GUI bridge: `ult bridge` serves all machines to the native GUIs on one private socket (`docs/BRIDGE.md`).
- Release pipeline: `make check` gate, `make build-all` for linux and darwin (amd64, arm64), `install.sh`, workflows `check`, `release` and `scan`.
- Docs: design, plan, user guide, configuration reference, the protocol as built, ADRs, GUI plan.
