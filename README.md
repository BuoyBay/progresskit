# Weighted progress trees

`Import progresskit` adds headless weighted progress trees, detached immutable
snapshots and one-way cooperative cancellation. Reports and snapshots are safe
from multiple threads. No GUI, callback, timer or native package dependency is
introduced by importing it.

## Install

From a Buoy consumer project with BPM installed:

```sh
bpm add progresskit --git https://github.com/BuoyBay/progresskit.git --rev refs/heads/main
```

BPM records the resolved commit in its lockfile. Keep that lockfile with the
consumer project for reproducible installs.

## API at a glance

Create `New ProgressNode(message = "", maxNodes = 10000, maxDepth = 64)`. This is
the root. Before starting, call `node.Split(weights As Array Of Double)` to turn
a leaf into a group and receive an independent array of child handles. Each leaf
can split once, with one or more finite positive weights. Children can themselves
split. `root.Start()` freezes the entire topology; repeated root starts are
harmless, while Start on a child throws. Configure the tree before handing its
handles to workers.

## Build from source

With the native `build-packages` driver from [devtools](https://github.com/BuoyBay/devtools)
on your `PATH`, run from this checkout:

```sh
build-packages --root "$PWD" --compile-consumer progresskit
```

The driver checks source targets and compiles an import consumer. See the
[API guide](docs/API.md) for behavior, limits and any host prerequisites.

## Local Buoy setup

On your own machine, install Buoy and run:

```sh
buoy ai init --both
```

## Contributing

[Contributing](CONTRIBUTING.md) · [License](LICENSE)
