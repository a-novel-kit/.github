# The board lifecycle

We plan before we build. A feature is an idea about what the product should do, in the product's own
terms, and an idea cannot be coded directly. It has to be broken down first, into concrete pieces of
work small enough to build and review. Turning a feature into buildable work is where everything here
begins.

Those pieces are issues, and an issue is the bridge between a business idea and the code that delivers
it. Each one is a slice of a feature, made concrete. Issues live on a shared board that shows what is
planned, what is being built, and what is waiting to ship. You read the board to see where things stand.
It keeps itself in step with the work, so you rarely write to it.

A feature usually needs several issues, often across several repositories: a service, the library
beneath it, the client in front of it. Each is a **Task**. An **Epic** groups the Tasks of one feature.
An **Initiative** groups Epics under a broader direction that plays out over time.

The Tasks of an Epic belong together. A service that needs a new library function cannot ship until both
are in. So they land together, or not at all. Each is built on its own branch, the branches are linked,
and they merge as one. The main branch never holds half a feature.

The main branch is staging. Everything in flight integrates there and proves it fits. It is not
production. It moves fast and sometimes breaks, and that is what staging is for.

Shipping comes later, on purpose. A release bundles the main branch into a published version. You cut it
when the moment is right, which may be as soon as a feature lands or once several have gathered. Large
work ships in steps: the dependency first, then the code that leans on it. The board is always in a
state you can release.

That is the whole shape of it. What follows is your part in it, and what to do when something looks
wrong. A last section explains how the board stays in step, for when you need to reason about it. The
process is identical in both organizations, so this page lives here and a-novel links to it.

## Your part

Work reaches the board before any code. Someone plans the feature into an Epic and its Tasks, one per
repository, each with a size and a priority. Planning is its own craft; here it is enough that the work
exists first, so the board always leads the code.

You pick up a Task and open a pull request. There is one thing you must do by hand: close the Task from
the PR. The Task lives in the organization's `.github` repository, and your PR lives in a code
repository. A plain `Closes #123` points at your own repository and links nothing. Write the full
reference instead:

```
Closes a-novel-kit/.github#123
```

That link is the whole connection between your work and the board. With it, opening the PR moves the
Task to review, and merging moves it to awaiting release, on their own. Without it, the Task sits still
while your PR sails past.

You review by approving. A standalone PR merges once approved. An Epic's members wait for each other. A
member holds until every sibling across every repository is ready too, and then the whole set merges
together. A held PR is not stuck. It is waiting for the rest of its feature.

Releasing is deliberate, and only an admin does it. For one repository, you run its release and choose
the size of the bump. For a whole Epic, you run the release train once. It ships every repository the
Epic touched, records what it shipped, and is safe to re-run to finish any that did not go. Releasing is
what clears the work: the shipped Tasks leave the board and their issues close.

## When something looks wrong

The board heals itself, so most surprises pass within a few minutes. A handful are worth recognizing.

**A PR shows a red gate.** It is an Epic member waiting for its siblings. Get the rest of the Epic
approved, and the gate clears for the whole set at once. There is nothing to fix on the PR itself.

**A PR is frozen.** Part of an Epic landed and part did not, so the automation froze what remains to keep
the main branch whole. If the missing piece can still merge, approve it and let it in; that completes the
feature. If the landed pieces must come out, an admin runs a rollback, which reverts them in order
through the same gate. Reverting merged code is heavy, so it asks for a typed confirmation.

**A ticket appears, labelled `escalation`.** The automation opened it because something needs a person: a
hotfix that stalled, a check that never finished, an emergency path that failed. Fix the underlying
thing, and the ticket closes itself once the condition clears. It is not planning work, so do not size or
groom it.

**The board looks stale.** A sweep re-checks it every fifteen minutes and corrects any drift, so it
usually rights itself. If you need it sooner, run the sweep by hand from the Actions tab.

**Everything has stopped.** The kill switch is on. It halts every board automation at once, for an
incident. While it is on, the gate fails every PR so nothing slips through, and the emergency hotfix path
stays open on purpose. Turn it off to resume, and the next sweep repairs whatever built up.

**A shipped version has a bug.** Patch it with a hotfix. You dispatch the repository's hotfix with the
line to patch and the fix to apply, and it cuts a new patch straight off the released tag. It then opens
a PR to carry the fix forward to the main branch. Merge that PR. Until it lands, the next release off
that line could bring the bug back.

## Underneath

You rarely need this. When the board does something you did not expect, it is enough to reason about it.

The board's Status has a single author. One bot writes it, always from what happened to a PR: a draft is
in progress, an open PR is in review, a merged one is awaiting release. Because the Status is derived and
never entered, it cannot drift, and a hand edit does not stick. The same logic runs live on each PR event
and again on the fifteen-minute sweep, so a dropped event never leaves the board wrong.

A parent follows its children. An Epic sits at the least-advanced status among its Tasks. It reaches
review once they all have, awaiting release once they all merge, and it archives once they all close.
Initiatives follow their Epics the same way, so a parent is always a true summary of what is under it.

The gate that holds Epic members is a required check. It guards the one rule the whole system exists to
protect: an Epic's members land together, or none of them do. The freeze and the rollback are its safety
net for a landing that goes half-way.

Everything writes through that one bot, and everything checks the kill switch first. An unset switch means
running. Any value at all engages it, so a mistyped value fails safe rather than open.
