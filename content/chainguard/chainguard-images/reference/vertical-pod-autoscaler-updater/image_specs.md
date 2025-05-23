---
title: "vertical-pod-autoscaler-updater Image Variants"
type: "article"
description: "Detailed specs for vertical-pod-autoscaler-updater Chainguard Image Variants"
date: 2023-03-07T11:07:52+02:00
lastmod: 2023-03-07T11:07:52+02:00
draft: false
tags: ["Reference", "Chainguard Images", "Product"]
images: []
menu:
  docs:
    parent: "vertical-pod-autoscaler-updater"
weight: 550
toc: true
---

This page shows detailed information about all available variants of the Chainguard **vertical-pod-autoscaler-updater** Image.

## Variants Compared
The **vertical-pod-autoscaler-updater** Chainguard Image currently has one public variant: 

- `latest`

The table has detailed information about each of these variants.

|              | latest                         |
|--------------|--------------------------------|
| Default User | `nonroot`                      |
| Entrypoint   | `/usr/bin/updater`             |
| CMD          | `--v=4 --stderrthreshold=info` |
| Workdir      | not specified                  |
| Has apk?     | no                             |
| Has a shell? | no                             |

## Image Dependencies
The table shows package distribution across all variants.

|                                   | latest |
|-----------------------------------|--------|
| `ca-certificates-bundle`          | X      |
| `wolfi-baselayout`                | X      |
| `vertical-pod-autoscaler-updater` | X      |

