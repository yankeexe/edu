---
title: "Image Overview: buck2"
type: "article"
description: "Overview: buck2 Chainguard Images"
date: 2022-11-01T11:07:52+02:00
lastmod: 2022-11-01T11:07:52+02:00
draft: false
tags: ["Reference", "Chainguard Images", "Product"]
images: []
menu:
  docs:
    parent: "images-reference"
weight: 500
toc: true
---

`stable` [cgr.dev/chainguard/buck2](https://github.com/chainguard-images/images/tree/main/images/buck2)
| Tags         | Aliases                                                  |
|--------------|----------------------------------------------------------|
| `latest`     | `0`, `0.0_git20230603`, `0.0_git20230603-r0`             |
| `latest-dev` | `0-dev`, `0.0_git20230603-dev`, `0.0_git20230603-r0-dev` |



Minimal image with buck2 binary and toolchain.

## Get It!

The image is available on `cgr.dev`:

```
docker pull cgr.dev/chainguard/buck2:latest
```

## Usage

The default entrypoint is set to the `buck` binary, and a default C/C++ toolchain is installed in the image.

```
docker run cgr.dev/chainguard/buck2:latest
buck2 8cb388050e28f9386ccc2458d6dcf55137e2f3b13008b2a13000994b38c496db <local>
A build system

Documentation: https://buck2.build/docs/

USAGE:
    buck2 [OPTIONS] <SUBCOMMAND>

OPTIONS:
    -h, --help
            Print help information

        --isolation-dir <ISOLATION_DIR>
            Instances of Buck2 share a daemon if and only if their isolation directory is identical.
            The isolation directory also influences the output paths provided by Buck2, and as a
            result using a non-default isolation dir will cause cache misses (and slower builds)

            [env: BUCK_ISOLATION_DIR=]
            [default: v2]

    -v, --verbose <NUMBER>
            How verbose buck should be while logging. Values: 0 = Quiet, errors only; 1 = default; 2
            = more info about errors; 3 = more info about everything

            [default: 1]

    -V, --version
            Print version information

SUBCOMMANDS:
    aquery
            Perform queries on the action graph (experimental)
    audit
            Perform lower level queries
    build
            Build the specified targets
    bxl
            Run BXL scripts
    clean
            Delete generated files and caches
    cquery
            Perform queries on the configured target graph
    ctargets
            Resolve target patterns to configured targets
    docs
            Print documentation of specified symbols
    help
            Print this message or the help of the given subcommand(s)
    init
            Initialize a buck2 project
    install
            Build and install an application
    kill
            Kill the buck daemon
    killall
            Kill all buck2 processes on the machine
    log
            Commands for interacting with buck2 logs
    lsp
            Start an LSP server for starlark files
    profile
            Profiling mechanisms
    query
            Alias for `uquery`
    rage
            Record information about the previous failed buck2 command
    root
            Find buck cell, project or package root
    run
            Build and run the selected target
    server
            Start, query, and control the http server
    starlark
            Run Starlark operations
    status
            Buckd status
    subscribe
            Subscribe to updates from the Buck2 daemon
    targets
            Show details about the specified targets
    test
            Build and test the specified targets
    uquery
            Perform queries on the unconfigured target graph
```

