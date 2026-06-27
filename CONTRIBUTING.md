# Contributing to a-novel-kit

## Taxonomy

The shared vocabulary behind the [`a-novel-kit`](https://github.com/a-novel-kit) organization — the
**libraries** and **tooling** the product is built on. Each repository specifies its own taxonomy in
its `CONTRIBUTING.md`, which acts as an extension of this document. The service-side concepts — what
a service is, its layers and APIs — live in the
[service & architecture concepts](https://github.com/a-novel/.github/blob/master/CONTRIBUTING.md).

The work spans two GitHub organizations. **`a-novel`** holds the **product**: the backend services
and the frontend users reach. **`a-novel-kit`** holds the **platform** beneath it: the shared
libraries the product depends on, and the tooling that builds, tests, and ships it. The dependency
runs one way — the product uses the kit, never the reverse.

### Repository kinds

Every repository is one **kind**, and its conventions follow from the kind:

- A **service** is one backend domain environment (see the
  [service concepts](https://github.com/a-novel/.github/blob/master/CONTRIBUTING.md)).
- A **platform** is a frontend — the product a user interacts with directly.
- A **library** is shared, versioned code other repositories depend on by an exact version.
- **Tooling** is code used to develop the product but never shipped inside it — the `a-novel` CLI,
  the reusable CI workflows, the organizations' meta-repositories.

The line that matters is **product versus tooling**: a service, a platform, and a library are shipped
— their code, or the artefacts built from it, reach production — while tooling only supports the work
of shipping them. Tests are part of the product they cover, not tooling.

### Libraries

A **library** is code several repositories share, published once and depended on by an exact version
rather than copied. The bar to add to one is high: a shared dependency is a shared liability, so a
library stays small and earns every addition.

**golib** is the shared Go library, kept deliberately minimal. It is a set of **sub-packages**, each
a directory with one focused job — configuration, tracing, an HTTP framework, and so on. A candidate
earns a place only when several services need it and nothing upstream covers it cleanly; if a
well-maintained library already does the job, that library is used instead.

When a sub-package grows a **broad API of its own**, it **graduates** to its own repository, as
`jwt` did. A graduated library is no longer a corner of `golib`; it is a standalone project, and it
takes on the **public-package obligations** that come with standing alone — its own documentation,
coverage reporting, and a versioning policy it holds to.

**nodelib** is the JavaScript and TypeScript counterpart, shaped to that ecosystem: a single **pnpm
workspace** holding several **packages**, released together under one version and published to a
package registry. A **catalog** pins the dependency versions those packages share, so they stay
aligned.

### Reusable workflows

The **workflows** repository holds **composite actions** — the reusable building blocks of every
repository's CI. A repository does not copy a build or release recipe; it **references** an action
and **pins** it to a released version, so an upgrade is a deliberate version bump rather than a
silent change. The actions are grouped by what they automate: Go, Node, building, publishing, and so
on.

### The stack and its CLI

The **stack** repository hosts the **`a-novel` CLI** — one command to build, test, run, and release
every repository across both organizations, in place of per-repository scripts.

- A **stack** is a registered checkout the CLI operates on; its **workspace** is the set of product
  and kit repositories under it, kept in sync from their remotes.
- A **daemon** — one per user, reached over a local socket — supervises whatever is running, so the
  command line and the terminal UI are thin clients over a single source of truth.
- The CLI runs a service's **targets** (see
  [runnable units](https://github.com/a-novel/.github/blob/master/CONTRIBUTING.md#runnable-units)):
  it tells a **server** from a **job**, and brings a service's **infra** up first. It can run a
  target in two **modes** — directly from source, or as a container.
- **Secrets** a service needs are kept in a local, encrypted store and injected into the child
  process — never written out in the open, never committed.

Lint, format, and generate are deliberately **not** CLI commands: each repository exposes them as
uniform `pnpm` scripts instead.

### Commenting as the bot

Some automation comments on a pull request or issue as the organization's **bot** rather than as a
person. The bot's signing key never touches a developer's machine. Instead, a per-organization
**dispatcher** workflow holds the key: a developer triggers it with their own credentials, and the
workflow mints a short-lived, comment-only token and posts. There is one dispatcher per organization,
because the key is an organization secret that cannot cross organization boundaries — so a
compromised trigger can ask for a comment and nothing more.

### Versioning and releases

Every library, action, and tool is versioned by a **git tag** following semantic versioning, and
every consumer pins an **exact** version — never a floating range — so an upgrade is always a
deliberate, reviewable change. A **release** is a local tag bump; CI publishes the artefact from the
pushed tag. A change that spans repositories ships in **stages**: the dependency releases first, its
consumers move to the released version, and only then is an old path removed.

## External resources

- [Developer onboarding guide](https://github.com/a-novel-kit/.github/blob/master/README.md) — the
  toolchain and the `a-novel` CLI in practice.
- [Service & architecture concepts](https://github.com/a-novel/.github/blob/master/CONTRIBUTING.md)
  — the service-side vocabulary this document refers to.
- [Semantic Versioning](https://semver.org/) — the versioning scheme.
- [Go modules](https://go.dev/ref/mod) and [pnpm workspaces](https://pnpm.io/workspaces) — how the
  libraries are structured and published.
