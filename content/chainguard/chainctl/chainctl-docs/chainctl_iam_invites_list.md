---
date: 2023-06-09T16:20:16Z
title: "chainctl iam invites list"
slug: chainctl_iam_invites_list
url: /chainguard/chainctl/chainctl-docs/chainctl_iam_invites_list/
draft: false
tags: ["chainctl", "Reference", "Product"]
images: []
type: "article"
toc: true
---
## chainctl iam invites list

List group invites.

```
chainctl iam invites list --group=GROUP_NAME|GROUP_ID [--output table|json|id]
```

### Examples

```
  # List all accessible group invites
  chainctl iam invites list
  
  # Filter invites by group
  chainctl iam invites list --group=my-group
```

### Options

```
      --group string   List invites from this group.
  -h, --help           help for list
```

### Options inherited from parent commands

```
      --api string                   The url of the Chainguard platform API. (default "http://api.api-system.svc")
      --audience string              The Chainguard token audience to request. (default "http://api.api-system.svc")
      --config string                A specific chainctl config file.
      --console string               The url of the Chainguard platform Console. (default "http://console-ui.api-system.svc")
      --issuer string                The url of the Chainguard STS endpoint. (default "http://issuer.oidc-system.svc")
  -o, --output string                Output format. One of: ["", "table", "tree", "json", "id", "wide"]
      --timestamp-authority string   The url of the Chainguard Timestamp Authority endpoint. (default "http://tsa.timestamp-authority.svc")
  -v, --v int                        Set the log verbosity level.
```

### SEE ALSO

* [chainctl iam invites](/chainguard/chainctl/chainctl-docs/chainctl_iam_invites/)	 - Manage invite codes that register identities or clusters with Chainguard.

