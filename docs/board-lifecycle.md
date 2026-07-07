## Board lifecycle and feature planning

This documentation walks you through our planning process, from a business feature to concrete code to a
shipped release.

As a contributor to `a-novel` and `a-novel-kit`, you turn ideas into features and grow the application.
The board takes on the bookkeeping, so you can focus on the two things that really matter: the intention
and the shipment. It tracks everything in between for you.

It starts with **the intention**: the specification behind your idea. Before coding anything, the first
step is to translate this specification into an issue (the GitHub layer that will later interface with
concrete code). You refine the scope, look up the resources you need, and track your progress there. That
specification is human-gated: it's the initial input the whole process will be based on. Taking time
validating and refining it is what makes the whole process smooth and well-scoped. Once it holds, the
GitHub issue becomes a Task, Epic, Initiative, or Milestone depending on its size, Task being the smallest
unit.

Then comes **the code**. A Task is (almost) always tied to a Pull Request, a branch that holds a working
copy of the code. As you pick it up and work, the Task moves from Ready to In progress. You usually don't
touch the board yourself: an agent runs the Pull Request, and the status follows it on its own. The agent
only stands in for a person, though, so you can take the work by hand, drive the Pull Request yourself,
and the board tracks it the same way. When it is ready, your task moves to the second human-gated stage:
the review process.

Now it is a maintainer's turn. They read the code, ask for changes, and in the end approve it. That is
the last stop of the development cycle. Once your code is approved, on its own or as a whole Epic, the
Task moves as Done and merges to master.

Master is staging, not production. Every merged Task gathers there and settles in with everything else
that has landed. Nothing half-built reaches it: an Epic's Tasks merge together or not at all, so master
only ever holds whole features.

