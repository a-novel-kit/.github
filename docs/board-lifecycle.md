## Board lifecycle and feature planning

This documentation walks you through our planning process, from a business feature to concrete code to a
shipped release.

As a contributor to `a-novel` and `a-novel-kit`, your job is to grow the application: new features,
better processes. The board, one per organization
([a-novel-kit](https://github.com/orgs/a-novel-kit/projects/1),
[a-novel](https://github.com/orgs/a-novel/projects/7)), hands most of the automation, so you can focus on
the two things that matter: the intention and the shipment.

**The intention** starts as a draft issue, the first layer. There you refine the scope, gather external
resources, and track your progress. Its description is the first human gate, where you settle what to
build. From it comes a real Task, Epic, Initiative, or Milestone, depending on its size. They all work
the same way, so let's take the smallest, the one that turns straight into code: the Task.

**The code** is where you build the Task. A Task is (almost) always tied to a Pull Request. That is a
branch, a working copy of the code where you do the work. Open the Pull Request, and the Task reaches the
second human gate: In Review.

There a maintainer reads the code and asks for any changes. This is the last gate of the development
cycle. Once approved, on its own or as a whole Epic, the work merges to master, and the release process
takes over.

Master is staging, not production. Every merged Task gathers there, next to everything else in progress.
This is why an Epic's Tasks merge as one: master holds whole features, never half of one.

**The shipment** is the other end that is yours. It is deliberate, and a maintainer makes the call. A
release takes what is on master and publishes it to production, as a versioned build. You cut it when the
moment is right: as soon as a feature lands, or once a few have gathered. For a single repository, that is
one release. For a cross-repo Epic, one release train ships every repository it touched. Big changes go
out [in steps](taxonomy.md#versioning-and-releases), the dependency before the code that uses it, so what
reaches production always fits together. Once a Task ships, it is done. It leaves the board, and its issue
closes.

```
    spec        build             open PR         approve                release
  ◆ Ready  →    In progress  →  ◆ In review  →  ◆ Awaiting release  →  ◆ shipped

  ◆ = a human gate; the board moves the rest on its own.
```

That is the whole path. The two ends are yours: the intention at the start and the shipment at the end.
The board handles the middle, and [tells you](#when-the-board-needs-you) on the rare day it needs you.

### When the board needs you

Most days you never think about the board. It moves your work along as you go. Now and then, though, it
needs a person. These are the cases worth knowing.

**A Pull Request will not merge, and its gate is red.** It is an Epic member, waiting for its siblings.
An Epic lands whole, so a member waits until every other Task in the Epic is ready too. Get the rest of
the Epic approved, and they merge together. There is nothing to fix on the PR itself.

**A Pull Request is frozen.** Part of an Epic reached master and part did not, so the board froze the rest
to keep master whole. If the missing piece can still merge, approve it and let it in. If the landed pieces
have to come back out, a maintainer runs a rollback. It reverts them in order, through the same review and
gate. Reverting shipped code is serious, so it asks for a typed confirmation.

**A ticket appears on the board, tagged `escalation`.** The board opened it because something is stuck and
needs you: a hotfix that stalled, a check that never finished. Fix the underlying thing, and the ticket
closes itself. It is a signal, not work to plan, so do not size or groom it.

**The board looks out of date.** A [sweep](#how-the-board-keeps-itself-in-step) re-checks everything every
fifteen minutes and fixes any drift, so it usually catches up on its own. If you need it sooner, run the
sweep by hand from the Actions tab.

**Everything has stopped.** The kill switch is on. It halts every bit of board automation at once, for an
incident. While it is on, no Pull Request can merge, and the emergency hotfix path stays open on purpose.
Turn it off, and the next sweep repairs whatever piled up.

**A version already in production has a bug.** Patch it with a hotfix. You start the repository's hotfix
with the release line and the fix, and it cuts a new patch straight off the shipped version. It then opens
a Pull Request to carry the fix back to master. Merge that one. Until it lands, the next release off that
line could bring the bug back.

### How the board keeps itself in step

You rarely need this. But when the board does something you did not expect, it helps to know how it works
underneath.

Every status is written by one actor, the board's bot, and always from what happened to a Pull Request: a
draft is in progress, an open one is in review, a merged one is awaiting release. Because the status is
read from what happened, not typed in, it cannot drift, and a manual edit will not stick. The same check
runs the moment something happens, and again on the fifteen-minute sweep, so a missed event never leaves
the board wrong.

A parent tracks its children. An Epic sits at the least-advanced status among its Tasks. So it enters
review once they all have, awaiting release once they all merge, and it leaves the board once they all
close. An Initiative follows its Epics the same way. A parent is always an honest summary of what is under
it.

The gate that holds an Epic's members guards the one rule the whole board protects: an Epic lands whole,
or not at all. The [freeze and the rollback](#when-the-board-needs-you) are its safety net for a landing
that only went half-way.
