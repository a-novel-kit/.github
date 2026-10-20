# Board lifecycle and feature planning

This document is about how a feature travels from an idea to code to a shipped release.

As a contributor to `a-novel` and `a-novel-kit`, you turn ideas into features and grow the application.
Each organization tracks its work on a board ([a-novel's](https://github.com/orgs/a-novel/projects/7) and
[a-novel-kit's](https://github.com/orgs/a-novel-kit/projects/1)). The boards and the agent skills take on
the bookkeeping, so you can focus on the two things that matter: the intention and the shipment.

Work starts with **the intention**, the non-technical specification behind your idea. Before any code,
you write that specification into a GitHub issue. There you refine the scope, gather the resources you
need and track your progress. The specification is the first human gate, because everything after it
builds on it. Taking the time to refine it makes the [whole planning process](#planning-an-issue) smooth
and well scoped. Once it holds, the issue becomes a Task, an Epic or an Initiative, depending on its
size.

Then comes the **implementation**. Most of the time, that means opening a Pull Request, a branch that
holds a working copy of the code. Once a Pull Request is tied to a Task, the Task enters
[the development cycle](#the-development-cycle) and moves to In progress. You or an agent can write the
code. The Pull Request stays a draft while the work goes on.

Marking the Pull Request ready moves the Task to In review, the second human gate. A maintainer reviews
the code, asks for changes and finally approves it. The Pull Request then merges to master, and the Task
moves to Awaiting release.

Merged code has not reached production yet. It waits on master, the branch every release is cut from.
Cutting the release is the third human gate. The release is what ships to production: versioned,
stable code.

On the board, each state is a column. Every issue is a card in the column of its state, and work moves
from left to right:

```
  Triage       Ready        In progress  In review    Awaiting release
  ┌───────┐    ┌───────┐    ┌───────┐    ┌───────┐    ┌───────┐
  │ Task  │    │ Task  │    │ Task  │    │ Task  │    │ Task  │
  └───────┘    └───────┘    └───────┘    └───────┘    └───────┘
   ◆ spec      └ board moves them ┘      ◆ review     ◆ release
```

Each ◆ marks a human gate.

## What deserves your attention

You can delegate execution, and an agent can even write the code. You cannot delegate comprehension and
judgment, and the three gates are made of nothing else:

- 🎯 **Make the spec sharp.** It is the whole foundation. A vague spec builds the wrong thing, and clean
  code never redeems a wrong target.
- 🔍 **Read the code until you understand it.** The review is where you learn what was built. A rushed
  review lets bugs and weak structure through, and every later change builds on them.
- 🚀 **Ship only when the work is ready.** The release is a deliberate choice of when. Good work shipped
  at the wrong time still lands badly.

Neither the board nor an agent catches any of this. Only a person who understands the work can. The spec
is yours, and the review and the release are a maintainer's. Spend your attention at your gate, and trust
the board with the rest.

## Shaping the work

Part of the specification is sizing the feature and splitting it. A **feature** is the intention
itself, the idea you set out to deliver. On the board it becomes one or more **issues**, and each issue's
**type** marks its size:

| Type           | What it is                                                                                            |
| -------------- | ----------------------------------------------------------------------------------------------------- |
| **Task**       | One branch of work, about one Pull Request; the leaf you build.                                       |
| **Epic**       | Several Tasks that must land together, across the repositories they touch; the unit that lands whole. |
| **Initiative** | Several Epics, for an effort that spans many releases.                                                |

A feature enters as a single issue, and its size sets the top level. A small feature is one Task, and a
larger one is an Epic or an Initiative. You build downward from there, one level at a time. Each issue
carries its own intention and clears its own [gate](#planning-an-issue) before the level beneath it is
written. An Initiative is settled before its Epics, and an Epic before its Tasks.

Only a Task is ever implemented. An Epic or an Initiative does no work of its own. It coordinates the
level below and keeps each child on the broader goal.

### Initiatives and Milestones

An **Initiative** is too large to build directly, so no Pull Request ever links to it. You plan it like
any issue. It then skips the implementation statuses and sits in a dedicated **Tracking** status while
its Epics are worked. It reaches **Awaiting release** once all its Epics have merged.

A **Milestone** is a separate GitHub construct. It is a named grouping with a due date and no spec of its
own, and it collects the issues aimed at one outcome. The Roadmap view
([a-novel](https://github.com/orgs/a-novel/projects/7/views/4),
[a-novel-kit](https://github.com/orgs/a-novel-kit/projects/1/views/3)) groups the board by Milestone. For
now, a Milestone belongs to a single repository, so a goal that spans several repositories needs a
Milestone of the same name in each.

### Meta tasks

Some work writes no code, such as creating a repository, changing a setting or granting a permission.
That work is a **meta task**. It carries a `meta` label and skips the code lifecycle: once the change is
made, you move it from Ready to **Applied** by hand. Keep meta tasks in their own Epic, apart from code
Tasks.

### Working with issues

One rule splits the work: **two Tasks share an Epic only if they can land concurrently**, merged to
master and shipped together without downtime. One Task is one branch and one Pull Request, so two pieces
of work are always two Tasks, in the same repository or not. If they can land at once, one Epic holds
them. If [one must ship before the other can, they need separate Epics](#ordering-across-repositories),
tied together by an Initiative.

Your code lives in a Pull Request, and the board follows the issue behind it, so tie the two together.
Name the issue in the Pull Request's description, by its number:

```
<summary>

Closes #123
```

`#123` is the number GitHub shows next to the issue's title. When the Pull Request merges, GitHub closes
the issue and records that this code delivered it. The board watches that record to move the issue
forward.

A plain `#123` only finds an issue in the Pull Request's own repository. An issue in another
repository, such as a planning issue in `.github`, takes its full address, with the organization, the
repository and the number: `Closes a-novel-kit/.github#123`. Without it, the link never forms and the
issue stays still on the board. The whole lifecycle turns on this link.

Tasks live in the repositories they touch, next to the code. An Epic or an Initiative that spans
repositories lives in the organization's `.github` repository. An Epic there adopts its Tasks as
children across repositories, so a feature that touches five repositories still rolls up to one place. A
dependency in the _other_ organization cannot be a child, so the Epic tracks it as a plain link.

## Naming an issue

A title says what the issue **delivers**, and the type sets how broad that is. An Initiative names a
whole area of work, and a Task a single change. A title never names how the work is built or which
version ships it, since those are attributes of the work.

Follow one feature, the notifications work, as its title tightens with the type:

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

## Planning an issue

Planning is the first human gate, and every issue clears it before anything follows from it: the code of
a Task, or the children of an Epic or an Initiative. It produces a specification good enough to build
from, or to break down, without guessing.

**Write it in product terms.** Say what the feature does and why, the scope it covers and leaves out,
and the resources it relies on. Leave the how to whoever implements it. A spec that pins down the code
goes stale as the work drifts, and it leaves the implementer no room to find a better way.

**Size it and rank it.** Give the issue a Size, for how much work it is, and a Priority, for how soon it
matters. The board orders what comes next from both. If the Size outgrows a single branch, the issue is
an Epic or an Initiative. Split it, and plan each child in turn.

**Then get it accepted.** A groomed specification waits in Triage until someone accepts it into Ready
and opens it for work. That acceptance is the gate. Everything downstream rests on what you settle here,
so spend the time now rather than in review. Accepted work that must wait its turn, such as a later
stage, sits in Backlog until then.

## The development cycle

This section is for when you write the code yourself. An agent that picks up a Task already works this
way. The shape is the same in every repository, and each repository's own `CONTRIBUTING.md` adds the
language, the stack and the tools.

**Read the local guidelines first.** Every repository has its own conventions, and they outrank any
general habit. Read them before writing a line, so your code lands in the shape reviewers expect.

**Break the work into small steps.** Build a large change as a sequence of small pieces that each stand
on their own. Small pieces are easier to write and far easier to review.

**Test as you go.** Write the tests alongside the code and keep them passing as you work. Untested code
is not finished. A test written afterward tends to check what the code does instead of what it should do.

**Keep the Pull Request in draft while you work.** A draft holds the Task at In progress and asks no one
to look yet. Once the code is written, tested and passing, mark the Pull Request ready. That moves the
Task to In review and calls in a maintainer.

**Get CI green before review.** Every push runs the checks. Clear what they flag before you ask for a
review, so the reviewer reads working code.

## Why an Epic lands whole

An Epic's Tasks make one **atomic landing**: they merge together, or not at all. They are the parts of
one feature, so master never carries half of one, such as a client with no endpoint or an endpoint no
client calls. This rule shapes how you plan.

A required check, the **merge-gate**, holds an Epic together. A Pull Request joins an Epic through an
`epic:<N>` label. The gate holds every member until all of them are ready and approved, then passes them
at once, and the merge queue lands them together. Each Task stands on its own, so master still builds
during the brief window while they land.

When one member lands and its siblings do not, the board sees a **partial landing** and
[freezes the rest](#when-the-board-needs-you) of the Epic. The fix is almost always to roll forward and get the stuck
sibling in. Reverting what already merged is a last resort, run by hand, because undoing a merge is far
more dangerous than finishing one.

## Ordering across repositories

Not every feature can land whole. A repository sees another's work only once it is **published**, not
merely merged. A service can only use a released version of a library, so the library's new function
must ship before the service can call it. Two such pieces cannot share an Epic, so dependent work ships
as a sequence of releases.

A breaking change is the clearest case, and it always takes two Epics. An **expand** Epic adds the new
path beside the old one and ships it, so both work at once. A **contract** Epic then removes the old
path, once the expanded release is out and nothing needs the old path anymore. Each Epic lands whole on
its own, and any order is safe between them.

An Initiative or a Milestone plans these ordered Epics into numbered **Stages**. A Stage's Epics land
together, and the next Stage waits for the earlier one to ship, so its Epics can build on a published
version. This is the atomic-landing rule one level up. Epics that can land concurrently share a Stage,
and one that must wait falls to a later Stage. Unrelated Epics share no Stage, so a release may ship
several at once. For now, the numbers are set by hand, once, and no automation reads them.

## Shipping

Merging is not shipping. Master holds merged work, and a release is what reaches production. Cutting
one is always a deliberate, human call.

Each repository ships on its own, as a [`vX.Y.Z` tag](taxonomy.md#versioning-and-releases). A release
publishes everything on that repository's master at that moment. You cut it from CI by running the
release workflow and choosing the size of the bump. The cut then waits at a protected gate until an admin
approves it, and no command releases from your machine.

One repository ships with one release. A cross-repository Epic ships with a **release train**, a single
dispatch that runs each repository's own release. The train runs them in any order, and re-running it
finishes any that did not go out.

A published version is permanent, and a tag never comes back. Recovery almost always rolls forward with
a new release that supersedes the bad one.

## Bugs and hotfixes

A **Bug** is the leaf for a defect. It is built and tracked like a Task, on the same lifecycle and the
same gates, with its own type to make its nature plain. A bug caught before it ships rides the ordinary
cycle like any other Task.

A bug in a version already in production cannot wait for the next release. You **hotfix** it. Fix it on
master first, through the ordinary cycle. An admin then runs the repository's hotfix workflow with the
release line, such as `1.4`, and the fix's commits. The workflow opens a backport Pull Request that
carries each fix onto the line's `release/v1.4` branch. Merging the backport cuts the next patch from
that line, behind the same release gate.

A bug that spans repositories is an ordinary Epic, run fast.

## When the board needs you

Most days the board runs itself. It heals its own drift within minutes and moves your work along as you
go. Every so often, it hands something back to you.

**A Pull Request will not merge, and its gate is red.** The Pull Request is an Epic member, waiting for
its siblings. An Epic lands whole, so each member holds until every other Task in the Epic is ready.
Get the rest approved, and the gate greens them together. The Pull Request itself needs no fix.

**A Pull Request is frozen.** Part of an Epic reached master and part did not, so the board froze the
rest to keep master whole. If the missing piece can still merge, approve it and let it in. If a sibling
was abandoned, the set can never be whole. The board then holds and waits for you, without guessing
which survivors still form the feature. If the landed pieces have to come back out, an admin runs a
rollback. The rollback reverts them in order, through the same review and gate, and asks for a typed
confirmation.

**A ticket tagged `escalation` appears in the Triage view
([a-novel](https://github.com/orgs/a-novel/projects/7/views/3),
[a-novel-kit](https://github.com/orgs/a-novel-kit/projects/1/views/2)).** The board opened it because a
hotfix is stuck: a backport it could not open, or a patch that failed to cut. Fix the underlying problem,
then close the ticket. The ticket is a signal, so do not size or groom it. A pile of open escalation
tickets is itself the alarm.

**The board looks out of date.** A [sweep](#how-the-board-keeps-itself-in-step) re-checks everything
every fifteen minutes and fixes any drift. To run it sooner, start the `reconcile-board` workflow of
the organization's `.github` repository:

```sh
org=a-novel-kit # or a-novel
gh workflow run reconcile-board.yaml --repo "$org/.github"
```

**A release stalled halfway.** A release train fans out to many repositories, and one of them can park
at its approval gate or fail after tagging. Re-run the train. It reads what actually shipped from the
live tags and re-cuts only the repositories still missing. A tag with no published release behind it is
flagged for a person.

**Everything has stopped.** The kill switch is on, and it halts every board automation at once during an
incident. While it is on, no Pull Request can merge, and the hotfix workflow keeps running so an admin
can still ship a patch. An org admin turns the switch off by setting the `AGENT_KILL_SWITCH`
organization variable to `off`, and the next sweep repairs whatever piled up.

**A version in production has a bug.** [Hotfix it](#bugs-and-hotfixes). Fix it on master, then backport
the fix to the release line.

## How the board keeps itself in step

This section explains the machinery, for when the board does something you did not expect. The system is
built from GitHub's own parts (required checks, the merge queue and scheduled jobs) and driven by a
single actor, the board's bot.

Once a Task has a Pull Request, the bot writes the Task's status from what happened to the Pull Request:

| Pull Request     | Task status      |
| ---------------- | ---------------- |
| Draft            | In progress      |
| Ready            | In review        |
| Approved         | Done             |
| Merged           | Awaiting release |
| Closed, unmerged | Ready            |

While the Pull Request is open, the bot rewrites the status on each of its events, so a manual edit does
not stick. Before a Pull Request exists, the bot has nothing to read, so the early statuses are yours. An
issue joins the board in Triage, and you move it to Ready at the spec gate. A meta task never gets a Pull
Request, so you set all its statuses by hand, up to Applied.

GitHub raises no event when a Status field changes. The board therefore never reacts to its own state.
It always derives the state from the Pull Requests and issues underneath. That derivation runs the moment
something happens, and again in a sweep every fifteen minutes that re-checks the whole board against
GitHub. The next sweep repairs a dropped event, a missed webhook or a stray hand edit. That repair is why
you can trust the board without anything guarding it.

A parent's status rolls up from its children in each sweep. An Epic moves to In progress as soon as one
of its Tasks does. It reaches In review once they all have, and Awaiting release once they all merge. An
Initiative holds [Tracking](#initiatives-and-milestones) until all its Epics merge. Either way,
a parent summarizes what is under it.

Archiving keeps the board clean. A finished item leaves the active views: an issue once it closes, a
parent once all its children have, and any item, such as a meta task, once it reaches **Applied**. The
board shows only live work.

The board answers trouble the same way every time. It stops, holds, and hands the problem to a person,
and it never rewrites what happened. The [merge-gate and the freeze](#why-an-epic-lands-whole) are the
clearest case. They hold a half-landed set for a person to finish instead of reverting a merge.

When the board needs a person, it says so. A hotfix that cannot be backported, or a patch that fails to
cut, raises an escalation ticket on the board. A rollback that cannot be produced opens a held draft for
a person to finish.

One switch stops everything if the automation misbehaves. The kill switch holds every Pull Request and
stops every status write at once. It fails safe: any value other than a clear "off" halts the
automation, so a typo stops the board instead of letting it run loose.
