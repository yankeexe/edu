---
title: "Image Overview: kubernetes-csi-external-resizer"
type: "article"
description: "Overview: kubernetes-csi-external-resizer Chainguard Images"
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

`stable` [cgr.dev/chainguard/kubernetes-csi-external-resizer](https://github.com/chainguard-images/images/tree/main/images/kubernetes-csi-external-resizer)
| Tags         | Aliases                                         |
|--------------|-------------------------------------------------|
| `latest`     | `1`, `1.8`, `1.8.0`, `1.8.0-r1`                 |
| `latest-dev` | `1-dev`, `1.8-dev`, `1.8.0-dev`, `1.8.0-r1-dev` |



Minimal image with [kubernetes-csi/external-resizer](https://github.com/kubernetes-csi/external-resizer).

## Get It!

The image is available on `cgr.dev`:

```
docker pull cgr.dev/chainguard/external-resizer:latest
```

## Using external-resizer

The Chainguard external-resizer image contains the `csi-resizer` controller and is a drop-in replacement for the upstream image.

To try it out, follow the [official installation
instructions](https://github.com/kubernetes-csi/external-resizer/blob/master/README.md#usage).

