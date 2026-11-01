# Source study and independent implementation

Inspired by Andrew Keller's ProgressDelegateKFS at the inspected archived revision
[`9d0b41fad7f75ab5c91098feed20a9fdb8a5745a`](https://github.com/andrewkeller/REALbasic-Common-KFS-BSD/blob/9d0b41fad7f75ab5c91098feed20a9fdb8a5745a/Common/Logic/ProgressDelegateKFS.rbbas),
and its adjacent `UnitTests_RBCKB/TestProgressDelegateKFS.rbbas`. The upstream code
includes weighted child nodes, aggregate values/messages, indeterminate state,
timers/UI observers, throttle policies and shared cancel/pause/kill signals.

ProgressKit is independently written in Buoy from the package contract in the
[implementation plan](../../examples/andrewkeller/IMPLEMENTATION-PLAN.md). No
upstream implementation, tests, comments or artwork were copied/transcribed. The
upstream BSD three-clause library and per-file contributor notices are recorded
in the [source inventory](../../examples/andrewkeller/inventory.json); any future
actual source/test reuse must preserve its applicable notices and disclaimer.

Deliberate differences include frozen topology before execution, strict positive
finite weights with numerical checks, bounded depth/node/message sizes, a shared
mutex, immutable polling snapshots, explicit success/failure/cancelled outcomes,
and independent one-way atomic cancellation. Completion never depends on
destruction. The v0.1 package does not port UI callbacks, timers, throttling,
dynamic child removal, pause or kill signals. Its independently authored tests
exercise these Buoy contracts rather than claiming full upstream compatibility.
