---
title: "Image Overview: nats"
type: "article"
description: "Overview: nats Chainguard Images"
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

`experimental` [cgr.dev/chainguard/nats](https://github.com/chainguard-images/images/tree/main/images/nats)
| Tags         | Aliases                                           |
|--------------|---------------------------------------------------|
| `latest`     | `2`, `2.9`, `2.9.17`, `2.9.17-r2`                 |
| `latest-dev` | `2-dev`, `2.9-dev`, `2.9.17-dev`, `2.9.17-r2-dev` |



Minimal image with NATS. **EXPERIMENTAL**

## Get It!

The image is available on `cgr.dev`:

```
docker pull cgr.dev/chainguard/nats:latest
```

## Using NATS

The default Chainguard NATS images includes only the `nats-server` binary.
The `dev` variant also includes the `nats` cli, and the `nsc` tool.
The default entrypoint is set to run the `nats-server` binary with a sample configuration at `/etc/nats/nats-server.conf`.
This can be overridden with the `--config-file` flag.

To run the NATS image with the sample configuration, use:

```shell
$ docker run cgr.dev/chainguard/nats
[1] 2023/03/13 19:37:46.086234 [INF] Starting nats-server
[1] 2023/03/13 19:37:46.086378 [INF]   Version:  2.9.15
[1] 2023/03/13 19:37:46.086380 [INF]   Git:      [b91fa85462d42c2f988170aee27955773e68c56d]
[1] 2023/03/13 19:37:46.086384 [INF]   Cluster:  MQROFIQSt51d4SvUIRXPVV
[1] 2023/03/13 19:37:46.086391 [INF]   Name:     NBXLCFJORJCUWEKDEIAUQHYEEB2FK6DUBKOYMSFJLPKVIVLF4ZVKBQVO
[1] 2023/03/13 19:37:46.086392 [INF]   ID:       NBXLCFJORJCUWEKDEIAUQHYEEB2FK6DUBKOYMSFJLPKVIVLF4ZVKBQVO
[1] 2023/03/13 19:37:46.086404 [INF] Using configuration file: /etc/nats/nats-server.conf
[1] 2023/03/13 19:37:46.087155 [INF] Starting http monitor on 0.0.0.0:8222
[1] 2023/03/13 19:37:46.087237 [INF] Listening for client connections on 0.0.0.0:4222
[1] 2023/03/13 19:37:46.087553 [INF] Server is ready
[1] 2023/03/13 19:37:46.087603 [INF] Cluster name is MQROFIQSt51d4SvUIRXPVV
[1] 2023/03/13 19:37:46.087610 [WRN] Cluster name was dynamically generated, consider setting one
[1] 2023/03/13 19:37:46.087671 [INF] Listening for route connections on 0.0.0.0:6222
```

