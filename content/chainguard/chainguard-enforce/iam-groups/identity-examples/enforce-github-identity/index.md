---
title : "Create an Assumable Identity for a GitHub Actions Workflow"
linktitle: "GitHub Actions Assumable Identity"
aliases:
- /chainguard/chainguard-enforce/iam-groups/enforce-github-identity/
lead: ""
description: "Procedural tutorial outlining how to create a Chainguard Enforce identity that can be assumed by a GitHub Actions workflow."
type: "article"
date: 2023-05-04T08:48:45+00:00
lastmod: 2023-05-04T08:48:45+00:00
draft: false
tags: ["Enforce", "Product", "Procedural"]
images: []
weight: 005
---

In Chainguard Enforce, [*assumable identities*](/chainguard/chainguard-enforce/iam-groups/assumable-ids/) are identities that can be assumed by external applications or workflows in order to perform certain tasks that would otherwise have to be done by a human.

This procedural tutorial outlines how to create an identity using Terraform, and then create a GitHub Actions workflow that will assume the identity to interact with Chainguard resources. 


## Prerequisites

To complete this guide, you will need the following.

* `terraform` installed on your local machine. Terraform is an open-source Infrastructure as Code tool which this guide will use to create various cloud resources. Follow [the official Terraform documentation](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli) for instructions on installing the tool.
* `chainctl` — the Chainguard Enforce command line interface tool — installed on your local machine. Follow our guide on [How to Install `chainctl`](/chainguard/chainguard-enforce/how-to-install-chainctl/) to set this up.
* A GitHub repository you can use for testing out GitHub identity federation. To complete this guide, you must have permissions to create GitHub Actions on this testing repo. 


## Creating Terraform Files

We will be using Terraform to create an identity for a GitHub Actions workflow to assume. This step outlines how to create three Terraform configuration files that, together, will produce such an identity.

To help explain each configuration file's purpose, we will go over what they do and how to create each file one by one. First, though, create a directory to hold the Terraform configuration and navigate into it.

```sh
mkdir ~/enforce-actions && cd $_
```

This will help make it easier to clean up your system at the end of this guide.


### `main.tf`

The first file, which we will call `main.tf`, will serve as the scaffolding for our Terraform infrastructure.

The file will consist of the following content.

```
terraform {
  required_providers {
	chainguard = {
  	source = "chainguard/chainguard"
	}
  }
}

provider "chainguard" {}
```

This is a fairly barebones Terraform configuration file, but we will define the rest of the resources in the other two files. In `main.tf`, we declare and initialize the Chainguard Terraform provider. 

To create the `main.tf` file, run the following command.

```sh
cat > main.tf <<EOF
terraform {
  required_providers {
	chainguard = {
  	source = "chainguard/chainguard"
	}
  }
}

provider "chainguard" {}
EOF
```

Next, you can create the `sample.tf` file.


### `sample.tf`

`sample.tf` will create a couple of structures that will help us test out the identity in a workflow. 

This Terraform configuration consists of two main parts. The first part of the file will contain the following lines.

```
resource "chainguard_group" "user-group" {
  name    	= "example-group"
  description = <<EOF
	This group simulates an end-user group, which the github
	actions identity can interact with via the identity in
	actions.tf.
  EOF
}
```

This section creates a Chainguard Enforce IAM group named `example-group`, as well as a description of the group. This will serve as some data for the identity — which will be created by the `actions.tf` file — to access when we test it out later on.

The next section contains these lines, which create a sample policy and apply it to the `example-group` group created in the previous section.

```
resource "chainguard_policy" "cgr-trusted" {
  parent_id   = chainguard_group.user-group.id
  document = jsonencode({
	apiVersion = "policy.sigstore.dev/v1beta1"
	kind   	= "ClusterImagePolicy"
	metadata = {
  	name = "trust-any-cgr"
	}
	spec = {
  	images = [{
    	glob = "cgr.dev/**"
  	}]
  	authorities = [{
    	static = {
      	action = "pass"
    	}
  	}]
	}
  })
}
```

This policy trusts everything coming from the [Chainguard Registry](/chainguard/chainguard-images/registry/overview/). Because this policy is broadly permissive, it wouldn't be practical or secure to use in a real-world scenario. Like the example group, this policy serves as some data for the Buildkite pipeline to inspect after it assumes the Chainguard identity.

Create the `sample.tf` file with the following command.

