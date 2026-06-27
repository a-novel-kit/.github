# Contributing to a-novel-kit

## Taxonomy

The shared vocabulary behind the [`a-novel-kit`](https://github.com/a-novel-kit) organization — the
**platform** the product is built on. `a-novel-kit` holds the **libraries** the product depends on
and the **tooling** that builds, tests, and ships it. The product itself lives in the
[`a-novel`](https://github.com/a-novel) organization
([service & architecture concepts](https://github.com/a-novel/.github/blob/master/CONTRIBUTING.md));
the dependency runs one way — the product uses the kit, never the reverse. Each repository extends
this document with its own `CONTRIBUTING.md`.

### Repository kinds

A repository here is one of two kinds:

- A **library** is shared code other repositories depend on.
- **Tooling** is code that helps build the product but is never part of it — the `a-novel` CLI, the
  reusable CI workflows, the organization's meta-repositories.

The distinction that matters is **product versus tooling**: a library is _shipped_ — its code reaches
production through the repositories that depend on it — while tooling only supports the work of
shipping. Tests are part of the product they cover, not tooling.

### Libraries

A **library** factorizes logic that several repositories share into one **shared, versioned, owned**
place, depended on by an exact version rather than copied. The bar to add to one is high: a shared
dependency is a shared liability, so a library earns every addition — it must be something several
consumers need that nothing upstream already does well.

Libraries come in two shapes:

- An **aggregated library** gathers many small, independent helpers under one repository — `golib`
  for Go, `nodelib` for JavaScript and TypeScript. Each helper is minor on its own; the repository is
  their shared home.
- A **graduated library** is a single piece that grew a **broad API of its own** and now stands
  alone, as `jwt` did. Graduating is the moment a piece stops being a helper and becomes a project of
  its own — and it takes on the **public-package obligations** that come with standing alone: its own
  documentation, coverage, and a versioning policy it holds to.

The line between the two is API breadth: a helper stays in an aggregate until its API outgrows that
home, then it graduates.

### Tooling

**Tooling** builds, tests, runs, and ships the product without shipping inside it:

- The **`a-novel` CLI** is one command for the whole workspace — building, testing, running, and
  releasing every repository — in place of the per-repository scripts each would otherwise carry. How
  it works is its own reference; here it is simply the tool that operates the rest.
- The **reusable workflows** are **composite actions** every repository's CI calls instead of copying
  a recipe — referenced and pinned to a released version, so an upgrade is a deliberate bump.
- The **meta-repositories** (`.github`) hold the organization's shared documentation and policy —
  this document among them.

### Versioning and releases

Every library and tool is versioned by a **git tag** following semantic versioning, and every
consumer pins an **exact** version — never a floating range — so an upgrade is always a deliberate,
reviewable change. A **release** is a local tag bump; CI publishes from the pushed tag. A change that
spans repositories ships in **stages**: the dependency releases first, its consumers move to the
released version, and only then is an old path removed.

## External resources

- [Developer onboarding guide](https://github.com/a-novel-kit/.github/blob/master/README.md) — the
  toolchain and the `a-novel` CLI in practice.
- [Service & architecture concepts](https://github.com/a-novel/.github/blob/master/CONTRIBUTING.md)
  — the product-side vocabulary.
- [Semantic Versioning](https://semver.org/) — the versioning scheme.
