---
title: "argocd-repo-server Image Variants"
type: "article"
description: "Detailed specs for argocd-repo-server Chainguard Image Variants"
date: 2023-03-07T11:07:52+02:00
lastmod: 2023-03-07T11:07:52+02:00
draft: false
tags: ["Reference", "Chainguard Images", "Product"]
images: []
menu:
  docs:
    parent: "argocd-repo-server"
weight: 550
toc: true
---

This page shows detailed information about all available variants of the Chainguard **argocd-repo-server** Image.

## Variants Compared
The **argocd-repo-server** Chainguard Image currently has 2 public variants: 

- `latest`
- `latest-dev`

The table has detailed information about each of these variants.

|              | latest                              | latest-dev                          |
|--------------|-------------------------------------|-------------------------------------|
| Default User | `argocd`                            | `argocd`                            |
| Entrypoint   | `/usr/local/bin/argocd-repo-server` | `/usr/local/bin/argocd-repo-server` |
| CMD          | not specified                       | not specified                       |
| Workdir      | `/home/argocd`                      | `/home/argocd`                      |
| Has apk?     | no                                  | yes                                 |
| Has a shell? | yes                                 | yes                                 |

## Image Dependencies
The table shows package distribution across all variants.

|                          | latest | latest-dev |
|--------------------------|--------|------------|
| `ca-certificates-bundle` | X      | X          |
| `busybox`                | X      | X          |
| `wolfi-baselayout`       | X      | X          |
| `argo-cd-repo-server`    | X      | X          |
| `argo-cd-compat`         | X      | X          |
| `apk-tools`              |        | X          |
| `bash`                   |        | X          |
| `git`                    |        | X          |

