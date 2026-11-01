# ProgressKit 0.1.0

`Import progresskit` adds headless weighted progress trees, detached immutable
snapshots and one-way cooperative cancellation. Reports and snapshots are safe
from multiple threads. No GUI, callback, timer or native package dependency is
introduced by importing it.

## Plan and run

Create `New ProgressNode(message = "", maxNodes = 10000, maxDepth = 64)`. This is
the root. Before starting, call `node.Split(weights As Array Of Double)` to turn
a leaf into a group and receive an independent array of child handles. Each leaf
can split once, with one or more finite positive weights. Children can themselves
split. `root.Start()` freezes the entire topology; repeated root starts are
harmless, while Start on a child throws. Configure the tree before handing its
handles to workers.

Weights are copied and normalized once, relative to siblings. Scaling by the
largest weight avoids overflowing a sum of large finite weights. Ratios that
underflow to zero reject the whole Split before any topology change. Zero and
negative weights, NaN/infinity, empty groups and exceeded bounds reject too.
`maxNodes` accepts 1..100000 and counts the root; `maxDepth` accepts 0..64 and
counts edges below the root. All messages accept at most 4096 UTF-8 bytes.

After Start, a leaf supports:

- `Report(fraction As Double, message = "")`: known finite fraction in 0..1;
  explicit regressions are allowed. Zero means known zero. Reporting one does
  not finish the work.
- `ReportUnknown(message = "")`: unknown total/progress, distinct from zero.
  The last reported fraction is retained internally but contributes zero to a
  snapshot while unknown.
- `Complete(message = "")`: successful terminal state, forcing fraction one.
- `Fail(message As String)`: failed terminal state.
- `Cancel(message = "")`: worker-acknowledged cancelled terminal state.

Fail/Cancel retain the last Report fraction, or zero if never reported, and clear
indeterminate state. Repeating the same terminal operation is a no-op, retaining
its first message. A conflicting terminal operation or any later Report or
ReportUnknown throws. Group reports/terminal operations throw: finish its leaves.
Dropping a handle has no effect on progress or outcomes. All rejected argument
and state operations throw `InvalidArgumentException`.

`SetMessage(message)` sets a local message on any unfinished node, including a
group and before Start. Every Report/ReportUnknown/terminal call replaces the
leaf's message, including when its optional message is empty. A snapshot uses the
node's nonempty local message, otherwise the first nonempty descendant message
in Split order. Message selection is deterministic, not necessarily the currently
active stage; set an explicit root message when that distinction matters.

## Observe snapshots

`node.Snapshot()` returns an immutable `ProgressSnapshot` with read-only fields:

| Member | Meaning |
| --- | --- |
| `Fraction` | Weighted descendant fraction in 0..1; unknown leaves contribute zero |
| `IsIndeterminate` | At least one descendant leaf is unknown |
| `Message` | Local message or deterministic descendant fallback |
| `IsStarted` | Whether the root has started |
| `Outcome` | `pending`, `running`, `succeeded`, `failed` or `cancelled` |
| `IsFinished` | Terminal success, failure or acknowledged cancellation |
| `IsComplete` | Successful completion only |
| `IsCancellationRequested` | Shared cancellation flag observed for this snapshot |

Each leaf starts unknown. Before Start the outcome is pending; after Start it is
running until a terminal operation. A group stays running until every descendant
leaf is terminal, then failure takes precedence over cancellation, which takes
precedence over success. A successful group has exactly fraction one. Failed and
cancelled work does not automatically contribute fraction one. A fraction can be
one without success, so use Outcome/IsComplete to decide whether to show success.

The snapshot captures all tree fields under one mutex and samples cancellation
once while holding it. Old snapshots never change and retain no tree references.
Snapshot and group SetMessage cost O(subtree nodes), with at most 64 recursive
edges. Reports and leaf terminal operations are O(1). All handles retain a shared
record store with integer child indices; retaining a child after releasing the
root is valid. There are no parent/child reference cycles. Construction/Split
allocate proportionally to node count; message limits are per node, not a total
memory quota.

## Cancellation and UI ownership

Every node's read-only `Cancellation` property returns the same
`ProgressCancellationToken`. Call `Request()` from any thread; `IsRequested` is
atomic, sticky and cannot reset. Requesting does not stop a thread, alter progress
or finish a leaf. Workers check the token, release their resources and acknowledge
with Cancel. Cancellation remains observable if requested after successful
completion; the successful outcome does not change. Create a new root for a new
operation. A standalone token can also be constructed independently.

Poll Snapshot at an application-chosen cadence. A desktop Timer can sample it on
the UI thread and map IsIndeterminate/Fraction to a ProgressBar, with Outcome
controlling the terminal status. The model performs no UI dispatch, throttling,
automatic scheduling or observer callbacks. It does not implement pause/kill,
dynamic topology, persistence, result payloads, or exception propagation from
workers; callers translate their worker outcomes to Complete/Fail/Cancel.

Maintainers run the private validation suite before release. See the repository README for public build instructions.
