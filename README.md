# Developing A-Novel

Welcome! This guide takes you from a fresh machine to a working a-novel
development environment. By the end you will have the complete toolchain
installed, the [`a-novel` CLI](https://github.com/a-novel-kit/stack) built and
configured, and local checkouts of every repository you need.

A-Novel spans two GitHub organizations: [`a-novel`](https://github.com/a-novel)
hosts [Agora](https://github.com/a-novel), the online writing studio itself,
and [`a-novel-kit`](https://github.com/a-novel-kit) hosts the shared libraries,
GitHub Actions and tooling behind it. One setup covers both.

**Contents**

- [Before you begin](#before-you-begin)
  - [Supported platforms](#supported-platforms)
  - [A note for macOS users](#a-note-for-macos-users)
  - [A note for Windows users](#a-note-for-windows-users)
- [Step 1 — install the toolchain](#step-1--install-the-toolchain)
- [Step 2 — install the a-novel CLI](#step-2--install-the-a-novel-cli)
- [Step 3 — daily usage](#step-3--daily-usage)
- [Getting help](#getting-help)

## Before you begin

### Supported platforms

Development is supported on **macOS and Linux** — anything that runs a
bash-compatible shell. Install commands below are given for macOS, Ubuntu and
Arch Linux; other distributions work fine too, as long as you can source the
same tools. Every tool section links its official install documentation in
case your setup needs a different path.

The example outputs in this guide were captured on a real Linux setup. Your
version numbers will differ — that is expected; what matters is that the
command answers at all, and that the version meets the stated floor when one
exists.

### A note for macOS users

We recommend installing the toolchain through [Homebrew](https://brew.sh),
and the macOS commands in this guide are written for it. The reason is
maintenance: everything installed through Homebrew updates with a single
`brew upgrade`, instead of each tool shipping its own installer and update
mechanism. Each section still links the official install documentation,
which may recommend a different method — you are free to follow it instead;
the rest of the guide works the same either way.

If you don't have Homebrew yet, install it first:

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

### A note for Windows users

Windows is not supported natively — the tooling assumes a bash-compatible
environment. Other solutions exist, but the one we recommend is
[WSL2](https://learn.microsoft.com/en-us/windows/wsl/install): install a
Ubuntu distribution, then follow the Ubuntu steps throughout this guide.
This path is well-trodden — the guide itself is maintained from a WSL2
environment.

## Step 1 — install the toolchain

Work through the subsections in order — later steps assume the earlier ones
(for example, `gh` is the easiest way to set up your GitHub SSH key). Each
subsection ends with a **Verify** block; the comment under each command shows
the kind of output you should expect.

### zsh

Any bash-compatible shell works, but we recommend
[zsh](https://www.zsh.org) on every platform — it is what the team uses, and
the `a-novel` CLI's shell integration (daemon auto-start, tab completion)
supports zsh, bash and fish. See the
[per-OS install instructions](https://github.com/ohmyzsh/ohmyzsh/wiki/Installing-ZSH)
for other systems.

```bash
# macOS — zsh is already the default shell; nothing to do

# Ubuntu
sudo apt install zsh
chsh -s $(which zsh)

# Arch Linux
sudo pacman -S zsh
chsh -s $(which zsh)
```

`chsh` makes zsh your default shell; log out and back in for it to take
effect.

Verify:

```bash
echo $SHELL
# /usr/bin/zsh
```

### Git

Everything is versioned with [Git](https://git-scm.com/downloads), and all
repositories are cloned over SSH.

```bash
# macOS
brew install git

# Ubuntu
sudo apt install git

# Arch Linux
sudo pacman -S git
```

Verify:

```bash
git --version
# git version 2.54.0
```

### GitHub CLI

The [GitHub CLI](https://github.com/cli/cli#installation) (`gh`) drives pull
requests and API calls from the terminal, and doubles as the easiest way to
authenticate your machine with GitHub.

```bash
# macOS
brew install gh

# Ubuntu — the apt package works; the official apt repository ships fresher
# releases if you ever need one:
# https://github.com/cli/cli/blob/trunk/docs/install_linux.md
sudo apt install gh

# Arch Linux
sudo pacman -S github-cli
```

Then authenticate. Pick **GitHub.com**, then **SSH** as the protocol — `gh`
offers to generate an SSH key and upload it to your account for you, which
covers the SSH requirement in one go:

```bash
gh auth login
```

Verify both the CLI and your SSH access:

```bash
gh --version
# gh version 2.93.0 (2026-05-28)

gh auth status
# github.com: ✓ Logged in to github.com account <you> (keyring)

ssh -T git@github.com
# Hi <you>! You've successfully authenticated, but GitHub does not provide shell access.
```

> [!NOTE]
> `ssh -T git@github.com` exits with code 1 even on success — GitHub refuses
> the shell after greeting you. The greeting line is what matters.

### Go

The services, the shared libraries and the `a-novel` CLI itself are written in
[Go](https://go.dev/doc/install). The minimum version is whatever
[`cli/go.mod`](https://github.com/a-novel-kit/stack/blob/HEAD/cli/go.mod)
declares in the stack repo — any release at or above it works.

```bash
# macOS
brew install go

# Ubuntu — use snap; the apt package lags far behind
sudo snap install go --classic

# Arch Linux
sudo pacman -S go
```

Verify:

```bash
go version
# go version go1.26.4 linux/amd64
```

One more thing while you are here: `go install` places binaries in
`$(go env GOPATH)/bin` — `~/go/bin` by default; set `GOBIN`
(`go env -w GOBIN=<dir>`) if you want them elsewhere. That directory must be
on your `PATH`, or the `a-novel` CLI you install in step 2 won't be found:

```bash
echo 'export PATH="$PATH:$(go env GOPATH)/bin"' >> ~/.zshrc
exec $SHELL
```

(The example targets zsh — if you use another shell, the same line goes to
its rc file instead, e.g. `~/.bashrc`.)

### Node.js

JavaScript tooling (linters, formatters, the published client packages) runs
on [Node.js](https://nodejs.org/en/download). We track the **Current** release
line, not LTS — the exact floor is the `engines.node` field of the
[stack `package.json`](https://github.com/a-novel-kit/stack/blob/HEAD/package.json).

```bash
# macOS
brew install node

# Ubuntu — use nvm; the apt package is far too old. Check the nvm README for
# the current installer version: https://github.com/nvm-sh/nvm#installing-and-updating
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.5/install.sh | bash
\. "$HOME/.nvm/nvm.sh"   # load nvm into this shell — new shells load it automatically
nvm install node         # "node" is nvm's alias for the latest version

# Arch Linux
sudo pacman -S nodejs npm
```

Verify:

```bash
node --version
# v24.11.1
```

### pnpm

[pnpm](https://pnpm.io/installation) is the package manager for every
JavaScript workspace in both orgs — plain `npm` or `yarn` will be rejected by
the repos' `preinstall` hooks.

```bash
# macOS — note: pnpm's standalone install script does not support Intel macs,
# which is one more reason Homebrew is the path here
brew install pnpm

# Ubuntu
curl -fsSL https://get.pnpm.io/install.sh | sh -

# Arch Linux
sudo pacman -S pnpm
```

Verify:

```bash
pnpm --version
# 11.5.2
```

### Podman and podman-compose

Local infrastructure — databases, test environments, service containers —
runs on [Podman](https://podman.io/docs/installation), with
[podman-compose](https://github.com/containers/podman-compose#installation)
as the provider behind `podman compose`. Docker is not used.

```bash
# macOS — Podman runs inside a lightweight VM: the two `podman machine`
# commands below create it once, then start it after each reboot.
brew install podman podman-compose
podman machine init
podman machine start

# Ubuntu
sudo apt install podman podman-compose

# Arch Linux
sudo pacman -S podman podman-compose
```

Verify that the Podman service answers and that the compose provider is
found:

```bash
podman info --format '{{.Version.Version}}'
# 5.8.2
#   (plain `podman info` prints a long status report — any error here means
#    the service, or the VM on macOS, is not running)

podman compose version
# >>>> Executing external compose provider "podman-compose". Please see podman-compose(1) for how to disable this message. <<<<
# podman version 5.8.2
```

The "external compose provider" banner is the success signal: it means
`podman compose` found `podman-compose`.

### Keeping the toolchain up to date

Most of the toolchain updates through your package manager; only the pieces
installed outside it need their own command:

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
releasing every project. It installs like any other Go tool, straight from
source — no need to clone anything first:

```bash
go install github.com/a-novel-kit/stack/cli/cmd/a-novel@latest
```

The binary lands in the Go install directory you added to your `PATH` in
step 1 — `$(go env GOPATH)/bin` by default, or `$GOBIN` if you changed it.

### Bootstrap your environment

```bash
a-novel core setup
```

`core setup` is the one-time interactive bootstrap, and it takes care of
everything: it re-checks the environment you installed in step 1 (podman,
git, GitHub SSH), creates the CLI's state directories, clones your workspace
(see below), installs a small block in your shell rc (zsh, bash and fish are
supported) that auto-starts the background daemon and enables tab
completion, and finally starts the daemon. It is also **idempotent** — you
can re-run it any time as a health check; on an already-configured machine
it changes nothing and simply reports green checks.

The workspace is a clone of the
[stack repository](https://github.com/a-novel-kit/stack); it hosts the CLI's
source and anchors your local checkouts of all org repositories. By default
it lives at `~/git-projects/a-novel`. To put it somewhere else, set
`A_NOVEL_STACKS` before running setup (format: `name:path` entries separated
by commas, first entry is the default workspace) — and keep it set, for
example in your shell rc:

```bash
export A_NOVEL_STACKS="default:$HOME/somewhere/else/a-novel"
```

Finally, pull the org repositories into the workspace:

```bash
a-novel core sync
```

`core sync` clones (or fast-forwards) the curated set of org repositories
into `app/` and `kit/` inside the workspace.

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

If the daemon reports `not running`, start it with `a-novel core start` —
new shells do this automatically thanks to the rc block installed by setup.

### Updating the CLI

After pulling a workspace update that touches `cli/`, rebuild in place —
`a-novel install` rebuilds the binary from your workspace's `cli/` directory
and restarts the daemon without losing your running targets:

```bash
git -C ~/git-projects/a-novel pull   # adjust if you moved your workspace
a-novel install
```

### Optional — bot credentials

Maintainers who run AI-agent workflows also need the per-org GitHub App keys
under `.secrets/`. This is not required for regular development; when you
need it, follow the
[GitHub access section of the stack README](https://github.com/a-novel-kit/stack#github-access).

## Step 3 — daily usage

A taste of the everyday commands, all run from anywhere inside the stack:

```bash
a-novel test -y                          # run all Go + pnpm tests in the working tree
a-novel build -y                         # build binaries, bundles and images
a-novel run start <service>/<target>     # start a service target (deps auto-cascade)
a-novel run logs <service>/<target> --follow
a-novel run ui                           # full-screen TUI
a-novel <verb> --help                    # exhaustive help for any subcommand
```

Lint, format and code generation are deliberately **not** CLI verbs — they
are uniform pnpm scripts in every repo: `pnpm lint:go`, `pnpm format:go`,
`pnpm generate:go`, and so on.

For the full picture — command tree, daemon architecture, volumes, release
flow — head to the [stack README](https://github.com/a-novel-kit/stack#readme)
and the
[CLI reference](https://github.com/a-novel-kit/stack/blob/HEAD/cli/README.md).

## Getting help

- Questions and discussion: join us on
  [Discord](https://discord.gg/rp4Qr8cA).
- Bugs and feature requests: open an issue on the affected repository (the
  templates will guide you).
- Security reports: **never** a public issue — follow the
  [security policy](https://github.com/a-novel-kit/.github/blob/HEAD/SECURITY.md).
