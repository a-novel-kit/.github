## Board lifecycle and feature planning

This documentation will take you through our entire planning process, from a business feature to the
concrete implementation, all the way to shipping to production.

As a contributor to `a-novel` and `a-novel-kit`, your goal is to build on our foundations and expand the
application: greater features, better processes, and so on. The board is there to give you automation, so
you can focus on what really matters: the intention, and the shipment (more on that later).

**The intention** usually begins as a draft issue (the first layer), where you refine the scope of your
work, look up external resources, and track your progress. That description is the first human gate of
the process: it is where you cement your specifications. This crucial step leads to a real Task, Epic,
Initiative, or even Milestone, depending on its size. They all scale the same concept, so let's focus on
the smallest, most down-to-earth one, the one that translates directly into an implementation: the Task.

**The code** is where a Task turns your business idea into a concrete implementation. A Task is (almost)
always linked to a Pull Request, where your raw code actually lives. A Pull Request is a branch, a local
copy of the code you can iterate on. Once you open a Pull Request on a Task, it reaches the second human
gate: In Review.

In review, it falls to a maintainer (a technical role) to read the code and call out what needs fixing
or improving. This is the last gate of the development cycle. Once a Task is approved, or a whole set of
Tasks under an Epic is, it becomes mergeable and lands on master, where the release process takes over.
That process is separate from the strict development cycle.

Master is where all the accepted work gathers, and it is a staging ground, not production. Every merged
Task lands there and integrates with everything else in flight. This is why an Epic's Tasks merge as one:
master should only ever hold whole features, never half of one.

**The shipment** is the other end that is yours. Shipping is deliberate, and it is a maintainer's call. A
release takes the current state of master and publishes it, as a versioned build that reaches
production. You cut one when the moment is right, sometimes as soon as a feature lands, sometimes once a
few have gathered. One repository ships with a single release; a cross-repo Epic ships with a single
release train that covers every repository it touched. Large changes go out in steps, the dependency
first and the code that uses it next, so production always holds a set that fits together. Once a Task
ships, it is done: it leaves the board, and its issue closes.

That is the whole path, from an idea to production. The two ends are yours, the intention you cement at
the start and the shipment you decide at the end, and the board keeps everything in between in step for
you.

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

**The board looks out of date.** A sweep re-checks everything every fifteen minutes and corrects any
drift, so it usually catches up on its own. If you need it sooner, you can run the sweep by hand from the
Actions tab.

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
whole, or not at all. The freeze and the rollback are its safety net for a landing that only went
half-way.
