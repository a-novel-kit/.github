## Board lifecycle and feature planning

This documentation walks you through our planning process, from a business feature to concrete code to a
shipped release.

As a contributor to `a-novel` and `a-novel-kit`, your job is to grow the application: new features,
better processes. The board, one per organization
([a-novel-kit](https://github.com/orgs/a-novel-kit/projects/1),
[a-novel](https://github.com/orgs/a-novel/projects/7)), hands you the automation for the rest, so you can
focus on the two things that matter, the intention and the shipment.

**The intention** starts as a draft issue, the first layer, where you refine the scope, gather external
resources, and track your progress. Its description is the first human gate: this is where you cement
your specifications. From it comes a real Task, Epic, Initiative, or Milestone, sized to the work. They
all scale the same idea, so let's take the smallest, the one that maps straight to code: the Task.

**The code** is a Task made concrete. A Task is (almost) always tied to a Pull Request, the branch where
your raw code lives and where you iterate. Open that Pull Request and the Task reaches the second human
gate: In Review.

There a maintainer reads the code and calls out what to fix or improve, the last gate of the development
cycle. Once approved, alone or as a full Epic, the work merges to master, where the release process takes
over.

Master is staging, not production. Every merged Task gathers there and integrates with the rest in
flight. That is why an Epic's Tasks merge as one: master holds whole features, never halves.

**The shipment** is the other end that is yours, a maintainer's deliberate call. A release publishes the
current master as a versioned build in production, cut when the moment is right: as soon as a feature
lands, or once a few have gathered. One repository ships with one release; a cross-repo Epic ships with
one release train across every repository it touched. Big changes go out
[in steps](taxonomy.md#versioning-and-releases), the dependency before the code that uses it, so
production always holds a set that fits. Once a Task ships it is done: off the board, its issue closed.

```
  ◆ = a human gate

  the intention   ◆ you write the spec           →  Ready
  the code          you build it on a branch     →  In progress
                  ◆ you open the PR for review    →  In review
                  ◆ a maintainer approves it      →  Awaiting release
  the shipment    ◆ a maintainer cuts a release   →  shipped (off the board)
```

That is the whole path. The two ends are yours, the intention at the start and the shipment at the end;
the board keeps the middle in step, and [tells you](#when-the-board-needs-you) on the rare day it needs a
hand.

### When the board needs you

Most days you never think about the board; it moves your work along as you go. Now and then, though, it
needs a person. These are the cases worth knowing.

**A Pull Request will not merge, and its gate is red.** It is an Epic member, waiting for its siblings.
An Epic lands whole, so a member holds until every other Task in the Epic is ready too. Get the rest of
the Epic approved, and they merge together. There is nothing to fix on the PR itself.

**A Pull Request is frozen.** Part of an Epic reached master and part did not, so the board froze what
remains to keep master whole. If the missing piece can still merge, approve it and let it in. If the
landed pieces have to come back out, a maintainer runs a rollback, which reverts them in order through
the same review and gate. Reverting shipped code is serious, so it asks for a typed confirmation.

**A ticket appears on the board, tagged `escalation`.** The board opened it because something is stuck
and needs you: a hotfix that stalled, a check that never finished. Fix the underlying thing, and the
ticket closes itself. It is a signal, not work to plan, so do not size or groom it.

**The board looks out of date.** A [sweep](#how-the-board-keeps-itself-in-step) re-checks everything
every fifteen minutes and corrects any drift, so it usually catches up on its own. If you need it sooner,
you can run the sweep by hand from the Actions tab.

**Everything has stopped.** The kill switch is on. It halts every bit of board automation at once, for an
incident. While it is on, no Pull Request can merge, and the emergency hotfix path stays open on purpose.
Turn it off, and the next sweep repairs whatever piled up.

**A version already in production has a bug.** Patch it with a hotfix. You dispatch the repository's
hotfix with the release line and the fix, and it cuts a new patch straight off the shipped version, then
opens a Pull Request to carry the fix back to master. Merge that one. Until it lands, the next release off
that line could bring the bug back.

### How the board keeps itself in step

You rarely need this, but when the board does something you did not expect, it helps to know how it works
underneath.

Every status is written by one actor, the board's bot, and always from what actually happened to a Pull
Request: a draft is in progress, an open one is in review, a merged one is awaiting release. Because the
status is read from reality and never typed in, it cannot drift, and a manual edit will not stick. The
same reasoning runs the moment something happens, and again on the fifteen-minute sweep, so a missed
event never leaves the board wrong.

A parent tracks its children. An Epic sits at the least-advanced status among its Tasks, so it enters
review once they all have, awaiting release once they all merge, and it leaves the board once they all
close. An Initiative follows its Epics the same way. A parent is always an honest summary of what is
under it.

The gate that holds an Epic's members guards the single rule the whole board protects: an Epic lands
whole, or not at all. The [freeze and the rollback](#when-the-board-needs-you) are its safety net for a
landing that only went half-way.
