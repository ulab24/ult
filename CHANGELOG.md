# Changelog

## [Unreleased]

## [0.2.2] - 2026-11-10

Machines, workspaces and the new-session dialog are popups over the main view, adding a machine is a wizard, and the Preview pane shows the real screen.

### Added

- Adding a machine no longer means editing a file: in the machines view (`m`) press `a` and a popup asks for the ssh address, suggests a name, tests the connection the way ult connects, and writes the `machine` line to your config when it works. If the test fails it shows why and lets you try again, change the address or add the machine anyway. `e` still opens the config in vim.

### Changed

- The new-session dialog is larger and easier to use: every machine, kind and restart choice is shown and clickable, the directory and name are boxes, a line says what will be started, and a hint explains the focused field.
- Popups keep the terminal's own background, so your background picture shows through, but their lines, titles and text now use the bright colours (in both the dark and light themes) so they stay readable on it.
- `w` now opens workspace management as a large popup over the dimmed main view instead of a separate screen: workspaces on the left, the selected workspace's sessions on the right (stacked on a narrow terminal). The keys are the same (`enter`, `tab`, `n`, `a`, `d`, `r`, `D`, `x`, `esc`) and the mouse works in it.
- `m` opens machine management as the same kind of large popup: machines on the left (sessions, online state), the selected machine's target, state and sessions on the right. Keys are unchanged (`enter` lists its sessions, `a` add, `e` edit, `d` remove, `r` retry, `esc` back) and the mouse works in it.
- The Preview pane now shows the session's screen as it looks, not its output with the escape codes cut out. Words are spaced properly, redraws replace the old text instead of piling up, and the preview is laid out at the session's own size. It shows plain text; colours are not drawn.
- Scrolling down past the end of the scrollback viewer with the mouse wheel leaves it and puts you back in the session, so you no longer need `q` or `esc` after scrolling up to look. The notches of the same flick that reached the end are ignored, so it does not jump out early.
- `^B m` while attached turns mouse reporting off, so a plain drag selects text and copies as in any terminal, and turns it on again for the wheel and the bar. The bar says `mouse off` while it is off, and the choice stays across sessions until you flip it back or leave the session. It is listed in `^B ?`. (Holding Shift, or Option on macOS, while dragging still selects with the mouse on.)

### Fixed

- The scrollback viewer (mouse wheel, or the prefix then `[`) keeps the colours the program used. It used to strip them and draw everything in the theme's text colour, so a session looked plain white or black, most of all in the light theme. It now shows the same colours as the live session, like tmux's scroll mode. It also replays carriage returns, backspaces and erase-to-end-of-line, so a shell's prompt redraw (zsh's reverse `%` marker and its padding) no longer shows up as a stack of extra `%` lines.

## [0.2.1] - 2026-11-10

Claude Code in a session no longer breaks ult's keys or prints stray characters.

### Fixed

