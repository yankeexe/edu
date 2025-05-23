---
title: "Image Overview: node"
type: "article"
description: "Overview: node Chainguard Images"
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

`stable` [cgr.dev/chainguard/node](https://github.com/chainguard-images/images/tree/main/images/node)
| Tags         | Aliases                                                |
|--------------|--------------------------------------------------------|
| `latest`     | `18`, `18.16`, `18.16.0`, `18.16.0-r2`                 |
| `latest-dev` | `18-dev`, `18.16-dev`, `18.16.0-dev`, `18.16.0-r2-dev` |
| `19`         | `19`, `19.9`, `19.9.0`, `19.9.0-r1`                    |
| `19-dev`     | `19-dev`, `19.9-dev`, `19.9.0-dev`, `19.9.0-r1-dev`    |
| `20`         | `20`, `20.2`, `20.2.0`, `20.2.0-r0`                    |
| `20-dev`     | `20-dev`, `20.2-dev`, `20.2.0-dev`, `20.2.0-r0-dev`    |



Minimal container image for running NodeJS apps

The image specifies a default non-root `node` user (UID 65532), and a working directory at `/app`, owned by that `node` user, and accessible to all users.

It specifies `NODE_PORT=3000` by default.

## Get It!

The image is available on `cgr.dev`:

```
docker pull cgr.dev/chainguard/node:latest
```

## Usage Example

Navigate to the [`example/`](https://github.com/chainguard-images/images/tree/main/images/node/example) directory:

```
cd example/
```

The Dockerfile is based on Docker's [Build your Node image](https://docs.docker.com/language/nodejs/build-images/) tutorial, but uses the Chainguard node base image instead.

Build the application on the node base image.

```
docker build \
  --tag node-docker \
  --platform=linux/amd64 \
  .
```

Then you can run the image:

```
docker run \
  --rm -it \
  -p 8000:8000 \
  --platform=linux/amd64 \
  node-docker
```

...and test to see that it works:

```
curl --request POST \
  --url http://localhost:8000/test \
  --header 'content-type: application/json' \
  --data '{"msg": "testing" }'
```

