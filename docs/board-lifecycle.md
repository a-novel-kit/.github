## Board lifecycle and feature planning

This guide walks you through our planning process, from a business feature to concrete code to a
shipped release.

As a contributor to `a-novel` and `a-novel-kit`, you turn ideas into features and grow the application.
Work is tracked in each organization's board ([a-novel's](https://github.com/orgs/a-novel/projects/7) and
[a-novel-kit's](https://github.com/orgs/a-novel-kit/projects/1)). Those boards, combined with agentic skills, take on
the bookkeeping, so you can focus on the two things that really matter: the intention and the shipment. They track
everything in between for you.

It starts with **the intention**: the non-technical specification behind your idea. Before coding anything, the first
step is to translate this specification into an issue (the GitHub layer that will later interface with
concrete code). You refine the scope, look up the resources you need, and track your progress there. That
specification is human-gated: it's the initial input the whole process will be based on. Taking time
validating and refining it is what makes the [whole planning process](#planning-a-task) smooth and
well-scoped. Once it holds, the
GitHub issue becomes a Task, Epic, or Initiative, depending on its size.

Once your specification is ready comes the implementation phase. Most of the time, this translates to opening a Pull
Request (a branch that holds a working copy of the code). Once a Task is tied to a Pull Request, it enters
[the implementation cycle](#the-development-cycle) and moves "In Progress". This step can be picked up by an agent, or by you. During this
phase, Pull Requests should remain in draft status. Once the development is completed, the Task moves to the second
human-gated step: the review process.

"In Review" status is automatically assigned once you mark a Pull Request as ready. It will request a code maintainer
to review your code, ask for changes, and in the end approve it. This is the final development step. Once done, your
Pull Request merges to master and enters the "Awaiting Release" status.

A merged code does not mean it has reached production yet: it seats ahead on a pre-production environment (the staging).
There, a release team becomes responsible for cutting a release from a publication plan. This cut is what actually gets
shipped into production: a versioned, stable code.

On the board, that same path is a row of states:

```
  ◆ Triage  →  [ Ready → In progress ]  →  ◆ In Review  →  [ Done ]  →  ◆ Awaiting Release  →  shipped

  ◆ waits for you    ·    [ … ] the board moves it on its own
```

### What deserves your attention

Execution is the part you can delegate; an agent can even write the code. Comprehension and judgment are
the part you cannot, and three stages are made of nothing else:

- **The spec** is the whole foundation, so make it sharp. A vague one builds the wrong thing, and no
  amount of clean code redeems a wrong target.
- **The review** is where you come to understand what was built, so read the code until you do. Rushed,
  it lets bugs and weak structure through, and every later change builds on what you let stand.
- **The release** is a deliberate choice of when, so ship only when the work is ready. Ship at the wrong
  time and even good work lands badly.

The board catches none of this, and neither does an agent; only a person who understands the work can. You
can hand off the typing, never the understanding. The spec is yours; the review and the release are a
maintainer's. At whichever stage is yours, comprehension and judgment are the whole job, so spend them
there and trust the board with the rest.

One rule sits above all of it: an Epic lands whole. Plan its pieces so they can land together, and keep
its siblings moving in step. A Task left behind holds back everything it is bound to.

### Shaping the work

The journey followed a single Task. Most features are larger than one Task, and the board gives them a
shape.

Every item on the board is a GitHub **issue**, and each issue has a **type**. The types nest, from the
single branch you write up to large-scale efforts that can need coordination or deeper planning:

| Type           | What it is                                                                                               |
| -------------- | -------------------------------------------------------------------------------------------------------- |
| **Task**       | One branch of work, about one Pull Request; the leaf you build. A **Bug** is the same, for a defect.     |
| **Feature**    | A few Tasks making one capability, inside a single repository.                                           |
| **Epic**       | One feature's Tasks across every repository it touches. The unit that ships: about one Epic per release. |
| **Initiative** | An umbrella over several Epics, for an effort spanning many releases.                                    |

You open each item at the level its work sits, and the larger ones come first: the Epic before its Tasks,
the Initiative before its Epics, so every piece has a parent from the start. An **Initiative** does no
work of its own, sitting in **Tracking** until its Epics are done. An **Epic** is named for the goal it
delivers, never for the version it will become; the version is its target, not its name.

A **Milestone** is not one of these. It is a separate GitHub feature, a named grouping with a due date and
no spec of its own, that collects the issues aimed at one outcome; the Roadmap view
([a-novel](https://github.com/orgs/a-novel/projects/7/views/4),
[a-novel-kit](https://github.com/orgs/a-novel-kit/projects/1/views/3)) groups the board by it. A Milestone
lives in one repository, so a cross-repo goal uses one of the same name in each.

Some work writes no code at all: creating a repository, changing a setting, granting a permission. That is
a **meta task**. It carries a `meta` label, skips the code lifecycle, and runs Backlog → Triage → Ready →
**Applied** before it archives itself. Keep meta tasks in their own Epic, never mixed with code Tasks.

Choosing between the types comes down to one rule: **work that cannot merge at the same time belongs to
different Epics.** One Task is one branch; two pieces that touch the same repository are one Task or two
stages, not two Tasks side by side. A breaking change across repositories is never one Epic either (the
next section explains why). And a whole effort spanning several releases is an Initiative with one Epic per
release, not a single Epic carrying them all.

Where an issue lives is what links it to your code. A `Closes` line in your Pull Request ties the two
together: `Closes #123` when the issue sits in the same repository, or the full
`Closes a-novel-kit/.github#123` when it lives in another one, like a planning issue in `.github`. A bare
`#123` aimed at another repository links nothing, so the board never advances. That link is what the whole
lifecycle keys off, so it is worth getting right.

An Epic lives in the organization's `.github` repository and adopts its Tasks as children across
repositories, which is how a feature spanning five repositories still rolls up to one place. A dependency
in the _other_ organization cannot be a child that way, so it is tracked as a plain link instead.

A parent's status is a summary of its children. An Epic sits at the least-advanced status among its
Tasks:
it reaches review only once every Task has, and awaiting release only once every Task has merged. When the
last child closes, the parent archives itself, and the same holds from Epics up to their Initiative.

### Planning a task

This is the first human gate, and it comes before any code. What it produces is a specification good
enough to build from without guessing.

**Write it in product terms, not code.** Say what the feature does and why, the scope it covers and the
scope it leaves out, and the resources it leans on. Leave the how to whoever implements it; a spec that
dictates code ages badly and boxes them in.

**Size it and rank it.** Give the issue a Size, for how much work it is, and a Priority, for how soon it
matters, so the board can order what comes next. A Task too large to size is really an Epic, so split it.

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

**Break the work into small steps.** A large change is easier to write, and far easier to review, when you
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

An Epic's Tasks merge together or not at all. That rule shapes how you plan, so it is worth understanding
why it exists.

A feature often spans repositories. A service needs a new function from a library; a client needs a new
field from a schema. Merge the service before the library and the service no longer builds. Preventing
that broken state is the whole point of the rule.

The obvious fix would be to merge every piece at the same instant, but GitHub cannot: it merges one Pull
Request at a time, one repository at a time, with no atomic merge across repositories. So "lands whole"
cannot mean "at the same instant." It means something you can actually guarantee: **whatever order the
Tasks land in, and however many land before the rest, every repository still builds.**

You earn that by keeping every step backward-compatible, which is why a breaking change across
repositories is never one Epic. It is two. An **expand** Epic adds the new path beside the old and ships
it, so both work at once. Then a **contract** Epic removes the old path, once the expand release is out and
nothing still needs it. Between them, any landing order is safe.

The board holds an Epic together with a required check, the **merge-gate**. A Pull Request joins an Epic by
carrying an `epic:<N>` label that only a maintainer can add, so membership is trusted rather than guessed
from a `Closes` line. The gate holds every member until all are approved, then greens them at once, and
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

Inside one Epic, the Tasks are a **convoy**: they land together, in any order, because each builds on its
own, and one release train ships them all. Across a dependency, releases go in **stages** instead: the
dependency ships first, and the code that needs it follows once it can point at the published version.

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
abandoned instead, the set can never be whole, so the board holds and waits for you rather than guessing
which survivors still form the feature. And if the landed pieces have to come back out, a maintainer runs a
rollback, which reverts them in order through the same review and gate. Reverting shipped code is serious,
so it asks for a typed confirmation.

**A ticket appears in the Triage view ([a-novel](https://github.com/orgs/a-novel/projects/7/views/3),
[a-novel-kit](https://github.com/orgs/a-novel-kit/projects/1/views/2)), tagged `escalation`.** The board
opened it because
something is stuck and needs a person: a hotfix that stalled, a check that never finished, an Epic that
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

**A version in production has a bug.** Patch it with a hotfix: you run the repository's hotfix workflow
with the release line and the fix, and it cuts a new patch straight off the shipped version, then opens a
Pull Request to carry the fix back to master. Merge that one. Until it lands, the next release off that
line could bring the bug back. A cross-repo bug is not a special case; it is an ordinary Epic, run fast.

### How the board keeps itself in step

You will rarely need this. But when the board does something you did not expect, it helps to know how it
works underneath. The whole system is built from GitHub's own parts (required checks, the merge queue,
scheduled jobs) and driven by a single actor, the board's bot.

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
half-way. Neither ever reverts a merge. The board's answer to trouble is to stop, hold, and hand it to a person,
never to rewrite history on its own.

When it does need a person, it says so out loud. A stuck hotfix, a check that never finished, an Epic that
cannot complete: each raises an escalation ticket on the board and, for the worst of them, a direct page. A
separate watchdog watches the safety systems themselves, so if a sweep stops running, someone hears about
it.

And if any of it misbehaves, one switch stops everything. The kill switch holds every Pull Request and
freezes every board write at once. It fails safe: anything but a clear "off" value halts the automation, so
a mistyped value stops the board rather than letting it run loose. The emergency hotfix path stays open
through all of it, because the stop must never block the fix.
