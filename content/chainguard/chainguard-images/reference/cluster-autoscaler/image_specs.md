---
title: "cluster-autoscaler Image Variants"
type: "article"
description: "Detailed specs for cluster-autoscaler Chainguard Image Variants"
date: 2023-03-07T11:07:52+02:00
lastmod: 2023-03-07T11:07:52+02:00
draft: false
tags: ["Reference", "Chainguard Images", "Product"]
images: []
menu:
  docs:
    parent: "cluster-autoscaler"
weight: 550
toc: true
---

This page shows detailed information about all available variants of the Chainguard **cluster-autoscaler** Image.

## Variants Compared
The **cluster-autoscaler** Chainguard Image currently has 2 public variants: 

- `latest`
- `latest-dev`

The table has detailed information about each of these variants.

|              | latest                        | latest-dev                    |
|--------------|-------------------------------|-------------------------------|
| Default User | `cluster-autoscaler`          | `cluster-autoscaler`          |
| Entrypoint   | `/usr/bin/cluster-autoscaler` | `/usr/bin/cluster-autoscaler` |
| CMD          | not specified                 | not specified                 |
| Workdir      | `/`                           | `/`                           |
| Has apk?     | no                            | yes                           |
| Has a shell? | no                            | yes                           |

## Image Dependencies
The table shows package distribution across all variants.

|                             | latest | latest-dev |
|-----------------------------|--------|------------|
| `cluster-autoscaler`        | X      | X          |
| `cluster-autoscaler-compat` | X      | X          |
| `wolfi-baselayout`          | X      | X          |
| `ca-certificates-bundle`    | X      | X          |
| `apk-tools`                 |        | X          |
| `bash`                      |        | X          |
| `busybox`                   |        | X          |
| `git`                       |        | X          |

