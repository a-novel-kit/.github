## Board lifecycle and feature planning

This documentation walks you through our planning process, from a business feature to concrete code to a
shipped release.

As a contributor to `a-novel` and `a-novel-kit`, you turn ideas into features and grow the application.
The board takes on the bookkeeping, so you can focus on the two things that really matter: the
intention and the shipment. It tracks everything in between for you.

It starts with **the intention**: the specification behind your idea, and before any code you open a draft issue
to hold it. You refine the scope, look up the resources you need, and track your progress there. That
specification is the first place the work waits for a human. It is where you settle what to build, and
everything after it rests on it, so do it with care. Once it holds, the idea becomes a real Task,
Epic, Initiative, or Milestone, sized to the work. They are one idea at different scales, so let's follow
the smallest, the one that turns straight into code: the Task.

Then comes **the code**. A Task is (almost) always tied to a Pull Request, a branch that holds a working
copy of the code. As you pick it up and work, the board follows on its own and moves the Task from Ready
to In progress. You never set its status; you write code. When it is ready, you ask for review, and the
work waits for a human a second time: In Review.

Now it is a maintainer's turn. They read the code, ask for changes, and in the end approve it. That is
the last stop of the development cycle. Once your code is approved, on its own or as a whole Epic, the Task moves as
Done and merges to master.

Master is staging, not production. Every merged Task gathers there and settles in with everything else
that has landed. Nothing half-built reaches it: an Epic's Tasks merge together or not at all, so master
only ever holds whole features.

The last stop is **the shipment**. A merged Task waits in Awaiting Release until someone decides it is
time. That call is deliberate, and a maintainer makes it. A release takes what is on master and
publishes it to production as a versioned build, cut the moment it is right: as soon as a feature lands,
or once a few have gathered. One repository ships with one release; a whole Epic ships with one release
train across every repository it touched. Big changes go out
[in steps](taxonomy.md#versioning-and-releases), the dependency before the code that leans on it, so what
reaches production always fits together. And the Task is finished: it leaves the board, and its issue
closes.

On the board, that same path is a row of states:

```
  ◆ Triage  →  [ Ready → In progress ]  →  ◆ In Review  →  [ Done ]  →  ◆ Awaiting Release  →  shipped

  ◆ waits for you    ·    [ … ] the board moves it on its own
```

### What deserves your attention

Look at where the path waits for a person. Three points, and only three: the spec, the review, the
release. The board fills every gap between them, but it cannot do these. A vague spec ships the wrong
thing, however clean the code. A rushed review lets it through. A careless release times it badly. The
spec is yours; the review and the release are a maintainer's. At each one, judgment is the whole job.
Spend it there, and let the board carry the rest.

One rule sits above all of it: an Epic lands whole. Plan its pieces so they can land together, and keep
its siblings moving in step. A Task left behind holds back everything it is bound to.

### When the board needs you

Most days the board runs itself and you barely notice it. It heals its own drift within minutes and moves
your work along as you go. Every so often, though, it hands something back to you. These are the moments
worth knowing on sight.

**A Pull Request will not merge, and its gate is red.** It is an Epic member, waiting for its siblings. An
Epic lands whole, so a member waits until every other Task in the Epic is ready too. Get the rest
approved, and they merge together. There is nothing to fix on the PR itself.

**A Pull Request is frozen.** Part of an Epic reached master and part did not, so the board froze the rest
to keep master whole. If the missing piece can still merge, approve it and let it in. If the landed pieces
have to come back out, a maintainer runs a rollback, which reverts them in order through the same review
and gate. Reverting shipped code is serious, so it asks for a typed confirmation.

**A ticket appears, tagged `escalation`.** The board opened it because something is stuck and needs a
person: a hotfix that stalled, a check that never finished. Fix the underlying thing and the ticket closes
itself. It is a signal, not work to plan, so do not size or groom it.

**The board looks out of date.** A [sweep](#how-the-board-keeps-itself-in-step) re-checks everything every
fifteen minutes and fixes any drift, so it usually rights itself. If you need it sooner, run the sweep by
hand from the Actions tab.

**Everything has stopped.** The kill switch is on, halting every board automation at once for an incident.
While it is on, no Pull Request can merge, and the emergency hotfix path stays open on purpose. Turn it
off and the next sweep repairs whatever piled up.

**A version in production has a bug.** Patch it with a hotfix: you start the repository's hotfix with the
release line and the fix, and it cuts a new patch straight off the shipped version, then opens a Pull
Request to carry the fix back to master. Merge that one. Until it lands, the next release off that line
could bring the bug back.

### How the board keeps itself in step

You will rarely need this. But when the board does something you did not expect, it helps to know how it
works underneath.

Every status is written by one actor, the board's bot, always from what happened to a Pull Request: a
draft is in progress, an open one is in review, a merged one is awaiting release. Because it is read from
what happened and never typed in, the status cannot drift, and a manual edit will not stick. The same
check runs the moment something happens and again on the fifteen-minute sweep, so a missed event never
leaves the board wrong.

A parent tracks its children. An Epic sits at the least-advanced status among its Tasks, so it enters
review once they all have, awaits release once they all merge, and leaves the board once they all close.
An Initiative follows its Epics the same way. A parent is always an honest summary of what is under it.

One rule stands above the rest, and a required check guards it: an Epic lands whole, or not at all. The
[freeze and the rollback](#when-the-board-needs-you) are its safety net for a landing that only went
half-way.
