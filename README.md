# ult

ult keeps your terminal sessions alive. It hosts coding agents (Claude Code, opencode, ulx, codex and others), shells, dev servers and editors as detachable sessions that survive closing the terminal, a lost ssh link and a power cut. One list shows them all, across your machines. It replaces tmux for this job. It ships as a single binary.

```
$ ult
```

opens the session list. `ult new --kind claude <dir>` starts a session, `ult attach <name>` joins it, `^Space d` leaves it running.

This repository carries only what you need to install ult: the install script, the release signing key, and the released binaries with their checksums. Development happens on ulab24's own infrastructure, so there is no source here.

## Install

```sh
curl -fsSL https://raw.githubusercontent.com/ulab24/ult/main/install.sh | bash
```

This installs the latest release for your machine (Linux or macOS, amd64 or arm64). It verifies the download (a GPG signature on the checksum list, then the sha256 of the archive) before installing anything, and puts `ult` in `/usr/local/bin` when run as root, otherwise in `~/.local/bin`.

To update, run the same command again: it replaces `ult` where it already lives and leaves your sessions and configuration alone.

Options go after `bash -s --`:

```sh
curl -fsSL https://raw.githubusercontent.com/ulab24/ult/main/install.sh | bash -s -- --version=v0.1.0
curl -fsSL https://raw.githubusercontent.com/ulab24/ult/main/install.sh | bash -s -- --dir=$HOME/bin
```

## First run

```sh
ult doctor            # check this machine
ult service install   # keep the daemon running at login (systemd user unit or launchd agent)
ult                   # the session list
```

Sessions are restored after a crash or power cut: they come back marked Interrupted, and `ult resume --all` restarts them, agents with their own resume command.

## Verify a download by hand

Each release carries `checksums.txt` and its signature `checksums.txt.asc`. The signing key is `ulab24-release-signing-key.asc` in this repository.

```sh
gpg --import ulab24-release-signing-key.asc
gpg --verify checksums.txt.asc checksums.txt
sha256sum -c --ignore-missing checksums.txt
```

## Feedback

Open an issue here. Tell us what broke, what was confusing, or what you wished it did.

## Licence

All rights reserved; free to download and run. See [LICENSE](LICENSE).