```sh
cat > sample.tf <<EOF
resource "chainguard_group" "user-group" {
  name    	= "example-group"
  description = <<EOF
	This group simulates an end-user group, which the github
	actions identity can interact with via the identity in
	actions.tf.
  EOF
}

resource "chainguard_policy" "cgr-trusted" {
  parent_id   = chainguard_group.user-group.id
  document = jsonencode({
	apiVersion = "policy.sigstore.dev/v1beta1"
	kind   	= "ClusterImagePolicy"
	metadata = {
  	name = "trust-any-cgr"
	}
	spec = {
  	images = [{
    	glob = "cgr.dev/**"
  	}]
  	authorities = [{
    	static = {
      	action = "pass"
    	}
  	}]
	}
  })
}
EOF
```

Now you can move on to creating the last of our Terraform configuration files, `actions.tf`.


### `actions.tf`

The `actions.tf` file is what will actually create the identity for your GitHub Actions workflow to assume. The file will consist of four sections, which we'll go over one by one.

The first section creates the identity itself.

```
resource "chainguard_identity" "actions" {
  parent_id   = chainguard_group.user-group.id
  name    	= "github-actions"
  description = <<EOF
	This is an identity that authorizes the actions in this
	repository to assume to interact with chainctl.
  EOF

  claim_match {
	issuer  = "https://token.actions.githubusercontent.com"
	subject = "repo:<github_orgName>/<github_repoName>:ref:refs/heads/main"
  }
}
```

First, this section creates a Chainguard Identity tied to the `chainguard_group` created by the `sample.tf` file; namely, the `example-group` group. The identity is named `github-actions` and has a brief description.

The most important part of this section is the `claim_match`. When the GitHub Actions workflow tries to assume this identity later on, it must present a token matching the `issuer` and `subject` specified here in order to do so. The `issuer` is the entity that creates the token, while the `subject` is the entity (here, the Actions workflow) that the token represents.

