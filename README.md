# Developing A-Novel

This guide takes a fresh machine to a working a-novel setup: the toolchain, the
[`a-novel` CLI](https://github.com/a-novel-kit/stack) built and configured, and
local checkouts of every repository.

A-Novel spans two GitHub organizations —
[`a-novel`](https://github.com/a-novel) (the Agora writing studio) and
[`a-novel-kit`](https://github.com/a-novel-kit) (shared libraries, Actions and
tooling). One setup covers both.

**Contents**

- [Before you begin](#before-you-begin)
  - [Supported platforms](#supported-platforms)
  - [A note for macOS users](#a-note-for-macos-users)
  - [A note for Arch Linux users](#a-note-for-arch-linux-users)
  - [A note for Windows users](#a-note-for-windows-users)
- [Step 1 — install the toolchain](#step-1--install-the-toolchain)
- [Step 2 — install the a-novel CLI](#step-2--install-the-a-novel-cli)
- [Step 3 — daily usage](#step-3--daily-usage)
- [Getting help](#getting-help)

## Before you begin

### Supported platforms

**macOS and Linux** — anything with a bash-compatible shell. Commands are given
for macOS, Ubuntu and Arch Linux; other distros work if you can source the same
tools. Example version numbers will differ — what matters is that the command
answers and clears any stated floor.

### A note for macOS users

Install through [Homebrew](https://brew.sh) so the whole toolchain updates with
one `brew upgrade`. If you don't have it yet:

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

### A note for Arch Linux users

A base Arch install omits a few packages later steps assume:

```bash
sudo pacman -S which inetutils openssh
```

### A note for Windows users

Not supported natively. Use
[WSL2](https://learn.microsoft.com/en-us/windows/wsl/install) with an Ubuntu
distribution and follow the Ubuntu steps throughout.

## Step 1 — install the toolchain

Work through these in order — later steps use earlier ones. Each ends with a
**Verify** block; the comment shows the expected output.

### zsh

Recommended on every platform: the `a-novel` CLI's shell integration (daemon
auto-start, tab completion) supports zsh, bash and fish.
[Other systems](https://github.com/ohmyzsh/ohmyzsh/wiki/Installing-ZSH).

```bash
# macOS — zsh is already the default shell; nothing to do

# Ubuntu
sudo apt install zsh
chsh -s $(which zsh)

# Arch Linux
sudo pacman -S zsh zsh-completions
chsh -s /usr/bin/zsh
```

`chsh` sets your default shell; log out and back in for it to take effect.

```bash
echo $SHELL
# /usr/bin/zsh
```

### Git

Everything is versioned with [Git](https://git-scm.com/downloads) and cloned
over SSH.

```bash
# macOS
brew install git

# Ubuntu
sudo apt install git

# Arch Linux
sudo pacman -S git
```

```bash
git --version
# git version 2.54.0
```

Set your commit identity — use an email tied to your GitHub account so commits
link back to it (the `…@users.noreply.github.com` address from your email
settings works):

```bash
git config --global user.name "Your Name"
git config --global user.email "you@users.noreply.github.com"
```

### Oh My Zsh (recommended)

An [Oh My Zsh](https://ohmyz.sh) config framework: better zsh defaults and
completion.

```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

Run it **now** — it rewrites `~/.zshrc` (your old file is backed up to
`~/.zshrc.pre-oh-my-zsh`), so installing it after later `PATH` edits would drop
them.

### GitHub CLI

[`gh`](https://github.com/cli/cli#installation) drives pull requests and API
calls, and authenticates your machine with GitHub.

```bash
# macOS
brew install gh

# Ubuntu — apt works; the official apt repo ships fresher releases if needed:
# https://github.com/cli/cli/blob/trunk/docs/install_linux.md
sudo apt install gh

# Arch Linux
sudo pacman -S github-cli
```

Authenticate — pick **GitHub.com**, then **SSH**, and **Skip** the key offer
(the next section creates one):

```bash
gh auth login
```

```bash
gh --version
# gh version 2.93.0 (2026-05-28)

gh auth status
# github.com: ✓ Logged in to github.com account <you> (keyring)
```

### SSH key

Repos clone over SSH. One Ed25519 key covers both Git auth and commit signing
(next section) — accept the default path, passphrase optional:

```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
```

Register it as an **authentication** key. `gh auth login` lacks the scope, so
grant it only for this command:

```bash
gh auth refresh -h github.com -s admin:public_key
gh ssh-key add ~/.ssh/id_ed25519.pub --type authentication --title "$(hostname)"
gh auth refresh -h github.com --remove-scopes admin:public_key
```

Prefer the web UI? Print the key and paste it at
[github.com/settings/ssh/new](https://github.com/settings/ssh/new):

```bash
cat ~/.ssh/id_ed25519.pub
```

```bash
ssh -T git@github.com
# Hi <you>! You've successfully authenticated, but GitHub does not provide shell access.
```

> [!NOTE]
> `ssh -T git@github.com` exits `1` even on success — the greeting line is what
> matters.

### Signing your commits

Commits to A-Novel repos must be signed — no GPG. Point Git at the same key:

```bash
git config --global gpg.format ssh
git config --global user.signingkey ~/.ssh/id_ed25519.pub
git config --global commit.gpgsign true
```

Then register that **same** key again, now as a **signing** key:

```bash
gh auth refresh -h github.com -s admin:ssh_signing_key
gh ssh-key add ~/.ssh/id_ed25519.pub --type signing --title "$(hostname) (signing)"
gh auth refresh -h github.com --remove-scopes admin:ssh_signing_key
```

New commits now show as **Verified** on GitHub.

### Go

Services, libraries and the CLI are written in [Go](https://go.dev/doc/install).
Minimum version:
[`cli/go.mod`](https://github.com/a-novel-kit/stack/blob/HEAD/cli/go.mod).

```bash
# macOS
brew install go

# Ubuntu — use snap; the apt package lags far behind
sudo snap install go --classic

# Arch Linux
sudo pacman -S go
```

```bash
go version
# go version go1.26.4 linux/amd64
```

`go install` puts binaries in `$(go env GOPATH)/bin` (`~/go/bin` by default).
That directory must be on your `PATH`, or step 2's CLI won't be found:

```bash
echo 'export PATH="$PATH:$(go env GOPATH)/bin"' >> ~/.zshrc
exec "$SHELL"
```

### Node.js

JavaScript tooling (linters, formatters, client packages) runs on
[Node.js](https://nodejs.org/en/download). We track **Current**; the floor is
`engines.node` in the
[stack `package.json`](https://github.com/a-novel-kit/stack/blob/HEAD/package.json).

```bash
# macOS
brew install node

# Ubuntu — use nvm; the apt package is far too old. Current installer version:
# https://github.com/nvm-sh/nvm#installing-and-updating
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.5/install.sh | bash
\. "$HOME/.nvm/nvm.sh"   # load nvm into this shell — new shells load it automatically
nvm install node         # "node" is nvm's alias for the latest version

# Arch Linux
sudo pacman -S nodejs npm
```

```bash
node --version
# v24.11.1
```

### pnpm

The package manager for every JS workspace — `npm` and `yarn` are rejected by
the repos' `preinstall` hooks.

```bash
# macOS — the standalone script doesn't support Intel macs; another reason to use brew
brew install pnpm

# Ubuntu
curl -fsSL https://get.pnpm.io/install.sh | sh -

# Arch Linux
sudo pacman -S pnpm
```

```bash
pnpm --version
# 11.5.2
```

### GitHub Packages access

The `@a-novel` and `@a-novel-kit` npm scopes live on
[GitHub Packages](https://docs.github.com/en/packages), so `pnpm install` needs
a [classic token](https://github.com/settings/tokens) with `read:packages`. Put
it in your **home** `.npmrc` — never a repo's, since those are committed:

```bash
# ~/.npmrc
//npm.pkg.github.com/:_authToken=ghp_your_token_here
```

### Podman and podman-compose

Local infrastructure runs on
[Podman](https://podman.io/docs/installation) (rootless, no daemon) with
[podman-compose](https://github.com/containers/podman-compose#installation)
**1.6.0+** behind `podman compose`. No Docker needed.

```bash
# macOS — Podman runs in a lightweight VM: init once, start after each reboot
brew install podman podman-compose
podman machine init
podman machine start

# Ubuntu
sudo apt install podman podman-compose

# Arch Linux — pacman prompts for an OCI runtime; choose `crun`
sudo pacman -S podman podman-compose
```

```bash
podman info --format '{{.Version.Version}}'
# 5.8.2   (an error here means the service — or the macOS VM — isn't running)

podman compose version
# >>>> Executing external compose provider "podman-compose". ... <<<<
# podman version 5.8.2
```

The "external compose provider" banner is the success signal: `podman compose`
found `podman-compose`. Versions below 1.6.0 mishandle
`depends_on: service_healthy` and can hang at start-up.

### AI coding agents (optional — heavily recommended)

Every repo ships agent-agnostic skills under `.agents/skills/`, so any agent
arrives pre-configured — Codex reads that directory directly, Claude Code
through a committed `.claude/skills` symlink. Optional, but a lot of leverage.
The team uses two:

#### Claude Code (Anthropic)

[Claude Code](https://code.claude.com/docs/en/overview) is Anthropic's agent.
Easiest is the
[IDE extension](https://code.claude.com/docs/en/ide-integrations) — VS Code (and
forks) plus a JetBrains plugin; search **Claude Code** and sign in. Or install
the CLI:

```bash
curl -fsSL https://claude.ai/install.sh | bash
```

It lands in `~/.local/bin`; if `claude` isn't found, add it to `PATH`:

```bash
echo 'export PATH="$PATH:$HOME/.local/bin"' >> ~/.zshrc
exec "$SHELL"
```

The CLI auto-updates in the background (`claude update` forces it). Either way,
Claude Code needs a paid Claude account (Pro, Max, Team or Enterprise) or a
Claude Console account.

```bash
claude --version
# 2.1.173 (Claude Code)
```

A few global settings sharpen it (set once in `~/.claude/settings.json`):

- **Model** — `/model`, pick the latest and most capable (unlocks `xhigh`).
- **Output style** — `/config` → **Output style** → **Explanatory**.
- **Effort** — `/effort xhigh` as your default; `/effort max` for the hard tasks.

#### Codex (OpenAI)

[Codex](https://developers.openai.com/codex) is OpenAI's agent. Easiest is the
[IDE extension](https://developers.openai.com/codex/ide) — VS Code, Cursor or
Windsurf (search **Codex**, id `openai.chatgpt`), with native JetBrains and
Xcode integrations; sign in on first open. Or install the CLI:

```bash
curl -fsSL https://chatgpt.com/codex/install.sh | sh
```

It adds `codex` to your `PATH`; re-run the installer to update, or on macOS use
the `codex` Homebrew cask (`brew install --cask codex`). Codex needs a paid
ChatGPT plan (Plus, Pro, Team, Edu or Enterprise) or an OpenAI API key — sign in
on first launch.

```bash
codex --version
# 0.145.0
```

### Keeping the toolchain up to date

Only the pieces installed outside your package manager need their own command:

```bash
# macOS — everything came from Homebrew
brew upgrade

# Ubuntu
sudo apt update && sudo apt upgrade   # git, gh, podman, podman-compose
sudo snap refresh                     # go
nvm install node                      # node (tracks the moving Current line)
pnpm self-update                      # pnpm (standalone installs only)

# Arch Linux — everything came from pacman
sudo pacman -Syu
```

## Step 2 — install the a-novel CLI

The `a-novel` CLI is the single entrypoint for building, testing, running and
releasing every project. It installs like any Go tool, straight from source:

```bash
go install github.com/a-novel-kit/stack/cli/cmd/a-novel@latest
```

It lands in the Go bin directory you added to `PATH` in step 1.

### Bootstrap your environment

```bash
a-novel core setup
```

`core setup` is the one-time interactive bootstrap: it re-checks your
environment, creates the CLI's state directories, clones your workspace,
installs a shell-rc block (daemon auto-start + completion) and starts the
daemon. It is idempotent — re-run it any time as a health check.

The workspace (a [stack](https://github.com/a-novel-kit/stack) clone) anchors
all your checkouts and defaults to `~/git-projects/a-novel`. To relocate it, set
`A_NOVEL_STACKS` before setup (format: `name:path` entries, comma-separated,
first is the default) and keep it set in your rc:

```bash
export A_NOVEL_STACKS="default:$HOME/somewhere/else/a-novel"
```

Then pull the org repositories into `app/` and `kit/`:

```bash
a-novel core sync
```

### Verify the installation

```bash
a-novel --version
# a-novel version d2f55a3

a-novel core status
# a-novel daemon: running
#   version: d2f55a3
#   socket : /run/user/1000/a-novel.sock
#   started: 2026-06-11T21:44:18Z (2s ago)
#   stacks : 1
#     * default    /home/<you>/git-projects/a-novel
```

If the daemon reports `not running`, start it with `a-novel core start` — new
shells do this automatically.

### Updating the CLI

After pulling a workspace change that touches `cli/`, rebuild in place — this
keeps your running targets:

```bash
git -C ~/git-projects/a-novel pull   # adjust if you moved your workspace
a-novel install
```

### Optional — bot credentials

Maintainers running AI-agent workflows need the per-org GitHub App keys under
`.secrets/`; see the
[GitHub access section of the stack README](https://github.com/a-novel-kit/stack#github-access).
Not needed for regular development.

### Optional — service secrets

Some services need secret values to run. The CLI's local secret manager stores
them AES-256-GCM-encrypted and injects them only into the child process of
`test` / `run` / `ui` — never printed, logged or committed.

```bash
a-novel secrets init   # generate the local key + store (run once)
```

Each service lists the secrets it needs — and where to get them — in its own
`CONTRIBUTING.md`; a secret you haven't set is skipped with a warning. The
[CLI README](https://github.com/a-novel-kit/stack/blob/HEAD/cli/README.md)
documents the full model.

## Step 3 — daily usage

The everyday commands, run from anywhere inside the stack:

```bash
a-novel test -y                          # run all Go + pnpm tests in the working tree
a-novel build -y                         # build binaries, bundles and images
a-novel run start <service>/<target>     # start a service target (deps auto-cascade)
a-novel run logs <service>/<target> --follow
a-novel run ui                           # full-screen TUI
a-novel <verb> --help                    # exhaustive help for any subcommand
```

Lint, format and code generation are **not** CLI verbs — they are uniform pnpm
scripts in every repo: `pnpm lint:go`, `pnpm format:go`, `pnpm generate:go`, and
so on.

For the full picture — command tree, daemon architecture, volumes, release flow
— see the [stack README](https://github.com/a-novel-kit/stack#readme) and the
[CLI reference](https://github.com/a-novel-kit/stack/blob/HEAD/cli/README.md).

## Getting help

- Questions and discussion: join us on [Discord](https://discord.gg/rp4Qr8cA).
- Bugs and feature requests: open an issue on the affected repository.
- Security reports: **never** a public issue — follow the
  [security policy](https://github.com/a-novel-kit/.github/blob/HEAD/SECURITY.md).
