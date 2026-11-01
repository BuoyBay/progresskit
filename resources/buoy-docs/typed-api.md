---
title: "progresskit typed API"
summary: "Source-derived public signatures, properties, parameters, defaults, and return values."
category: "Reference"
order: 21
types: ["ProgressCancellationToken", "ProgressNode", "ProgressSnapshot"]
dependencies: []
symbols: ["ProgressCancellationToken", "ProgressNode", "ProgressSnapshot"]
related: ["api.md", "getting-started.md", "index.md"]
---

# progresskit typed API

Generated from the package source declarations. Platform guards are shown where present; inherited members are documented on the base type. See the [API contracts](api.md) for behavior and limitations.

## ProgressCancellationToken

`Public Class ProgressCancellationToken`

One-way cooperative cancellation; requesting never stops or completes work.

### Properties

| Name | Type | Read-Only | Shared | Default | Description |
| --- | --- | --- | --- | --- | --- |
| `IsRequested` | `Boolean` | Yes | No | — | — |

### Constructor

`Public Sub Constructor()`

No parameters.

Returns: no value.

### Request

`Public Sub Request()`

No parameters.

Returns: no value.

## ProgressSnapshot

`Public Class ProgressSnapshot`

Detached immutable view of one node and its descendants.

### Properties

| Name | Type | Read-Only | Shared | Default | Description |
| --- | --- | --- | --- | --- | --- |
| `Fraction` | `Double` | Yes | No | — | — |
| `Message` | `String` | Yes | No | — | — |
| `Outcome` | `String` | Yes | No | — | — |
| `IsStarted` | `Boolean` | Yes | No | — | — |
| `IsIndeterminate` | `Boolean` | Yes | No | — | — |
| `IsFinished` | `Boolean` | Yes | No | — | — |
| `IsComplete` | `Boolean` | Yes | No | — | — |
| `IsCancellationRequested` | `Boolean` | Yes | No | — | — |

## ProgressNode

`Public Class ProgressNode`

Fixed-topology weighted progress with thread-safe reports and snapshots.

### Properties

| Name | Type | Read-Only | Shared | Default | Description |
| --- | --- | --- | --- | --- | --- |
| `Cancellation` | `ProgressCancellationToken` | Yes | No | — | — |

### Constructor

`Public Sub Constructor(message As String = "", maxNodes As Integer = 10000, maxDepth As Integer = 64)`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `message` | `String` | `ByVal` | `""` |
| `maxNodes` | `Integer` | `ByVal` | `10000` |
| `maxDepth` | `Integer` | `ByVal` | `64` |

Returns: no value.

### Split

`Public Function Split(weights As Array Of Double) As Array Of ProgressNode`

Split a leaf once before Start; returned handles retain the shared tree.

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `weights` | `Array Of Double` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `Array Of ProgressNode` |

### Start

`Public Sub Start()`

Only the root may start; repeated root starts are harmless.

No parameters.

Returns: no value.

### Report

`Public Sub Report(fraction As Double, message As String = "")`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `fraction` | `Double` | `ByVal` | Required |
| `message` | `String` | `ByVal` | `""` |

Returns: no value.

### ReportUnknown

`Public Sub ReportUnknown(message As String = "")`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `message` | `String` | `ByVal` | `""` |

Returns: no value.

### Complete

`Public Sub Complete(message As String = "")`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `message` | `String` | `ByVal` | `""` |

Returns: no value.

### Fail

`Public Sub Fail(message As String)`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `message` | `String` | `ByVal` | Required |

Returns: no value.

### Cancel

`Public Sub Cancel(message As String = "")`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `message` | `String` | `ByVal` | `""` |

Returns: no value.

### SetMessage

`Public Sub SetMessage(message As String)`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `message` | `String` | `ByVal` | Required |

Returns: no value.

### Snapshot

`Public Function Snapshot() As ProgressSnapshot`

No parameters.

#### Returns

| Type |
| --- |
| `ProgressSnapshot` |
