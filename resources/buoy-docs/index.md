---
title: "ProgressKit"
summary: "Bounded weighted progress trees, immutable snapshots and cooperative cancellation."
category: "Overview"
order: 0
keywords: ["progress", "weighted", "snapshot", "cancellation", "thread"]
related: ["getting-started.md", "api.md", "typed-api.md"]
---

# ProgressKit

ProgressKit provides a headless progress model shared by worker threads and UI or
console observers. Configure fixed child weights before starting the root. Leaves
report a known fraction or explicitly unknown progress, then finish successfully,
fail, or acknowledge cancellation. Group outcomes aggregate from their leaves.

Snapshots are detached immutable views. A cancellation request is a cooperative
signal, separate from worker completion and successful progress. No callbacks,
UI operations or background work happen merely by importing the package.

Read [Getting started](getting-started.md) and [API reference](api.md). The package
is compiler checked for macOS, Windows and Linux on x86_64 and arm64. Native
headless verification passed on Linux x86_64, macOS arm64 and Windows x64.
