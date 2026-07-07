# The board lifecycle

Every piece of work in `a-novel` and `a-novel-kit` is a GitHub issue on one shared **board**, and it
moves through the same lifecycle from idea to shipped. Most of that movement is automated: you decide
_what_ to build and _when_ to ship it, and the board keeps itself honest about _where each thing is_.

This is the map — the shape of the process first, then what you do, then how to react when something
looks wrong. Both organizations run the identical process on their own board; this doc is shared and
[`a-novel`](https://github.com/a-novel/.github/blob/master/CONTRIBUTING.md) links here.

## The big picture

Work is a **hierarchy**. An **Initiative** is a large goal, split into **Epics**; an **Epic** is a
cross-repo change, split into **Features** and **Tasks**; a **Bug** is an unplanned fix. Each is a
GitHub issue, and each carries a **Status** on the board.

The Status moves left to right, and shipped work leaves the board:

```
Backlog ──▶ Ready ──▶ In progress ──▶ In review ──▶ Awaiting release ──▶ archived
  plan      pick up     open a PR      request review    merge              release
```

The single idea that makes the board trustworthy: **its Status has one writer — the automation — and
it is _derived_ from what actually happened** (a PR opened, merged, released). You never set a Status
by hand; it can't drift, and hand-edits don't stick. So:

- **You** drive the two ends — you _plan_ an item to `Ready`, and you _decide to release_.
- **The automation** drives the middle and the exit — your PR opening, being approved, merging, and
  shipping each move the Status on their own, and shipped work is archived off the board.

An Epic's Status is the **floor of its children**: it reaches `In review` when _all_ its Tasks are in
review or beyond, `Awaiting release` when they've all merged, and archives when they've all shipped.
The same rolls up from Epics to Initiatives. So a parent is a live summary of its children, for free.

That is the whole process. The rest of this document is detail you only need when you're doing a
specific step, or when something surprises you.

## Vocabulary

| Term | Meaning |
| --- | --- |
| **Initiative / Epic / Feature / Task** | The work hierarchy, largest to smallest. Each is a GitHub issue. |
| **Bug** | An unplanned fix; lives on the board like a Task. |
| **`epic:<N>` label** | Marks a PR as a member of Epic _N_ — the binding that makes its landing atomic. |
| **Status** | The board field tracking where an item is. The single-writer value, derived from reality. |
| **merge-gate** | The required check that holds an Epic's PRs until the whole set is ready, then lets them land together. |
| **the saga** | The set of automations that land a multi-repo Epic atomically and release it. |
| **reconcile sweep** | A safety net that re-derives the whole board every ~15 minutes, catching anything a live event missed. |
| **release train** | One dispatch that releases every repo an Epic touched. |
| **kill-switch** | The org-wide brake that halts all board automation during an incident. |

The Statuses, in order: `Backlog` (planned, not ready) · `Triage` (incoming, un-assessed) · `Tracking`
(a parent watching its children) · `Ready` (pick-up-able) · `In progress` (a draft PR is open) ·
`In review` (a PR is up for review) · `Done` (approved, about to merge) · `Awaiting release` (merged,
not yet shipped) · `Applied` (a no-PR task that's simply done). Shipped items are **archived** (removed
from the board) and their issues closed.

## What you do — the human gates

The process has a handful of points where a human decides. Everything else follows from them, so these
are the parts to know well.

### 1. Plan

Work starts as an issue. A cross-repo change is an **Epic** with a **Task** per repo; a self-contained
change is a single **Feature** or **Task**. Planning sets the item's Type, Priority, Size, and Status,
and places it under its parent. (Planning itself is a separate practice; here it's enough to know that
work exists on the board _before_ a PR does.)

### 2. Link your PR to its issue — the one contract

**A PR that implements a planning issue must close it, using the full cross-repo reference:**

```
Closes a-novel-kit/.github#123      (or a-novel/.github#123)
```

Planning issues live in the org `.github` repo, but your PR lives in a code repo — so a bare
`Closes #123` points at _your_ repo and links **nothing**. The full reference is the entire contract:
the automation moves the issue's Status by following that link. **No link, no movement** — the issue
sits frozen while your PR sails past it. A Task filed in the _same_ repo as the PR keeps the bare form.

Once linked, opening the PR moves the issue to `In review`; merging moves it to `Awaiting release`. You
do nothing else.

### 3. Review

Approving a PR is a human gate. For a standalone PR, approval is the last step before it merges. For an
Epic member (an `epic:<N>` PR), approval is necessary but not sufficient: **merge-gate holds it until
every sibling across every repo is also ready**, then lets the whole set into the merge queue so they
land together. A held member is normal — it's waiting for its Epic, not blocked by a fault.

### 4. Release

Releasing is a deliberate, admin-only dispatch. For a single repo, run its release workflow and pick
the bump. For a whole Epic, run the **release train** once: it releases every repo the Epic touched, in
any order (they're already mutually consistent), and records each release on the Epic. It's idempotent
— re-running it only ships the repos that haven't shipped yet.

Releasing is what archives the work: the shipped items leave the board and their issues close.

### The exception levers

Three deliberate, human-triggered escape hatches, each covered below under **When something looks off**:

- **Hotfix** — patch an already-released line without waiting for the default branch.
- **Rollback** — undo an Epic that landed badly.
- **Kill-switch** — halt all board automation during an incident.

## When something looks off — edge cases

The board is designed to self-heal, so most surprises resolve on the next sweep. These are the ones
worth recognizing, and what to do.

### A PR is "held" (merge-gate is red)

It's an Epic member waiting for its siblings — expected, not a fault. Get the rest of the Epic's PRs
approved and ready; the gate greens for the whole set at once and they land together. Nothing to fix on
the held PR itself.

### A PR is "frozen" (epic-freeze fired)

The automation detected a **partial landing** — some of an Epic's members merged, but others didn't —
and froze the survivors so nothing else lands half-done. Two ways out:

- **Roll forward** (preferred): the stray member can still merge — approve and re-enqueue it, completing
  the Epic. There's a grace window before the freeze bites, so a normal queue hiccup fixes itself.
- **Roll back**: the landed members must be undone. Trigger the admin **epic-rollback** — it reverts the
  merged members newest-first, groups the reverts under a fresh rollback-Epic, and lands them through
  the same gate. Reverting merged code is destructive, so it's admin-only and asks for a typed
  confirmation.

### An escalation ticket appears on the board

The automation opens a ticket (labelled `escalation`) when it needs a human — a stuck hotfix, a stale
required check, a failed emergency path. **Act on the underlying condition, don't size it as planning
work.** When the condition clears, the ticket resolves itself; close it once handled. A pile of open
escalation tickets is an ops signal, not a backlog.

### A hotfix or lock looks stuck

A watchdog watches the emergency paths. If a hotfix run hangs on its lock, or a required check never
reaches a verdict, it escalates (see above) rather than letting the work rot silently. You'll hear
about it through a ticket.

### The board looks stale

The **reconcile sweep** re-derives the whole board every ~15 minutes, so drift usually corrects itself
within a cycle. If you need it now, the sweep is a workflow you can run on demand (Actions →
`reconcile-board` → Run workflow). It's a safety net, never the primary path — the live events do the
real work.

### Everything is halted

The **kill-switch** is on — an org variable that halts every board automation at once (the incident
brake). While it's engaged, merge-gate posts a `failure` on every gated PR (so nothing sneaks through),
rollups and archives pause, and the emergency hotfix path stays open (it's deliberately exempt). Clear
the switch to resume; the next sweep repairs anything that accumulated.

### A hotfix (patching a released line)

A bug in a shipped version, fixable without dragging in whatever landed since, is a **hotfix**: dispatch
the repo's hotfix workflow with the line and the fix, and it cuts a new patch off the released tag,
then opens a PR to forward-port the fix to the default branch. It's a deliberate, admin-gated dispatch,
and the one thing you must do is **merge that forward-port PR** — until it lands, a later release cut
from that line could silently reintroduce the bug.

## How the automation works — reference

You rarely need this, but when the board does something unexpected, this is enough to reason about it.

- **Single-writer status.** One actor (the [Agent] bot) writes every Status, derived purely from a PR's
  state. Because it's a pure function of reality, the sweep can re-run it any time and get the same
  answer — which is why the board is consistent and hand-edits are overwritten.
- **derive-status** turns a PR event into its issue's Status: draft → `In progress`, open → `In review`,
  approved → `Done`, merged → `Awaiting release`. It finds the issue through the PR's `Closes` link.
- **rollup** computes each Epic's and Initiative's Status as the floor of its children, and archives a
  parent once all its children are closed.
- **merge-gate** is the required check that enforces the **Epic Atomicity Rule** — all of an Epic's
  members land together or none do — by holding members until the set is ready, then greening them over
  the merge queue so they commit together.
- **epic-freeze / partial-landing detection** watches for an Epic that landed half-way and freezes the
  survivors; **epic-rollback** is its compensating undo.
- **the reconcile sweep** runs all of the above on a ~15-minute schedule as a level-triggered floor: it
  re-derives statuses, re-rolls-up parents, re-posts the gate, archives shipped/closed work, and pages
  a human for anything wedged. It exists so a single dropped webhook can never leave the board wrong.
- **archive** removes shipped (`Awaiting release` on publish) and done (`Applied`, or any closed issue)
  items from the board and closes their trackers — the board only ever shows live work.
- **the release train** dispatches each touched repo's own release, recording a receipt per repo on the
  Epic so a re-run resumes rather than double-ships.
- **the kill-switch** is the fail-safe halt every writer checks first; unset means running, and any
  non-empty value engages it, so a fat-fingered value fails _safe_.

## The design record

The full design and rationale, stage by stage, lives in the Initiative and its Epics — kept linked so
the "why" stays retrievable after they archive:

- Initiative [#46](https://github.com/a-novel-kit/.github/issues/46) — board & issue lifecycle automation.
- Stage Epics: [#49](https://github.com/a-novel-kit/.github/issues/49) statuses/fields/labels ·
  [#50](https://github.com/a-novel-kit/.github/issues/50) rulesets + [Agent] permissions ·
  [#51](https://github.com/a-novel-kit/.github/issues/51) rollup + single-writer status + merge-gate ·
  [#52](https://github.com/a-novel-kit/.github/issues/52) coordinator + saga + release ·
  [#53](https://github.com/a-novel-kit/.github/issues/53) hotfix + exception handlers.