In this case, the `issuer` field points to `https://token.actions.githubusercontent.com`, the issuer of OIDC tokens for GitHub Actions. The `subject` field, meanwhile, points to the `main` branch of an example GitHub repository. The GitHub documentation provides [several examples of `subject` claims](https://docs.github.com/en/actions/deployment/security-hardening-your-deployments/about-security-hardening-with-openid-connect#example-subject-claims) which you can refer to if you want to construct a `subject` claim specific to your needs. For the purposes of this guide, though, you will need to replace `<github_orgName>` and `<github_repoName>` with the name of your GitHub user or organization and the repository where your GitHub Actions workflow is hosted.

The next section will output the new identity's `id` value. This is a unique value that represents the identity itself.

```
output "actions-identity" {
  value = chainguard_identity.actions.id
}
```

The section after that looks up the `viewer` role. 

```
data "chainguard_roles" "viewer" {
  name = "viewer"
}
```

The final section grants this role to the identity on the `example-group`.

```
resource "chainguard_role-binding" "view-stuff" {
  identity = chainguard_identity.actions.id
  group	= chainguard_group.user-group.id
  role 	= data.chainguard_roles.viewer.items[0].id
}
```

Run the following command to create this file with each of these sections. Be sure to change the `subject` value to align with your own GitHub repository. For example, if your GitHub repository is located at `github.com/UserName/repo-name.git` you would set the `subject` value to `"repo:UserName/repo-name:ref:refs/heads/main"`.

```sh
cat > actions.tf <<EOF
resource "chainguard_identity" "actions" {
  parent_id   = chainguard_group.user-group.id
  name    	= "github-actions"
  description = <<EOF
	This is an identity that authorizes the actions in this
	repository to assume to interact with chainctl.
  EOF

  claim_match {
	issuer  = "https://token.actions.githubusercontent.com"
	subject = "repo:<github_orgName>/<github_repoName>:ref:refs/heads/main"
  }
}

output "actions-identity" {
  value = chainguard_identity.actions.id
}

data "chainguard_roles" "viewer" {
  name = "viewer"
}

resource "chainguard_rolebinding" "view-stuff" {
  identity = chainguard_identity.actions.id
  group	= chainguard_group.user-group.id
  role 	= data.chainguard_roles.viewer.items[0].id
}
EOF
```

Following that, your Terraform configuration will be ready. Now you can run a few `terraform` commands to create the resources defined in your `.tf` files.


## Creating Your Resources

First, run `terraform init` to initialize Terraform's working directory.

```sh
terraform init
```

Then run `terraform plan`. This will produce a speculative execution plan that outlines what steps Terraform will take to create the resources defined in the files you set up in the last section.

```sh
terraform plan
```

If the plan worked successfully and you're satisfied that it will produce the resources you expect, you can apply it. First, though, you'll need to log in to `chainctl` to ensure that Terraform can create the Chainguard resources.

```sh
chainctl auth login
```

Then apply the configuration.

```sh
terraform apply
```

Before going through with applying the Terraform configuration, this command will prompt you to confirm that you want it to do so. Enter `yes` to apply the configuration.

```
. . .

Plan: 3 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + actions-identity = (known after apply)

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: 
```

After pressing `ENTER`, the command will complete and will output an `actions-identity` value.

```
. . .

Apply complete! Resources: 3 added, 0 changed, 0 destroyed.

Outputs:

actions-identity = "<your actions identity>"
```

This is the identity's [UIDP (unique identity path)](/chainguard/chainguard-enforce/reference/events/#uidp-identifiers), which you configured the `actions.tf` file to emit in the previous section. Note this value down, as you'll need it to set up the GitHub Actions workflow you'll use to test the identity. If you need to retrieve this UIDP later on, though, you can always run the following `chainctl` command to obtain a list of the UIDPs of all your existing identities.

```sh
chainctl iam identities ls
```

You're now ready to create a GitHub Actions workflow which you'll use to test out this identity.


## Creating and Testing a GitHub Actions Workflow

To create a GitHub workflow, navigate to your repository in your browser and click the **Actions** tab. From there, find and click the **New workflow** button in the left-hand sidebar menu.

![Screenshot of part of the Actions tab in a GitHub repository. At the left of the "All workflows" list is a "New workflow" button.](actions-new-workflow.png)

Next, you'll be prompted to choose a workflow template. Because this tutorial includes the exact code you'll need for this workflow, you can skip this step by clicking the **set up a workflow yourself ➔** link.

You can name the workflow file whatever you like, although the default — `main.yaml` — will work for the purposes of this guide.

In the **Edit** textbox, add the following. Be sure to replace `<your actions identity>` with the `actions-identity` value produced by the previous `terraform apply` command.

```
name: Assume and Explore

on:
  workflow_dispatch: {}

jobs:
  assume-and-explore:
    name: actions assume example

    permissions:
      id-token: write

    runs-on: ubuntu-latest
    steps:

    - uses: chainguard-dev/actions/setup-chainctl@main
      with:
        identity: <your actions identity>

    - run: chainctl iam groups ls
```

This workflow is named `actions assume example`. The `permissions` block grants `write` permissions to the workflow for the `id-token` scope. [Per the GitHub Actions documentation](https://docs.github.com/en/actions/deployment/security-hardening-your-deployments/about-security-hardening-with-openid-connect#adding-permissions-settings), you **must** grant this permission in order for the workflow to be able to fetch an OIDC token. 

This workflow performs two actions:

* First, it assumes the identity you just created with Terraform.
* Second, the workflow runs the `chainctl iam groups ls` command to list the groups that the identity has access to.

Commit the workflow to your repository, then navigate back to the **Actions** tab. The **Assume and Explore** workflow will appear in the left-hand sidebar menu. Click on this, and then click the **Run workflow** button on the resulting page to execute the workflow.

![Screenshot of the "Assume and Explore" workflow, with the "Run workflow" button showing.](actions-run-workflow.png)

This indicates that the workflow can indeed assume the identity and interact with the `example-group` group.

![Screenshot showing the output of the "Assume and Explore" workflow.](actions-workflow-output.png)

If you'd like to experiment further with this identity and what the workflow can do with it, there are a few parts of this setup that you can tweak. For instance, if you'd like to give this identity different permissions you can change the role data source to the role you would like to grant.

```
data "chainguard_roles" "editor" {
  name = "editor"
}
```

You can also edit the workflow itself to change its behavior. For example, instead of inspecting the groups the identity has access to, you could have the workflow inspect the policies.

```
	- run: chainctl policies ls
```

Of course, the GitHub Actions workflow will only be able to perform certain actions on certain resources, depending on what kind of access you grant it.


## Removing Sample Resources

To remove the resources Terraform created, you can run the `terraform destroy` command.

```sh
terraform destroy
```

This will destroy the sample policy, the role-binding, and the identity created in this guide. However, you'll need to destroy the `example-group` group yourself with `chainctl`.

```sh
chainctl iam groups rm example-group
```

You can then remove the working directory to clean up your system.

```sh
rm -r ~/enforce-actions/
```

Following that, all of the example resources created in this guide will be removed from your system.


## Learn more

For more information about how assumable identities work in Chainguard Enforce, check out our [conceptual overview of assumable identities](/chainguard/chainguard-enforce/iam-groups/assumable-ids/). Additionally, the Terraform documentation includes a section on [recommended best practices](https://developer.hashicorp.com/terraform/cloud-docs/recommended-practices) which you can refer to if you'd like to build on this Terraform configuration for a production environment. Likewise, for more information on using GitHub Actions, we encourage you to check out the [official documentation on the subject](https://docs.github.com/en/actions).