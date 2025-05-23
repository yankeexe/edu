---
date: 2023-06-09T16:20:16Z
title: "chainctl events subscriptions"
slug: chainctl_events_subscriptions
url: /chainguard/chainctl/chainctl-docs/chainctl_events_subscriptions/
draft: false
tags: ["chainctl", "Reference", "Product"]
images: []
type: "article"
toc: true
---
## chainctl events subscriptions

Subscription interactions.

### Options

```
  -h, --help   help for subscriptions
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

* [chainctl events](/chainguard/chainctl/chainctl-docs/chainctl_events/)	 - Events related commands for the Chainguard platform.
* [chainctl events subscriptions create](/chainguard/chainctl/chainctl-docs/chainctl_events_subscriptions_create/)	 - Subscribe to events under a group.
* [chainctl events subscriptions delete](/chainguard/chainctl/chainctl-docs/chainctl_events_subscriptions_delete/)	 - Delete a subscription.
* [chainctl events subscriptions list](/chainguard/chainctl/chainctl-docs/chainctl_events_subscriptions_list/)	 - List subscriptions.

