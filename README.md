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
  - [A note for Arch Linux users](#a-note-for-arch-linux-users)
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

### A note for Arch Linux users

A base Arch install leaves out a few packages that the rest of this guide — and
many tool installers — assume are already there. Install them up front so a
later step doesn't fail on a missing command:

```bash
sudo pacman -S which inetutils openssh
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
(for example, the SSH-key step uses the `gh` you install just before it). Each
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
sudo pacman -S zsh zsh-completions
chsh -s /usr/bin/zsh
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

Set your commit identity — Git stamps it on every commit as the author, and
GitHub matches the email to your account:

```bash
git config --global user.name "Your Name"
git config --global user.email "you@users.noreply.github.com"
```

Use an email tied to your GitHub account so your commits link back to your
profile. GitHub's `…@users.noreply.github.com` address — found under your
account's email settings — works if you would rather not publish a real one.

### Oh My Zsh (recommended)

[Oh My Zsh](https://ohmyz.sh) is a configuration framework for zsh: sane
defaults, themes, plugins, and far better completion out of the box. It is
optional, but it makes day-to-day shell work markedly nicer, and it is what
the team runs.

```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

The installer replaces your `~/.zshrc` with its own template (your previous
file is backed up to `~/.zshrc.pre-oh-my-zsh`). Run it **now**, before later
steps append `PATH` lines to `~/.zshrc` — installing it afterwards would shed
those edits. It may also offer to set zsh as your login shell; you already did
that in the zsh step, so accepting is harmless and declining is fine.

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

Then authenticate. Pick **GitHub.com**, then **SSH** as the protocol. When
`gh` offers to generate or upload an SSH key, choose **Skip** — the next
section creates one explicitly and reuses it for commit signing:

```bash
gh auth login
```

Verify:

```bash
gh --version
# gh version 2.93.0 (2026-05-28)

gh auth status
# github.com: ✓ Logged in to github.com account <you> (keyring)
```

### SSH key

Every repository is cloned over SSH, so GitHub needs one of your public keys.
You create a single key here and use it for two things: authenticating Git
operations, and signing your commits (next section).

Generate an Ed25519 key, accepting the default location at each prompt. A
passphrase protects the key if the machine is lost or the private key is
exfiltrated; leaving it empty (press Enter) is reasonable on an encrypted
personal machine and saves a prompt on every signed commit — choose by your
threat model.
This writes the private key to `~/.ssh/id_ed25519` and the public key to
`~/.ssh/id_ed25519.pub` (reference:
[Generating a new SSH key](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)):

```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
```

Register the public key with GitHub as an **authentication** key — the next
section adds the same key again as a _signing_ key, since GitHub tracks the two
separately:

```bash
gh ssh-key add ~/.ssh/id_ed25519.pub --type authentication --title "$(hostname)"
```

Prefer the web UI? Print the public key and paste it at
[github.com/settings/ssh/new](https://github.com/settings/ssh/new) (reference:
[Adding a new SSH key to your account](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account)):

```bash
cat ~/.ssh/id_ed25519.pub
```

Verify your SSH access:

```bash
ssh -T git@github.com
# Hi <you>! You've successfully authenticated, but GitHub does not provide shell access.
```

> [!NOTE]
> `ssh -T git@github.com` exits with code 1 even on success — GitHub refuses
> the shell after greeting you. The greeting line is what matters.

### Signing your commits

Commits to A-Novel repositories must be signed — no GPG needed. Reuse the SSH
key you created in the previous section: point Git at it and turn signing on
(reference:
[Signing commits](https://docs.github.com/en/authentication/managing-commit-signature-verification/signing-commits)
and
[Telling Git about your signing key](https://docs.github.com/en/authentication/managing-commit-signature-verification/telling-git-about-your-signing-key)):

```bash
git config --global gpg.format ssh
git config --global user.signingkey ~/.ssh/id_ed25519.pub
git config --global commit.gpgsign true
```

> If you created your key under a different name or path, substitute it for
> `~/.ssh/id_ed25519.pub` in the commands here.

Then register that **same** public key a second time — now as a _signing_ key:

```bash
gh ssh-key add ~/.ssh/id_ed25519.pub --type signing --title "$(hostname) (signing)"
```

New commits now show as **Verified** on GitHub.

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
exec "$SHELL"
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

### GitHub Packages access

The `@a-novel` and `@a-novel-kit` npm scopes are published to
[GitHub Packages](https://docs.github.com/en/packages), not the public npm
registry — every JS workspace's `.npmrc` points those scopes at
`npm.pkg.github.com`. Installing them (anything that runs `pnpm install`) needs
a GitHub **personal access token** with the `read:packages` scope.

Create a classic token with `read:packages` at
[github.com/settings/tokens](https://github.com/settings/tokens) (reference:
[Authenticating to GitHub Packages](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-npm-registry#authenticating-to-github-packages)),
then add it to the `.npmrc` in your **home** directory — never a repo's:

```bash
# ~/.npmrc — outside every repo, so the token can't be committed by accident
//npm.pkg.github.com/:_authToken=ghp_your_token_here
```

> Keep the credential in `~/.npmrc`, not a project `.npmrc`. The per-repo
> `.npmrc` files are committed and only map the scopes to the registry; a token
> placed there is one `git add` away from being pushed. npm and pnpm merge your
> home `~/.npmrc` with the project one, so the registry mapping and your token
> combine automatically.

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

# Arch Linux — pacman prompts for an OCI runtime provider; choose `crun`
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

### Claude Code (optional — heavily recommended)

[Claude Code](https://code.claude.com/docs/en/overview) is Anthropic's
terminal coding agent, and our workflow leans on it heavily: every repository
ships curated Claude Code skills and settings (under `.claude/`) that teach
it the project's conventions, so it arrives pre-configured the moment you
open a workspace folder. You can develop a-novel without it — but you would
be passing on a lot of leverage.

Install with the [native installer](https://code.claude.com/docs/en/setup)
on every platform — macOS, Linux and WSL2 alike:

```bash
curl -fsSL https://claude.ai/install.sh | bash
```

The binary lands in `~/.local/bin`, and the installer adds that directory to
your `PATH`. If a new shell still can't find `claude`, add it yourself:

```bash
echo 'export PATH="$PATH:$HOME/.local/bin"' >> ~/.zshrc
exec "$SHELL"
```

(As with Go in step 1, another shell's rc file takes the same line instead,
e.g. `~/.bashrc`.)

Unlike the rest of the toolchain, Claude Code keeps itself up to date in the
background — the same reason we prefer Homebrew elsewhere, satisfied even
better here. (A `claude-code` Homebrew cask exists if you prefer it, but it
does not auto-update.)

Claude Code requires a paid Claude account (Pro, Max, Team or Enterprise) or
a Claude Console account — the free plan does not include it. To
authenticate, run `claude` once from any project directory and follow the
browser prompts.

Verify:

```bash
claude --version
# 2.1.173 (Claude Code)

claude doctor    # deeper health check: install method, auto-update status
```

The per-repo `.claude/` directories handle every _project_ convention, so
there is nothing to configure for a-novel itself. A few _personal_ settings,
though, noticeably improve the experience — all are global (saved to
`~/.claude/settings.json`), so you set them once and forget them:

- **Model** — run `/model` and pick the latest, most capable model; press Enter
  to save it as your default. The newest models unlock the `xhigh` effort level
  below.
- **Output style** — `/config` → **Output style** → **Explanatory**. Claude
  then narrates the reasoning behind each change, which is far easier to learn
  from and to review than terse, answer-only output.
- **Reasoning effort** — `/effort xhigh` makes deep reasoning your everyday
  default (it persists across sessions). Step up to `/effort max` for the
  genuinely hard tasks — architecture, gnarly debugging — where you want it to
  think without a token limit (`max` applies to the current session only).
  `xhigh` is only offered on recent models; if your selected model doesn't list
  it, the `/effort` picker shows which levels it does — pick a newer one
  (another reason to choose the latest above) or fall back to `high`.

`claude update` forces an update immediately if you don't want to wait for the
background one.

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

### Optional — service secrets

Some services need secret values to run. The CLI ships a small local secret
manager so these are never handled in the open: a tool — or an AI agent — can
run the toolchain against real secrets **without ever seeing them**. Values are
stored AES-256-GCM-encrypted under a local key and injected only into the child
process of `test` / `run` / `ui` — never printed, logged, committed, or passed
as an argument.

Set the store up once:

```bash
a-novel secrets init   # generate the local key + store (run once)
```

After that you only provision the secrets for the services you actually work on.
A service declares what it needs in a committed, value-free `.a-novel/secrets.yaml`,
and the CLI injects them automatically; a secret you haven't set yet is skipped
with a descriptive warning naming exactly what to run. **Each service states the
secrets it requires — and where to obtain them — in its own `CONTRIBUTING.md`;
look there for the precise commands.** The
[CLI README](https://github.com/a-novel-kit/stack/blob/HEAD/cli/README.md)
documents the full secrets model.

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
