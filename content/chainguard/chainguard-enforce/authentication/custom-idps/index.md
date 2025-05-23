---
title : "Using Custom Identity Providers to Authenticate to Chainguard"
linktitle: "Custom IDPs"
lead: ""
description: "An introduction to and overview of Chainguard's custom IDP support features"
type: "article"
date: 2023-04-17T08:48:45+00:00
lastmod: 2023-04-17T08:48:45+00:00
draft: false
tags: ["Enforce", "Chainguard Images", "Overview"]
images: []
weight: 010
---

> _This documentation is related to Chainguard Enforce. You can request access to the product by selecting **Chainguard Enforce** on the [inquiry form](https://www.chainguard.dev/contact?utm_source=docs)._

The Chainguard Enforce platform supports Single Sign-on (SSO) authentication for users. By default, users can log in with GitHub, GitLab and Google, but SSO support allows users to bring their own identity provider for authentication. This is helpful when your organization mandates using a corporate identity provider — like Okta or Azure Active Directory — to authenticate to SaaS products.


## Usage

Once an administrator has [configured an identity provider](#setup-and-administration), users can authenticate at the command line and in the web console using the identity provider’s unique ID.

### Authenticate with `chainctl`

`chainctl`, the Chainguard command line interface (CLI), supports SSO authentication by supplying the identity provider unique ID as a flag or by setting it as a default in configuration. To use a flag to authenticate using SSO, pass the `--identity-provider` flag to `chainctl auth login`.

```sh
export IDP_ID=<your identity provider id here>
chainctl auth login --identity-provider=$IDP_ID
```

Remembering identity provider IDs like this can be difficult. As an alternative, you can set the identity provider by editing the `chainctl` configuration file. You can do so with the following command:

```sh
chainctl config edit
```

This will open a text editor (`nano`, by default) where you can edit the local `chainctl` config. Add the following lines to this file.

```
default:
  identity-provider: <your identity provider id here>
```

Then save and close the file. If you used the default editor, `nano`, you can do so by pressing `CTRL + X`, `Y`, and then `ENTER`. 

Once set, the configured identity provider will be used automatically any time you run `chainctl auth login`.

> **Note**: `chainctl auth login --headless` is not currently compatible with SSO authentication.


### Authenticate with the Chainguard Enforce Console

To authenticate with the Chainguard Enforce Consle using SSO, click the **Use your identity provider** link on the login page.

![Screenshot showing an example Chainguard login page, with a red arrow pointing to the "Use your identity provider" link.](sso-1.png)

On the next page, add the identity provider's unique ID.

![Screenshot showing an example Chainguard login page with a field reading "IDP Identifier"](sso-2.png)

After adding your ID, click the **Continue** button. You'll then be redirected to your identity provider to authenticate, after which you'll be redirected back to the Console.


## Setup and Administration

Chainguard SSO requires OpenID Connect (OIDC) compatible identity providers. In particular, identity providers must also support the following.

* The `authorization code` grant type (sometimes called the `authorization code` *flow*).
* The standard `openid`, `email` and `profile` scopes. Note that the Chainguard platform will partially function with only the `openid` scope, but full functionality requires the `email` and `profile` scopes as well.

Customer-managed identity providers must also have a public, unauthenticated OIDC discovery endpoint.

In order to set up SSO for your identity provider, you must configure an OIDC application that the Chainguard platform can use on your identity provider. Following that, you have to configure the Chainguard platform to use that application.


### Integration Guides for supported identity providers

If your identity provider is Okta, Ping Identity or Azure Active Directory, we’ve published step-by-step integration guides for your platform.

* [Okta](/chainguard/chainguard-enforce/authentication/example-idps/okta/)
* [Ping Identity](/chainguard/chainguard-enforce/authentication/example-idps/ping-id/)
* [Azure Active Directory](/chainguard/chainguard-enforce/authentication/example-idps/azure-ad/)

If you aren't using one of these identity providers, you can complete the following Generic Integration Guide to configure your provider to work with Chainguard Enforce. However, be aware that Chainguard does not actively support identity providers other than the ones listed previously. If you are using an alternate identity provider, we encourage you to [contact us](https://www.chainguard.dev/contact?utm_source=docs) to learn more.


### Generic Integration Guide

For a generic OIDC-compatible identity provider, start by creating an OIDC application. If possible, set as much metadata as possible for the application so that your users can identify this application as the Chainguard platform. The following assets and details can be helpful to include in metadata.

* The Console homepage is [https://console.enforce.dev/](https://console.enforce.dev)
* Our terms of service can be found at [chainguard.dev/terms-of-service](https://www.chainguard.dev/terms-of-service)
* Our terms of use can be found at [chainguard.dev/terms-of-use](https://www.chainguard.dev/terms-of-use)
* Our privacy policy is located at [chainguard.dev/privacy-notice](https://www.chainguard.dev/privacy-notice)
* You can also add a Chainguard logo icon here to help your users visually identify this integration. The icon from the [Chainguard Enforce Console](https://console.enforce.dev/logo512.png) will be suitable for most platforms.

Next, configure your OIDC application as follows:

* Set redirect URI to `https://issuer.enforce.dev/oauth/callback`
* Restrict grant types to **authorization code** only. It is critical that your application does not support "client credentials", "device code", "implicit" or other grant types (sometimes called “flows”).
* Restrict response types to only authorization codes (sometimes called just “code”)
* Enable “openid”, “email” and “profile” scopes for application
* Disable or set PKCE to **optional**

Finally, configure a set of client credentials and make note of the following details to configure Chainguard:

* The issuer URL

> **Note**: It is critical that you add `/.well-known/openid-configuration` to this URL to find the OIDC discovery page and that this URL matches the `iss` claim in identity tokens issued by this identity provider exactly.

* Client ID
* Client Secret

Next, use `chainctl` to log in to Chainguard with an OIDC provider (such as Google, GitHub, or GitLab) to bootstrap your account.

```sh
chainctl auth login
```

Create a new identity provider using the details you noted from your OIDC application. Be sure to update the details in the following example `export` commands to align with your own application/client ID, client secret, and issuer URL.

```sh
export NAME=my-sso-identity-provider
export CLIENT_ID=<your application/client id here>
export CLIENT_SECRET=<your client secret here>
export ISSUER=<your issuer url here>
chainctl iam identity-provider create \
  --configuration-type=OIDC \
  --oidc-client-id=${CLIENT_ID} \
  --oidc-client-secret=${CLIENT_SECRET} \
  --oidc-issuer=${ISSUER} \
  --oidc-additional-scopes=email \
  --oidc-additional-scopes=profile \
  --name=${NAME}
```

This command will prompt you to select a Chainguard IAM group under which to install your identity provider. Your selection won’t affect how your users authenticate but will have implications on who has permission to modify the SSO configuration.


## Managing Existing Identity Providers

Identity providers can be managed via `chainctl` using the `chainctl iam identity-provider` subcommand.

To create new providers, you can use the `create` subcommand.

```sh
chainct iam identity-provider create
```

To list out every configured identity provider, run the `list` subcommand.

```sh
chainctl iam identity-provider list
```

To modify an existing identity provider, use the `update` subcommand.

```sh
chainctl iam identity-provider update
```

This can be useful for rotating client credentials.

Lastly, to delete an identity provider, run the `delete` subcommand.

```sh
chainctl iam identity-provider delete
``` 

For more details, check out the [`chainctl` documentation for these commands](/chainguard/chainctl/chainctl-docs/chainctl_iam_identity-providers/).


## IAM and Security

Once an identity provider has been created on the Chainguard platform, any user that can authenticate with that identity provider will be able to use it to access the Chainguard platform. It’s important to note that users can do so even if they have no IAM capabilities with the IAM Group at which the identity provider is defined. Identity providers give access to the Chainguard platform, but not the specific IAM Group where the identity provider is defined.

The IAM capabilities `identity_providers.create`, `identity_providers.update`, `identity_providers.list` and `identity_providers.delete` control which users can read and manipulate identity providers. The built-in roles `viewer`, `editor` and `owner` have the following identity provider related capabilities.

| **Role** | **Capabilities** |
|----------|----------|
| `viewer`   | `identity_providers.list`   |
| `editor`   | `identity_providers.list`   |
| `owner`   | `identity_providers.create`, `identity_providers.list`, `identity_providers.update`, `identity_providers.delete`   |

As with other capabilities, if an identity provider capability is granted to a parent IAM group, it is inherited on all child IAM groups.


## Backup accounts

In the case of an outage or misconfiguration of your identity provider, it can be helpful to have an authentication mechanism to the Chainguard outside of your SSO identity provider to recover. For this purpose, you can use one of our OIDC login providers (currently Google, GitHub, or GitLab) to create a backup account

As an OIDC login account needs to be set up to bootstrap the SSO identity provider initially, it’s possible to keep this account as a breakglass account in case you need it for recovery. However, the nature of these OIDC provider accounts is such that it is difficult to share them as a breakglass resource as they’re often tied to a single user.

Instead of relying on an account with an OIDC login provider, you can alternatively set up an assumable identity to use as a backup account. Refer to our [conceptual guide on assumable identities](/chainguard/chainguard-enforce/iam-groups/assumable-ids/) to learn more.