# Developing A-Novel

The developer onboarding guide for the [`a-novel`](https://github.com/a-novel)
and [`a-novel-kit`](https://github.com/a-novel-kit) organizations: the
toolchain, the [`a-novel` CLI](https://github.com/a-novel-kit/stack), and
where to go from there.

- [1. Install the toolchain](#1-install-the-toolchain)
- [2. Install the a-novel CLI](#2-install-the-a-novel-cli)
- [3. Use the a-novel CLI](#3-use-the-a-novel-cli)

## 1. Install the toolchain

Development is supported on **macOS and Linux** (any bash-compatible
environment). macOS commands assume [Homebrew](https://brew.sh):

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

### Git — [install docs](https://git-scm.com/downloads)

| OS     | Install                | Update             |
| ------ | ---------------------- | ------------------ |
| macOS  | `brew install git`     | `brew upgrade git` |
| Ubuntu | `sudo apt install git` | `sudo apt upgrade` |
| Arch   | `sudo pacman -S git`   | `sudo pacman -Syu` |

Verify: `git --version`

You also need [SSH access to GitHub](https://docs.github.com/en/authentication/connecting-to-github-with-ssh)
— all repos clone over SSH. Verify: `ssh -T git@github.com` (greets you by
username).

### Go — [install docs](https://go.dev/doc/install)

The minimum version is set by [`cli/go.mod`](https://github.com/a-novel-kit/stack/blob/master/cli/go.mod)
in the stack repo. The Ubuntu `apt` package lags behind — use snap or the
official tarball there.

| OS     | Install                          | Update                 |
| ------ | -------------------------------- | ---------------------- |
| macOS  | `brew install go`                | `brew upgrade go`      |
| Ubuntu | `sudo snap install go --classic` | `sudo snap refresh go` |
| Arch   | `sudo pacman -S go`              | `sudo pacman -Syu`     |

Verify: `go version`

### Node.js — [install docs](https://nodejs.org/en/download)

We track the **Current** release line, not LTS — check `engines.node` in the
[stack `package.json`](https://github.com/a-novel-kit/stack/blob/master/package.json)
for the floor. On Ubuntu, use [nvm](https://github.com/nvm-sh/nvm#installing-and-updating)
(the `apt` package is far too old).

| OS     | Install                      | Update              |
| ------ | ---------------------------- | ------------------- |
| macOS  | `brew install node`          | `brew upgrade node` |
| Ubuntu | `nvm install node` (via nvm) | `nvm install node`  |
| Arch   | `sudo pacman -S nodejs npm`  | `sudo pacman -Syu`  |

Verify: `node --version`

### pnpm — [install docs](https://pnpm.io/installation)

| OS     | Install                                             | Update              |
| ------ | --------------------------------------------------- | ------------------- |
| macOS  | `brew install pnpm`                                 | `brew upgrade pnpm` |
| Ubuntu | `curl -fsSL https://get.pnpm.io/install.sh \| sh -` | `pnpm self-update`  |
| Arch   | `sudo pacman -S pnpm`                               | `sudo pacman -Syu`  |

Verify: `pnpm --version`

### Podman — [install docs](https://podman.io/docs/installation)

On macOS, prefer Homebrew over the `.dmg` installer — it updates with the
rest of your toolchain. Podman on macOS runs inside a VM that you create
once (`machine init`) and start after each reboot (`machine start`).

| OS     | Install                                                              | Update                |
| ------ | -------------------------------------------------------------------- | --------------------- |
| macOS  | `brew install podman && podman machine init && podman machine start` | `brew upgrade podman` |
| Ubuntu | `sudo apt install podman`                                            | `sudo apt upgrade`    |
| Arch   | `sudo pacman -S podman`                                              | `sudo pacman -Syu`    |

Verify: `podman info` (must succeed — it also proves the service/VM is up)

### podman-compose — [install docs](https://github.com/containers/podman-compose#installation)

The external provider behind `podman compose`, used for service
dependencies and test environments.

| OS     | Install                           | Update                        |
| ------ | --------------------------------- | ----------------------------- |
| macOS  | `brew install podman-compose`     | `brew upgrade podman-compose` |
| Ubuntu | `sudo apt install podman-compose` | `sudo apt upgrade`            |
| Arch   | `sudo pacman -S podman-compose`   | `sudo pacman -Syu`            |

Verify: `podman compose version`

### GitHub CLI — [install docs](https://github.com/cli/cli#installation)

Ubuntu's `apt` package works; the [official apt repository](https://github.com/cli/cli/blob/trunk/docs/install_linux.md)
ships fresher releases if you need them.

| OS     | Install                     | Update             |
| ------ | --------------------------- | ------------------ |
| macOS  | `brew install gh`           | `brew upgrade gh`  |
| Ubuntu | `sudo apt install gh`       | `sudo apt upgrade` |
| Arch   | `sudo pacman -S github-cli` | `sudo pacman -Syu` |

Verify: `gh --version`, then authenticate with `gh auth login` and confirm
with `gh auth status`.

## 2. Install the a-novel CLI

The [`a-novel` CLI](https://github.com/a-novel-kit/stack) is the single
entrypoint for building, testing, running and releasing every project. It
lives in the **stack** repo, which also anchors your local checkouts of all
org repositories:

```bash
git clone git@github.com:a-novel-kit/stack.git ~/git-projects/a-novel
cd ~/git-projects/a-novel/cli
go install ./cmd/a-novel    # requires $(go env GOPATH)/bin on your PATH

a-novel core setup          # one-time bootstrap: env checks, state dirs, shell rc, daemon
a-novel core sync           # clone / fast-forward the org repos into app/ and kit/
```

Verify:

```bash
a-novel --version
a-novel core status
```

Update:

```bash
git -C ~/git-projects/a-novel pull
a-novel install             # rebuild + reinstall + state-preserving daemon restart
```

The full setup guide — including GitHub access and the org bot credentials —
is the [stack README](https://github.com/a-novel-kit/stack#readme).

## 3. Use the a-novel CLI

```bash
a-novel test -y                          # run all Go + pnpm tests in the working tree
a-novel build -y                         # build binaries, bundles and images
a-novel run start <service>/<target>     # start a service target (deps auto-cascade)
a-novel run ui                           # full-screen TUI
a-novel <verb> --help                    # exhaustive help for any subcommand
```

Lint, format and generate are pnpm scripts, uniform across repos:
`pnpm lint:go`, `pnpm format:go`, `pnpm generate:go`, …

Full usage docs: the
[stack README](https://github.com/a-novel-kit/stack#use-the-cli) and the
[CLI reference](https://github.com/a-novel-kit/stack/blob/master/cli/README.md).
