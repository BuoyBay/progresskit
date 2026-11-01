---
title: "ProgressKit getting started"
summary: "Plan weighted stages, start workers and observe detached snapshots."
category: "Guides"
order: 10
keywords: ["Split", "Start", "Report", "Complete", "Cancel"]
related: ["index.md", "api.md", "typed-api.md"]
---

# Getting started

Install `progresskit` through BPM or put `progresskit-0.1.0.buipkg` in your
consumer's `packages/` directory, then use `Import progresskit`.

Construct a ProgressNode root. Split it using an array of positive Double weights
and retain the returned children. Split children again for nested stages if
needed. Only the root can Start, and it freezes the entire topology before
worker-thread reports. A one-to-three sibling weighting assigns one quarter to
the first stage and three quarters to the second.

Use Report for finite known fractions in 0..1; use ReportUnknown when the total is
unknown. Reporting one does not finish a stage: call Complete only after the
stage succeeds. Call Fail after a failure or Cancel after the worker has actually
stopped in response to cancellation. The root snapshot's IsComplete is true only
when every leaf succeeded. Create a new tree for a new operation.

Poll Snapshot rather than retaining one forever. In a desktop app, sample from a
UI Timer and update native controls there. IsIndeterminate and Fraction describe
the displayed progress; Outcome distinguishes running, success, failure and
acknowledged cancellation. The package does not call UI controls from workers.

Call the shared Cancellation.Request and have workers check IsRequested. A
request alone never makes a node terminal. Release worker-owned resources before
acknowledging Cancel; keep worker inputs alive until their threads have exited.

The project includes a checked console example at
`examples/andrewkeller/progress/main.bui` and the focused verifier
`tests/keller_progress_verify.py`.
