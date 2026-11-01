---
title: "ProgressKit API"
summary: "Tree construction, reports, terminal outcomes, snapshots and cancellation contracts."
category: "Reference"
order: 20
keywords: ["ProgressNode", "ProgressSnapshot", "ProgressCancellationToken"]
symbols: ["ProgressNode", "ProgressNode.Split", "ProgressNode.Start", "ProgressNode.Report", "ProgressNode.ReportUnknown", "ProgressNode.Complete", "ProgressNode.Fail", "ProgressNode.Cancel", "ProgressNode.SetMessage", "ProgressNode.Snapshot", "ProgressNode.Cancellation", "ProgressSnapshot", "ProgressCancellationToken"]
related: ["index.md", "getting-started.md", "typed-api.md"]
---

# API reference

## ProgressNode

| Member | Contract |
| --- | --- |
| `New ProgressNode(message = "", maxNodes = 10000, maxDepth = 64)` | Root; node count 1..100000, depth 0..64, UTF-8 messages up to 4096 bytes |
| `Split(weights As Array Of Double)` | Split one leaf once before Start; nonempty finite positive weights; returns child handles |
| `Start()` | Root-only, freezes the entire tree; repeated calls harmless |
| `Report(fraction As Double, message = "")` | Started unfinished leaf; known finite fraction in 0..1; regressions allowed |
| `ReportUnknown(message = "")` | Started unfinished leaf; unknown contribution is zero |
| `Complete(message = "")` | Leaf succeeds and forces fraction one |
| `Fail(message As String)` | Leaf fails, retaining its last Report fraction or zero |
| `Cancel(message = "")` | Leaf acknowledges cancellation, retaining its last Report fraction or zero |
| `SetMessage(message As String)` | Any unfinished node, including before Start |
| `Snapshot()` | Immutable detached snapshot of this subtree |
| `Cancellation` | Shared one-way cancellation token |

Split copies and normalizes weights using max-weight scaling. Zero/negative,
NaN/infinite and underflowed weights reject; bounds count the root at depth zero.
All validation/state failures throw InvalidArgumentException. No group may Report
or finish directly. Identical terminal operations are no-ops preserving the first
terminal message; other terminal changes and subsequent reports throw.

Report, ReportUnknown and terminal operations replace the message, including with
their default empty message. Snapshot uses a local nonempty message, otherwise
the first nonempty descendant in child order. Fail/Cancel clear unknown state.
Dropping handles never marks work complete.

## ProgressSnapshot

Read-only Fraction, Message, Outcome, IsStarted, IsIndeterminate, IsFinished,
IsComplete and IsCancellationRequested. Outcome is pending before root Start and
running thereafter until terminal. A group becomes terminal when all descendant
leaves do: failed takes precedence over cancelled, then succeeded. IsComplete
means success; IsFinished includes failure and acknowledged cancellation.

Unknown leaves contribute zero to the fraction. Report(1) is not successful
completion. A cancelled/failed leaf preserves its last Report fraction and never
automatically contributes one. A successful group returns exactly one. Snapshot
is O(subtree nodes), holds one tree mutex and samples cancellation once. An old
snapshot cannot change and does not retain the tree. Reports are O(1).

## ProgressCancellationToken

New ProgressCancellationToken creates an independent atomic flag; a root also
constructs a token automatically. Request sets it permanently, IsRequested reads
it. Request never stops threads or changes node outcomes, even after success.
Workers acknowledge stopped work with Cancel. Tokens have no reset/pause/kill API.

There are no callbacks, scheduling, dynamic topology or persistence. Configure
the tree before distributing handles to workers. Mutations/snapshots are guarded
by a shared mutex; records use child indices to avoid reference cycles. A child
handle can retain the shared store after the root handle is gone.


[Full typed API: properties, parameters, defaults, and returns](typed-api.md)
