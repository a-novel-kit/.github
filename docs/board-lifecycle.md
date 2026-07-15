## Board lifecycle and feature planning

This guide walks you through our planning process, from a business feature to concrete code to a
shipped release.

As a contributor to `a-novel` and `a-novel-kit`, you turn ideas into features and grow the application.
Work is tracked in each organization's board ([a-novel's](https://github.com/orgs/a-novel/projects/7) and
[a-novel-kit's](https://github.com/orgs/a-novel-kit/projects/1)). Those boards, combined with agentic skills, take on
the bookkeeping, so you can focus on the two things that really matter: the intention and the shipment. Everything else
can be tracked for you.

Work starts with **the intention**: the non-technical specification behind your idea. Before coding anything, the first
step is to translate this specification into an issue (the GitHub layer that will later interface with
concrete code). You refine the scope, look up the resources you need, and track your progress there. That
specification is human-gated: it's the initial input the whole process will be based on. Taking the time to
validate and refine it is what makes the [whole planning process](#planning-an-issue) smooth and
well-scoped. Once it holds, the
GitHub issue becomes a Task, Epic, or Initiative, depending on its size.

Once your specification is ready, the **implementation phase** begins. Most of the time, this translates to opening a
Pull Request (a branch that holds a working copy of the code). Once a Task is tied to a Pull Request, it enters
[the implementation cycle](#the-development-cycle) and moves to "In Progress". This step can be picked up by an agent,
or by you. During this phase, Pull Requests should remain in draft status. Once the development is completed, the Task
moves to the second human-gated step: the review process.

"In Review" status is automatically assigned once you mark a Pull Request as ready. It will request a code maintainer
to review your code, ask for changes, and in the end approve it. This is the final development step. Once done, your
Pull Request merges to master and enters the "Awaiting Release" status.

Merging does not mean the code has reached production yet: it first sits in a pre-production environment (staging).
There, a release team becomes responsible for cutting a release from a publication plan. This cut is what actually gets
shipped into production: versioned, stable code.

On the board, the states a feature passes through are a row of columns. Work crosses them left to right, and every issue
sits as a card in the column for its state:

```
  Triage       Ready        In progress  In Review    Awaiting Release
  ┌───────┐    ┌───────┐    ┌───────┐    ┌───────┐    ┌───────┐
  │ Task  │    │ Task  │    │ Task  │    │ Task  │    │ Task  │
  └───────┘    └───────┘    └───────┘    └───────┘    └───────┘
   ◆ you       └ board moves them ┘      ◆ you        ◆ you
```

Those three ◆ gates are the spec, the review, and the release.

### What deserves your attention

Execution is the part you can delegate; an agent can even write the code. Comprehension and judgment are
the part you cannot, and three stages are made of nothing else:

- 🎯 **The spec** is the whole foundation, so make it sharp. A vague one builds the wrong thing, and no
  amount of clean code redeems a wrong target.
- 🔍 **The review** is where you come to understand what was built, so read the code until you do. Rushed,
  it lets bugs and weak structure through, and every later change builds on what you let stand.
- 🚀 **The release** is a deliberate choice of when, so ship only when the work is ready. Ship at the wrong
  time and even good work lands badly.

The board catches none of this, and neither does an agent; only a person who understands the work can. You
can hand off the typing, never the understanding. The spec is yours; the review and the release are a
maintainer's. At whichever stage is yours, comprehension and judgment are the whole job, so spend your
attention there and trust the board with the rest.

### Shaping the work

Part of the specification phase boils down to correctly sizing and dispatching the feature you work on.
A **feature** is the intention itself, the idea you set out to deliver; on the board it becomes one or more
**issues**, each with a **type** that marks its size:

| Type           | What it is                                                                                            |
| -------------- | ----------------------------------------------------------------------------------------------------- |
| **Task**       | One branch of work, about one Pull Request; the leaf you build.                                       |
| **Epic**       | Several Tasks that must land together, across the repositories they touch; the unit that lands whole. |
| **Initiative** | Several Epics, for an effort that spans many releases.                                                |

A feature enters as a single issue, and its size sets the top: small enough, that top is one Task; larger,
an Epic or an Initiative. You build downward from there, one level at a time. Each issue carries its own
intention, validated at its own [gate](#planning-an-issue) before the level beneath it is written, so an
Initiative is settled before its Epics, and an Epic before its Tasks. Only a Task is ever implemented. An Epic and an
Initiative do no work of their own but coordinate the level below, holding each child to their direction so it serves
the broader goal and stays on topic.

#### Initiatives and Milestones

An **Initiative** is too large to build directly, so no Pull Request ever links to it. Planned like any
issue, it skips the implementation statuses and sits in a dedicated **Tracking** status while its Epics are
worked, reaching **Awaiting release** once all its Epics have merged.

A **Milestone** is a separate GitHub construct, a named grouping with a due date and
no spec of its own, that collects the issues aimed at one outcome; the Roadmap view
([a-novel](https://github.com/orgs/a-novel/projects/7/views/4),
[a-novel-kit](https://github.com/orgs/a-novel-kit/projects/1/views/3)) groups the board by it. For now, a Milestone is
tied to a single repository, so a goal that spans several needs the same-named Milestone recreated in each.

#### Meta tasks

Some work writes no code at all: creating a repository, changing a setting, or granting a permission. That is
a **meta task**. It carries a `meta` label, skips the code lifecycle, and runs through the Backlog → Triage → Ready →
**Applied** statuses. Keep meta tasks in their own Epic, never mixed with code Tasks.

#### Working with issues

Splitting issues comes down to one rule: **two Tasks share an Epic only if they can land
concurrently**, merged to master and shipped together without downtime. One Task is one branch and one Pull
Request, so two pieces of work are always two Tasks, in the same repository or not. If they can land at
once, one Epic holds them. If
[one must ship before the other can, they need separate Epics](#ordering-across-repositories), tied together by
an Initiative.

Your code lives in a Pull Request, but the board follows the issue behind it, so the two must be tied
together. You tie them in the Pull Request's description, with a line naming the issue by its number:

```
<summary>

Closes #123
```

`#123` is the issue's number, which GitHub shows next to its title. When the Pull Request merges, GitHub
closes that issue and records that this code delivered it, and that record is the signal the board watches
to move the issue forward.

A plain `#123` only finds an issue in the Pull Request's own repository. When the issue lives in another
one, such as a planning issue in `.github`, you give its full address instead: `Closes a-novel-kit/.github#123`,
which names the organization, the repository, then the number. A plain `#123` aimed across repositories
matches nothing: the link never forms, and the issue sits still on the board. The whole lifecycle turns on
this link, so it is worth getting right.

Only Tasks live in the repositories they touch, next to the code; every issue above them, Epics and
Initiatives alike, lives in the organization's `.github` repository. An Epic there adopts its Tasks as
children across those repositories, which is how a feature spanning five repositories still rolls up to one
place. A dependency in the _other_ organization cannot be a child that way, so it is tracked as a plain
link instead.

### Naming an issue

A title says what the issue **delivers**, and the type decides how broad that is. The larger the type, the
broader the outcome it names: an Initiative names a whole area of work, a Task a single change. A title
never names how the work is built or which version ships it; the mechanism and the version are attributes
it carries, not what it is.

Take one feature, the notifications work, and watch its title tighten as the type narrows:

- An **Initiative** names the area of effort: ✅ **Contributor notifications**.
  - ❌ _Q3 notifications_: a calendar box the effort outlasts.
  - ❌ _Discord notifications for GitHub activity_: too narrow, that is one Epic inside the area.
- An **Epic** names the goal it delivers: ✅ **Discord notifications for GitHub activity**.
  - ❌ _v2.3.0_: the version it ships in, not the goal it reaches.
  - ❌ _Notifications_: too broad, that is the whole Initiative.
- A **Task** names the single change: ✅ **Resolve a GitHub user id to a Discord id**.
  - ❌ _Various notification fixes_: vague and plural, where a Task is one branch.
  - ❌ _Edit `notify.ts`_: the file it touches, not the change it makes.
- A **Bug** names the broken behavior: ✅ **Discord pings fire twice on a re-run**.
  - ❌ _Notifications broken_: names neither what breaks nor how.
  - ❌ _Bug in `notify.ts`_: a guess at where it lives, not what the reader sees.

### Planning an issue

This is the first human gate, and every issue clears it before anything follows from it: the code of a
Task, the child issues of an Epic or Initiative. What it produces is a specification good enough to build
from, or to break down, without guessing.

**Write it in product terms, not code.** Say what the feature does and why, the scope it covers and the
scope it leaves out, and the resources it leans on. Leave the how to whoever implements it: a spec that
pins down the code goes stale as the real work drifts from it, and it leaves the implementer no room to
find a better way.

**Size it and rank it.** Give the issue a Size, for how much work it is, and a Priority, for how soon it
matters, so the board can order what comes next. If the Size outgrows a single branch, the issue is an Epic or an
Initiative, not a Task: split it, and plan each child in turn.

**Then get it accepted.** A groomed specification waits in Triage until someone accepts it into Ready and
opens it for work. That acceptance is the gate: everything downstream rests on what you settled here, so
spend the time now rather than in review.

### The development cycle

This one is for when you write the code yourself; an agent that picks up a Task already works this way. It
is the general shape only, the same in every repository, and each repository's own `CONTRIBUTING.md` fills
in the language, the stack, and the tools. In outline: learn the local rules, build in small tested steps,
and open the result for review once it holds together.

**Read the local guidelines first.** Every repository carries its own conventions, and they outrank any
general habit. Read them before writing a line, so your code lands in the shape the reviewers expect.

**Break the work into small steps.** A large change is easier to write and far easier to review when you
build it as a sequence of small pieces that each stand on their own, rather than one sprawling diff.

**Test as you go, not at the end.** Write the tests alongside the code and keep them passing as you work.
Untested code is not finished code, and a test written afterward tends to check what the code does rather
than what it should.

**Keep the Pull Request in draft while you work.** A draft holds the Task at In progress and asks no one to
look yet. Once the code is written, tested, and passing, mark the Pull Request ready: that is what moves
the Task to In Review and calls in a maintainer.

**Green CI is the price of review.** Every push runs the checks; clear what they flag before you ask for
eyes. A reviewer reads working code, not a broken pipeline.

### Why an Epic lands whole

An Epic's Tasks make one **atomic landing**: they merge together, or not at all. They are the independent
parts of one feature, so the rule keeps master from ever carrying half of one: a client with no endpoint to
reach, or an endpoint no client calls. That is worth understanding because it shapes how you plan.

The board holds an Epic together with a required check, the **merge-gate**. A Pull Request joins an Epic by
carrying an `epic:<N>` label that only a maintainer can add, so membership is trusted rather than guessed
from a `Closes` line. The gate holds every member until all are approved, then approves them at once, letting
the merge queue land them together. Because each Task stands on its own, the brief window while they land
still builds.

When a member lands and its siblings do not, the board sees the **partial landing** and
[freezes the rest](#when-the-board-needs-you), so nothing else merges until the Epic is whole again. The
fix is almost always to roll forward and get the stuck sibling in. Reverting what is already merged is a last
resort, run by hand, because undoing a merge is far more dangerous than finishing one.

### Ordering across repositories

Not every feature can land whole. When a piece in one repository needs a piece in another, the two cannot
share an Epic, because a repository sees another's work only once it is **published**, not merely merged: a
service can only use a released version of a library, so the library's new function must ship before the
service can call it. Releases go out one at a time, so cross-repo work comes together in a sequence of
steps, never all at once.

A breaking change is the clearest case, and it is never one Epic. It is two. An **expand** Epic adds the
new path beside the old and ships it, so both work at once. Then a **contract** Epic removes the old path,
once the expanded release is out and nothing still needs it. Each Epic lands whole on its own; between them,
any order is safe.

An Initiative or Milestone plans these ordered Epics into numbered **Stages**. A Stage's Epics land
together, and the next Stage waits for the earlier to ship, so its Epics can build on a published version.
It is the atomic-landing rule one level up: Epics that can land concurrently share a Stage, and one that
must wait falls to a later Stage. Unrelated Epics share no Stage, so a release may ship several at once. For
now the numbers are set by hand, once and for good, and no automation yet reads them.

### Shipping

Merging is not shipping. Master is staging; a release is what reaches production, and cutting one is always
a deliberate, human call.

Each repository ships on its own, as a [`vX.Y.Z` tag](taxonomy.md#versioning-and-releases), and a release
publishes everything on that repository's master at that moment. You cut it from CI, never from your
machine, by running the release workflow and choosing the size of the bump. Only an admin can, through a
protected gate, and there is no local release command on purpose.

One repository ships with one release. A cross-repo Epic ships with a **release train**: a single dispatch
that runs each repository's own release, in any order, and is safe to re-run to finish any that did not go.

A published version is permanent; a tag can never be taken back. So recovery is almost always to roll
forward with a new release that supersedes the bad one, never to rewrite what shipped.

### Bugs and hotfixes

A **Bug** is the leaf for a defect: built and tracked like a Task, on the same lifecycle and the same
gates, with its own type, so its nature is plain. Caught before it ships, it rides the ordinary cycle like
any other Task.

Once a version is in production, a bug cannot wait for that cycle. You **hotfix** it: run the repository's
hotfix workflow with the release line and the fix, and it cuts a new patch straight off the shipped
version, then opens a Pull Request to carry the same fix back to master. Merge that backport, or the next
release off that line ships without the fix and brings the bug back.

A bug that spans repositories is no special case. It is an ordinary Epic, run fast.

### When the board needs you

Most days the board runs itself, and you barely notice it. It heals its own drift within minutes and moves
your work along as you go. Every so often, though, it hands something back to you. These are the moments
worth knowing on sight.

**A Pull Request will not merge, and its gate is red.** It is an Epic member, waiting for its siblings. An
Epic lands whole, so a member holds until every other Task in the Epic is ready too. Get the rest approved,
and the gate greens them together. There is nothing to fix on the Pull Request itself.

**A Pull Request is frozen.** Part of an Epic reached master and part did not, so the board froze the rest
to keep master whole. If the missing piece can still merge, approve it and let it in. If a sibling was
abandoned instead, the set can never be whole, so the board holds and waits for you rather than guessing
which survivors still form the feature. And if the landed pieces have to come back out, a maintainer runs a
rollback, which reverts them in order through the same review and gate. Reverting shipped code is serious,
so it asks for a typed confirmation.

**A ticket appears in the Triage view ([a-novel](https://github.com/orgs/a-novel/projects/7/views/3),
[a-novel-kit](https://github.com/orgs/a-novel-kit/projects/1/views/2)), tagged `escalation`.** The board
opened it because
something is stuck and needs a person: a hotfix that stalled, a check that never finished, or an Epic that
cannot complete. Fix the underlying thing and the ticket closes itself. It is a signal, not work to
plan, so do not size or groom it. A pile of open escalation tickets is itself the alarm.

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

**A version in production has a bug.** [Hotfix it](#bugs-and-hotfixes): patch the shipped version directly
and backport the fix to master, so the next release off that line does not bring the bug back.

### How the board keeps itself in step

You will rarely need this. But when the board does something you did not expect, it helps to know how it
works underneath. The whole system is built from GitHub's own parts (required checks, the merge queue, and
scheduled jobs) and driven by a single actor, the board's bot.

Every status is written by that one bot, always from what happened to a Pull Request: a draft is in
progress, an open one is in review, an approved one is done, and a merged one is awaiting release. Because the
status is read from what happened and never typed in, it cannot drift, and a manual edit will not stick.
There is one carve-out: a meta task has no Pull Request to read from, so its final Applied status is set by
hand.

GitHub raises no event when a Status field changes, so the board is never driven by its own state; it is
always derived from the Pull Requests and issues underneath. That derivation runs the moment something
happens, and again on a sweep every fifteen minutes that re-checks the whole board against live GitHub
truth. A dropped event, a missed webhook, or a stray hand edit: the next sweep quietly puts it right. That is
why the board can be trusted even though nothing guards it at the moment.

A parent's status rolls up from its children. An Epic sits at the least-advanced status among its Tasks,
reaching review once they all have and awaiting release once they all merge. Initiatives follow their
Epics the same way, so a parent is always an honest summary of what is under it.

Archiving keeps the board clean. A finished item leaves the active views: an issue once it closes, a parent
once all its children have, and a meta task once it reaches **Applied**. The board shows only live work.

The board's answer to trouble is always the same: stop, hold, and hand it to a person, never rewrite what
happened. The [merge-gate and freeze](#why-an-epic-lands-whole) that keep an Epic whole are the clearest
case, holding a half-landed set for a person to finish rather than reverting a merge.

When it does need a person, it says so out loud. A stuck hotfix, a check that never finished, or an Epic that
cannot complete: each raises an escalation ticket on the board and, for the worst of them, a direct page. A
separate watchdog watches the safety systems themselves, so if a sweep stops running, someone hears about
it.

And if any of them misbehaves, one switch stops everything. The kill switch holds every Pull Request and
freezes every board writing at once. It fails safe: anything but a clear "off" value halts the automation, so
a mistyped value stops the board rather than letting it run loose. The emergency hotfix path stays open
through all of it, because the stop must never block the fix.
