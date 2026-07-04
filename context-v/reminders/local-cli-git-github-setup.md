# Local CLI, Git, and GitHub Setup

Date checked: 2026-07-04

## Environment

- OS: Ubuntu 20.04.6 LTS (Focal Fossa)
- Architecture: amd64
- Workspace: `/home/owner/code/roster-monorepo`

## Codex CLI

Codex CLI was already installed at:

```text
/usr/bin/codex
```

Installed version:

```text
codex-cli 0.142.5
```

The npm registry reported the same latest version:

```text
@openai/codex 0.142.5
```

No Codex CLI update was needed.

## Git

Git was already installed at:

```text
/usr/bin/git
```

Installed version:

```text
git version 2.50.1
```

The installed apt candidate also reported `2.50.1`, so there was no package-manager update available from the configured apt sources.

Global Git identity was configured as:

```text
user.name = Mathieu Doucet
user.email = mjdoucet8@gmail.com
init.defaultBranch = main
```

No global credential helper was configured before GitHub CLI authentication.

Note: upstream Git tags showed newer versions than `2.50.1`, but system-wide Git updates were not performed because `sudo` is not usable on this machine:

```text
sudo: /usr/bin/sudo must be owned by uid 0 and have the setuid bit set
```

## GitHub CLI

GitHub CLI (`gh`) was not installed initially.

Installed GitHub CLI version:

```text
gh version 2.96.0 (2026-07-02)
```

Installed location:

```text
/home/owner/.local/bin/gh
```

Install method:

- Downloaded the official GitHub CLI Linux amd64 tarball:
  `https://github.com/cli/cli/releases/download/v2.96.0/gh_2.96.0_linux_amd64.tar.gz`
- Verified the tarball checksum:

```text
83d5c2ccad5498f58bf6368acb1ab32588cf43ab3a4b1c301bf36328b1c8bd60
```

- Extracted and installed the `gh` binary into `/home/owner/.local/bin/gh`.

## Shell PATH Configuration

`/home/owner/.profile` already contained logic to add `$HOME/.local/bin` to `PATH` for login shells when that directory exists.

Added equivalent logic to `/home/owner/.bashrc` so interactive Bash terminals can also find user-local installs:

```bash
# Add user-local command installs.
if [ -d "$HOME/.local/bin" ] ; then
    PATH="$HOME/.local/bin:$PATH"
fi
```

After this change, a login Bash shell resolved `gh` correctly:

```text
/home/owner/.local/bin/gh
```

## GitHub Authentication

GitHub CLI authentication was started with:

```bash
/home/owner/.local/bin/gh auth login --hostname github.com --git-protocol https --web
```

The device login flow produced:

```text
https://github.com/login/device
```

At the time this note was written, the login session was still waiting for browser authorization. After authorization, verify with:

```bash
gh auth status
```

If authenticated successfully, GitHub CLI should also configure Git to use the GitHub credentials because the login flow was started with Git integration enabled.

Follow-up on 2026-07-04:

Codex runs with `HOME=/home/owner` and resolves `gh` to:

```text
/home/owner/.local/bin/gh
```

For Codex to reuse GitHub CLI authentication across sessions, `gh` needs a persistent config under:

```text
/home/owner/.config/gh/hosts.yml
```

The most reliable setup for Codex is to authenticate with plaintext `gh` storage rather than a system keychain, because Codex may not have access to desktop credential stores:

```bash
unset GH_CONFIG_DIR
mkdir -p "$HOME/.config/gh"
/home/owner/.local/bin/gh auth login --hostname github.com --git-protocol https --web --insecure-storage
```

When prompted with `Authenticate Git with your GitHub credentials?`, answer `Y`. After approving the browser/device flow, return to the same terminal and let the command fully exit. Then verify:

```bash
/home/owner/.local/bin/gh auth status
ls -la "$HOME/.config/gh"
```

On verification from Codex, `/home/owner/.config/gh/hosts.yml` existed and contained an entry for `github.com` user `mjdoucet8`, but `gh auth status` reported the stored token was invalid. Re-authenticate from a normal terminal if this recurs:

```bash
gh auth logout -h github.com -u mjdoucet8
gh auth login --hostname github.com --git-protocol https --web --insecure-storage
```

Codex sandbox limitation: direct GitHub network calls from Codex may fail or require explicit escalation even when the local `gh` token file exists. If a `gh` command needs GitHub API/network access, expect to run the command manually in a normal terminal and paste back the result, unless an escalated Codex command is approved for that specific operation.
