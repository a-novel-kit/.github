# Taxonomy

This document is the shared vocabulary of [`a-novel-kit`](https://github.com/a-novel-kit), the **platform**
the product is built on. The kit holds the **libraries** the product depends on and the **tooling** that
builds, tests and ships it. The product uses the kit, never the reverse. The product lives in the
[`a-novel`](https://github.com/a-novel) organization, which has
[its own taxonomy](https://github.com/a-novel/.github/blob/master/docs/taxonomy.md). Each repository
extends this document with its own `CONTRIBUTING.md`.

## Repository kinds

A repository here is a **library** or **tooling**.

A **library** is shared code other repositories depend on. **Tooling** is code that helps build the
product but is never part of it.

What separates the two is whether the code ships. A library ships: its code reaches production through
the repositories that depend on it. Tooling only supports the work of shipping. Tests belong to the
product they cover, so they are not tooling.

## Libraries

A **library** gives logic that several repositories share one versioned, owned home. Its consumers
depend on a released version instead of copying the code. The bar to add to a library is high, because
a shared dependency is a shared liability. An addition must be something several consumers need and
nothing upstream already does well.

Libraries come in two shapes.

An **aggregated library** gathers many small, independent helpers under one repository: `golib` for Go,
`nodelib` for JavaScript and TypeScript. Each helper is minor on its own, and the repository is their
shared home.

A **graduated library** is a single piece that grew a broad API of its own and now stands alone, as `jwt`
did. Graduating turns a helper into a project. The project takes on the obligations of a standalone
package: its own documentation, its own coverage, and a versioning policy it holds to.

API breadth draws the line between the two. A helper stays in an aggregate until its API outgrows that
home, then it graduates.

## Tooling

**Tooling** builds, tests, runs and ships the product without shipping inside it.

The **[`a-novel` CLI](https://github.com/a-novel-kit/stack)** is one command for the whole workspace. It
builds, tests and runs every repository the same way.

The **reusable workflows** and **composite actions** hold the CI recipe every repository shares. A
composite action bundles a few steps, and a reusable workflow a whole job. Repositories pin both to a
released version, so an upgrade is a deliberate bump.

The **meta-repositories** (`.github`) hold the organization's shared documentation and policy, this
document among them.

## Versioning and releases

Every library and tool is versioned by a **git tag** that follows
[semantic versioning](https://semver.org/). Every consumer records the exact version it builds with, in
`go.mod`, a lockfile or a pinned `uses:` tag, so an upgrade is always a reviewable change. A **release**
is cut in CI by the repository's release workflow, which tags the version and publishes it.

A change that spans repositories ships in **stages**. The dependency releases first, its consumers move
to the released version, and the old path is removed last.