The last stop is **the shipment**. A merged Task waits in Awaiting Release until someone decides it is
time. That call is deliberate, and a maintainer makes it. A release takes what is on master and publishes
it to production as a versioned build, cut the moment it is right. The release process is separate,
managed by admins using automated publish workflows (a single release for a repo, or a release train for
cross-repo deployments). Big changes go out [in steps](taxonomy.md#versioning-and-releases), the
dependency before the code that leans on it, so what reaches production always fits together. And the Task
is finished: it leaves the board, and its issue closes.

On the board, that same path is a row of states:

```
  ◆ Triage  →  [ Ready → In progress ]  →  ◆ In Review  →  [ Done ]  →  ◆ Awaiting Release  →  shipped

  ◆ waits for you    ·    [ … ] the board moves it on its own
```

### What deserves your attention

Execution is the part you can delegate. The board handles it, and an agent can even write the code.
Comprehension and judgment are the part you cannot, and three stages are made of nothing else: the spec,
the review, the release.

The **spec** sets the target. Build from a vague one and you build the wrong thing, however clean the
code, so a sharp spec is the whole foundation.

The **review** is where you understand what was built. It is not a rubber stamp. You read the code to
grasp it, because a rushed review lets bugs through and lets weak structure settle in, and every later
change builds on what you let stand.

The **release** picks the moment. Ship at the wrong time and even good work lands badly.

The board catches none of this, and neither does an agent; only a person who understands the work can.
You can hand off the typing, never the understanding. Your part is the spec; a maintainer holds the review
and the release. At whichever stage is yours, comprehension and judgment are the whole job, so spend them
there and trust the board with the rest.

One rule sits above all of it: an Epic lands whole. Plan its pieces so they can land together, and keep
its siblings moving in step. A Task left behind holds back everything it is bound to.

### Shaping the work

The journey followed a single Task. Most features are larger than one Task, and the board gives them a
shape.

Every item on the board is an issue, and every issue has a **type**. The types nest, from the branch you
write up to the effort that spans a year.

A **Task** is one branch of work, about one Pull Request. It is the leaf you actually build, and it closes
when its Pull Request merges. A **Feature** groups a few Tasks that make up one capability inside a single
repository. An **Epic** groups the Tasks of one feature across every repository the feature touches, and
it is the unit that ships: roughly one Epic, one release. An **Initiative** stands above Epics, the
umbrella for a broad effort that runs across many releases; it does no work of its own, sitting in
**Tracking** until its Epics are done. Cutting across all of these, a **Milestone** names one shared goal,
so a roadmap can gather everything aimed at the same outcome.

An Epic is named for the goal it delivers, never for the version it will become. The version is its
target, not its name.

Some work writes no code at all: creating a repository, changing a setting, granting a permission. That is
a **meta task**. It carries a `meta` label, skips the code lifecycle, and runs Backlog → Triage → Ready →
**Applied** before it archives itself. Keep meta tasks in their own Epic, never mixed with code Tasks.

Choosing between the types comes down to one rule: **work that cannot merge at the same time belongs to
different Epics.** One Task is one branch; two pieces that touch the same repository are one Task or two
stages, not two Tasks side by side. A breaking change across repositories is never one Epic either (the
next section explains why). And a whole effort spanning several releases is an Initiative with one Epic per
release, not a single Epic carrying them all.

Where an issue lives is what links it to your code. A Task lives in the repository it changes, so its Pull
Request references it directly. Its Epic lives in the organization's `.github` repository and adopts the
Task as a child across repositories, which is how a feature spanning five repositories still rolls up to
one place. A dependency in the _other_ organization cannot be a child that way, so it is tracked as a plain
link instead.

A parent's status is a summary of its children. An Epic sits at the least-advanced status among its Tasks:
it reaches review only once every Task has, and awaiting release only once every Task has merged. When the
last child closes, the parent archives itself, and the same holds from Epics up to their Initiative.

### Why an Epic lands whole

An Epic's Tasks merge together or not at all. That rule shapes how you plan, so it is worth understanding
why it exists.

A feature often spans repositories. A service needs a new function from a library; a client needs a new
field from a schema. Merge the service before the library and the service no longer builds. Preventing
that broken state is the whole point of the rule.

The obvious fix, merging every piece at the same instant, is not one GitHub offers: it merges one Pull
Request at a time, one repository at a time, with no atomic merge across repositories. So "lands whole"
cannot mean "at the same instant." It means something you can actually guarantee: **whatever order the
Tasks land in, and however many land before the rest, every repository still builds.**

You earn that by keeping every step backward-compatible, which is why a breaking change across
repositories is never one Epic. It is two. An **expand** Epic adds the new path beside the old and ships
it, so both work at once. Then a **contract** Epic removes the old path, once the expand release is out and
nothing depends on the old path surviving. Between them, any landing order is safe.

The board holds an Epic together with a required check, the **merge-gate**. A Pull Request joins an Epic by
carrying an `epic:<N>` label that only a maintainer can add, so membership is trusted rather than guessed
from a `Closes` line. The gate holds every member until all are approved, then greens them at once and
lets the merge queue land them together.

When a member lands and its siblings do not, the board sees the **partial landing** and
[freezes the rest](#when-the-board-needs-you), so nothing else merges until the Epic is whole again. The
fix is almost always to roll forward and get the stuck sibling in. Reverting what already merged is a last
resort, run by hand, because undoing a merge is far more dangerous than finishing one.

### Shipping

Merging is not shipping. Master is staging; a release is what reaches production, and cutting one is always
a deliberate, human call.

Each repository ships on its own, as a [`vX.Y.Z` tag](taxonomy.md#versioning-and-releases), and a release
publishes everything on that repository's master at that moment. You cut it from CI, never from your
machine, by running the release workflow and choosing the size of the bump. Only an admin can, through a
protected gate, and there is no local release command on purpose.

One repository ships with one release. A cross-repo Epic ships with a **release train**: a single dispatch
that runs each repository's own release, in any order, and is safe to re-run to finish any that did not go.

Two words are worth keeping apart. Inside one Epic, the Tasks are a **convoy** — they land together, in any
order, because each builds on its own. Across Epics, where one depends on another, they form a **release
train** — the dependency ships first, and the code that needs it catches the next departure once it can
point at the published version.

That order is a hard rule. A dependency is **published**, not merely merged, before anything downstream
rolls out, and you never merge a consumer that still points at an unreleased dependency.

A published version is permanent; a tag can never be taken back. So recovery is almost always to roll
forward with a new release that supersedes the bad one, never to rewrite what shipped.

### When the board needs you

Most days the board runs itself and you barely notice it. It heals its own drift within minutes and moves
your work along as you go. Every so often, though, it hands something back to you. These are the moments
worth knowing on sight.

**A Pull Request will not merge, and its gate is red.** It is an Epic member, waiting for its siblings. An
Epic lands whole, so a member holds until every other Task in the Epic is ready too. Get the rest approved,
and the gate greens them together. There is nothing to fix on the Pull Request itself.

**A Pull Request is frozen.** Part of an Epic reached master and part did not, so the board froze the rest
to keep master whole. If the missing piece can still merge, approve it and let it in. If a sibling was
abandoned instead, so the set can never be complete, the board holds and waits for you rather than guessing
which survivors still form the feature. And if the landed pieces have to come back out, a maintainer runs a
rollback, which reverts them in order through the same review and gate. Reverting shipped code is serious,
so it asks for a typed confirmation.

**A ticket appears, tagged `escalation`.** The board opened it because something is stuck and needs a
person: a hotfix that stalled, a check that never finished, an Epic that cannot complete. Fix the
underlying thing and the ticket closes itself. It is a signal, not work to plan, so do not size or groom
it. A pile of open escalation tickets is itself the alarm.

**The board looks out of date.** A [sweep](#how-the-board-keeps-itself-in-step) re-checks everything every
fifteen minutes and fixes any drift, so it usually rights itself. If you need it sooner, run the sweep by
hand from the Actions tab.

**A release stalled halfway.** A release train fans out to many repositories, and one can park at its
approval gate or fail after tagging. Re-run the train: it reads what actually shipped from the live tags
and only re-cuts the repositories still missing, so finishing a partial release is safe. A tag with no
published release behind it is flagged loudly for a person, never skipped in silence.

**Everything has stopped.** The kill switch is on, halting every board automation at once for an incident.
While it is on, no Pull Request can merge, and the emergency hotfix path stays open on purpose. Turn it off
and the next sweep repairs whatever piled up.

**A version in production has a bug.** Patch it with a hotfix: you run the repository's hotfix workflow
with the release line and the fix, and it cuts a new patch straight off the shipped version, then opens a
Pull Request to carry the fix back to master. Merge that one. Until it lands, the next release off that
line could bring the bug back. A cross-repo bug is not a special case; it is an ordinary Epic, run fast.

### How the board keeps itself in step

You will rarely need this. But when the board does something you did not expect, it helps to know how it
works underneath. The whole system is built from GitHub's own parts — required checks, the merge queue,
scheduled jobs — driven by a single actor, the board's bot.

Every status is written by that one bot, always from what happened to a Pull Request: a draft is in
progress, an open one is in review, an approved one is done, a merged one is awaiting release. Because the
status is read from what happened and never typed in, it cannot drift, and a manual edit will not stick.
There is one carve-out: a meta task has no Pull Request to read from, so its final Applied status is set by
hand.

GitHub raises no event when a Status field changes, so the board is never driven by its own state; it is
always derived from the Pull Requests and issues underneath. That derivation runs the moment something
happens, and again on a sweep every fifteen minutes that re-checks the whole board against live GitHub
truth. A dropped event, a missed webhook, a stray hand edit: the next sweep quietly puts it right. That is
why the board can be trusted even though nothing guards it in the moment.

A parent's status rolls up from its children. An Epic sits at the least-advanced status among its Tasks,
reaching review once they all have and awaiting release once they all merge, and it archives when they all
close. Initiatives follow their Epics the same way, so a parent is always an honest summary of what is
under it.

One rule stands above the rest, and a required check guards it: an Epic lands whole, or not at all. The
merge-gate holds a member until its whole set is ready, and a freeze stops the survivors if a landing goes
half-way. Neither ever reverts a merge — the board's answer to trouble is to stop, hold, and hand it to a
person, never to rewrite history on its own.

When it does need a person, it says so out loud. A stuck hotfix, a check that never finished, an Epic that
cannot complete: each raises an escalation ticket on the board and, for the worst of them, a direct page. A
separate watchdog watches the safety systems themselves, so if a sweep stops running, someone hears about
it.

And if any of it misbehaves, one switch stops everything. The kill switch holds every Pull Request and
freezes every board write at once. It fails safe: anything but a clear "off" value halts the automation, so
a mistyped value stops the board rather than letting it run loose. The emergency hotfix path stays open
through all of it, because the stop must never block the fix.