- While a program such as Claude Code had the extended keyboard mode on, `^B` and the keys after it did nothing, because your terminal then sends them in a different form. ult now understands both forms (the kitty keyboard protocol and xterm's modifyOtherKeys), so the prefix keys work, and keys that are not ult's still reach the program unchanged.
- Strings like `^[[?62;22;52c` and `^[P1+r5463...` no longer appear at the shell prompt. Programs ask the terminal what it can do and the terminal answers; ult used to ask again for every question in the replayed history when you attached, and typed the answers into whatever was in front. Questions in the replay are now left out, and an answer nobody asked for is dropped.
- Leaving a session now puts the keyboard modes back completely. A program that turned on the extended keyboard mode more than once used to leave your terminal in it, which broke the keys in the next session.
- Focus reports (`^[[I`) are passed on to a program only while it has asked for them, even when the request arrives split across two reads.

## [0.2.0] - 2026-11-10

ult now looks like lazygit next to nvim, is scriptable with Lua, and the Mac app will get the same setup.

### Added

- On a terminal of 100 columns by 24 rows or more the main screen is split into panes: Machines, Sessions and Workspaces on the left, a live Preview of the selected session and an Event log on the right. `1`, `2`, `3` and `0` focus a pane, `[` and `]` step inside it, and you can click panes. Smaller terminals keep the single list, and an attached session is never touched.
- The folder picker is a large popup that shows each folder's name first with its parent dimmed, marks folders that have a live session with `●`, shows the full path of the highlighted folder underneath, ranks folders by what you used most recently and filters as you type.
- `~/.config/ult/init.lua` configures ult (`ult.opt.theme = "light"`), binds keys in the session list, while attached and in the folder picker, shows popups, lists and input boxes, adds text to the status bar, changes colours and reacts to sessions starting, changing state, attaching and machines coming and going. A mistake in the file is reported with its file and line and the last good setup stays; nothing is half applied.
- Plugins are folders in `~/.config/ult/plugins/`. They can do nothing about your sessions or your computer until you allow it: a plugin lists the permissions it wants (`sessions.read`, `sessions.control`, `exec`) and you grant them in `init.lua`. A plugin that keeps failing is switched off for you.
- `ult plugins` lists plugins with their state and permissions, `ult lua check` tests your files, `ult lua eval` tries an expression, `ult doctor` has a Lua line, and `ult config show` now says where each setting comes from. The reference is `docs/LUA.md`, also on the ult page of ulab24.com under "Extending ult".
- `ult bridge`, the endpoint the native app connects to, loads your `init.lua` and plugins and sends what they describe (settings, key bindings, status text, colours, popups, lists, input boxes and messages) to the app, which shows them and sends back the key presses and answers. A plugin that works in the terminal works in the app; the details are in `docs/BRIDGE.md`. The Swift package for the Mac app (`UltKit`) can read and send these messages.

### Changed

- In the folder picker you now type to filter, so the letter keys `j`, `k`, `h`, `l` and `r` no longer move around there; use the arrow keys, `enter`, `backspace` and `esc`.
- The protocol version is now 1.1. Nothing changes for apps that do not ask for the new `ext` capability, and `ultd` is not involved.

### Security

- Lua runs only in the `ult` program you use, never in the background daemon, and is limited in time, memory and what it can touch (no files, no shell, no network).

## [0.1.11] - 2026-11-09

ulx sessions now report their own status.

### Added

- ulx sessions report their own status: a ulx running inside ult now shows working, blocked on a permission and finished, and `ult` resumes it with `ulx --resume <id>`. Needs ulx 0.4.0 or later; nothing to install.

## [0.1.10] - 2026-11-09

Agents can be taught to start and steer other sessions.

### Added

- `ult add skill claude` and `ult add skill ulx` install a short skill that teaches the agent it can start, watch and steer other sessions through ult (`ult remove skill` takes it out; `--dry-run`, `--force`, `--print` for other agents). `ult doctor` shows whether it is installed or outdated.

## [0.1.9] - 2026-11-09

Split panes in the TUI, tmux-style.

### Changed

- The ult mark in the title bar is now three squares that always show (▛) with the fourth blinking while a session is working, instead of one blinking square.

### Added

- Split panes: `^B %` and `^B "` split side by side or stacked, `o ; arrows q x z { }` move, swap, zoom and close panes, Ctrl/Alt-arrows resize, `( ) s L` switch the session in a pane. Only the active pane takes input; click any pane to make it active, drag a border to resize, the wheel opens a pane's scrollback. Everything works from the keyboard alone. `max_panes` (default 8, 1 to 16) limits a layout; each workspace's layout is remembered. The daemon is not involved: panes are composed by the client, one attach per pane.
- A session now lives in one terminal: attaching it from another terminal detaches the first, which is told why (`takeover` on `attach`, `taken-over` event; see docs/PROTOCOL.md). Read-only watchers are unaffected.

## [0.1.8] - 2026-11-09

The TUI works with the mouse.

### Added

- Mouse support in the TUI. Click a session to select it and double-click to attach; click a key hint (`n new`, `w workspaces`, `esc back` and so on) to press it; click the workspace name in the title bar to open the switcher and the machines to open theirs; in the switcher click a workspace or a session, double-click to switch or attach; the wheel scrolls the list, the switcher and the scrollback viewer. While attached, click a tab in the status bar to switch to it, or `new`, `list` and `detach` in its right-hand corner (the bar stays clickable even when the program uses the mouse itself). `mouse = off` turns all of it off; hold Shift (Option on macOS) to select text while it is on.
- Dialogs take the mouse. In the New session dialog click a field to focus it (a choice field such as Machine or Kind also steps to its next value) and click `[ Create ]` or `[ Cancel ]`; in the Kill dialog click `[ Kill ]` or `[ Keep running ]`; in the folder picker click a folder, double-click to open it; a click closes help.
- A new session starts in its workspace's directory: the dialog offers the directory of the workspace's newest session on the chosen machine (and, from inside a session, that session's directory), not the home directory. Choosing another machine follows along until you type in the field.

### Changed

- Dialogs take the terminal's own background in the 16-colour palette and with a transparent theme, so they blend with any background, light or dark, instead of a black or white box.

## [0.1.7] - 2026-11-09

The TUI shows one workspace at a time, and every session is in one.

### Changed

- Workspaces are mandatory. A session that names none is in `default`, which always exists; `ult ls --json` and the protocol always carry a `workspace`. `ult workspace <session> -` now means "back to default". Sessions from before this release are in `default`.
- The session list shows only the active workspace, named in the title bar: `ult ┃ api ┃ 3 sessions`. The app always starts in `default`. `w` is the workspace switcher: `enter` switches, `n` makes one (empty ones stay listed while you are in them), `a` moves a session in from another workspace, `d` sends one to `default`, `D` (twice) dissolves a workspace into `default`, `r` renames it.
- A new session starts in the workspace being shown. The tabs while attached follow the same rule, so `default` has tabs too.
- A session that is blocked in a workspace you are not looking at is flagged in the title bar (`◆ 1 in api`), and the next-session key switches to it.

## [0.1.6] - 2026-11-09

Leaving the scrollback viewer takes you back into the session.

### Fixed

- After the mouse wheel (or `^B [`) opened the scrollback viewer from an attached session, `Esc` left you in the session list and the session had to be reopened. It now goes back into the session.

## [0.1.5] - 2026-11-09

The mouse wheel works while attached, the session list shows the active workspace, and leaving ult leaves the terminal clean. `ult service restart` now picks up a new binary.

### Added

- The session list's title bar shows the workspace of the selected session after `ult`, like `ult ┃ au ┃ 2 sessions`.
- The mouse wheel works while attached in the TUI. It used to reach the shell as arrow keys (`^[[A`). Now the wheel opens the scrollback viewer, unless the program uses the mouse itself (nvim, `claude`), which then gets it. The terminal keeps plain clicks while attached: hold Shift (Option on macOS) to select text, or set `mouse = off` to leave the wheel to the terminal.

### Fixed

- `ult service restart` ran whatever binary the service file named, so after an upgrade `ult doctor` kept reporting the old daemon build. It now rewrites the service file when it points at a different ult, then starts that one.
- Leaving ult no longer leaves pieces of the TUI in your shell. A program that left its alternate screen while attached (a replayed editor, `claude`) dropped the terminal onto the main screen, and the TUI drew there. In the TUI the program's alternate-screen switches are now emulated, so the terminal never leaves it; `ult attach` takes a program that is still in its alternate screen out of it on detach. Detaching also turns off mouse reporting, bracketed paste and the other modes the program set, as tmux does.

## [0.1.4] - 2026-11-09

Editors such as nvim no longer overlap the status bar.

### Fixed

- Editors (nvim, vim) no longer draw under the status bar. A terminal that reports its size in-band (mode 2048) or answers the text-area size query told the program the full height; ult now answers for the reduced size instead.

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
