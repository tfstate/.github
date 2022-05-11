# TFstate.dev ✨

[![Gitter](https://img.shields.io/gitter/room/tfstate/community)](https://gitter.im/tfstate/community) [![Open Collective backers and sponsors](https://img.shields.io/opencollective/all/tfstate)](https://opencollective.com/tfstate) [![GitHub Repo stars](https://img.shields.io/github/stars/tfstate/github-sls-rest-api?style=social)](https://github.com/tfstate/github-sls-rest-api)

**TFstate.dev** is a free [Terraform State Provider](https://www.terraform.io/language/settings/backends/http) and [Open Source Hosted Service](https://github.com/tfstate/github-sls-rest-api) for secure Terraform Remote State hosting using a GitHub Token, courtsey of [Scaffoldly](https://scaffold.ly)

Features:

- GitHub Token used for Authentication and Authorization to Terraform State
- Encrypted State in Amazon S3 using Amazon KMS
- State Locking
- Highly available [Hosted API](https://api.tfstate.dev/github/swagger.html) in AWS Lambda + API Gateway
- Plug and Play: Only a GitHub Token is needed to use TFstate.dev

✅ We do not store or save the provided GitHub token.

---

## Getting started 🚀

First, a GitHub token is needed. This can be a [Personal Access Token](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token), a [GitHub Actions Secret](https://docs.github.com/en/actions/security-guides/automatic-token-authentication), or any other form of [GitHub Oauth Token](https://github.blog/2021-04-05-behind-githubs-new-authentication-token-formats/). At a minimum, the token needs `repo:read` access for the configured repository.

➡ See our [example repository](https://github.com/tfstate/example).

To use TFstate.dev in Terraform, add the following [backend configuration](https://www.terraform.io/language/settings/backends/http) to Terraform:

```hcl
terraform {
  backend "http" {
    address        = "https://api.tfstate.dev/github/v1"
    lock_address   = "https://api.tfstate.dev/github/v1/lock"
    unlock_address = "https://api.tfstate.dev/github/v1/lock"
    lock_method    = "PUT"
    unlock_method  = "DELETE"
    username       = "{your-github-org}/{your-github-repo}"
  }
}
```

Then, Terraform can be configured to use the TFstate.dev backend using the GitHub token:

```bash
terraform init -backend-config="password={your-github-token}"
terraform plan
terraform apply
```

Alternatively, the `TF_HTTP_PASSWORD` environment variable can be set with the GitHub token:

```bash
export TF_HTTP_PASSWORD="{your-github-token}"
terraform init
terraform plan
terraform apply
```

⚠️ _Note_: If the token previously used has rotated or been changed (such as a [GitHub Actions Secret](https://docs.github.com/en/actions/security-guides/automatic-token-authentication)), add the `-reconfigure` flag.

---

## Additional Topics

The following sections detail specifics on how to use TFstate.dev on a regular basis.

Is there anything we haven't covered or do you need additional help? Message us on [Gitter](https://gitter.im/tfstate/community).

### Migrating to TFstate.dev

First, change your Terraform Configuration to use TFstate.dev as the Backend:

```hcl
terraform {
  backend "http" {
    address        = "https://api.tfstate.dev/github/v1"
    lock_address   = "https://api.tfstate.dev/github/v1/lock"
    unlock_address = "https://api.tfstate.dev/github/v1/lock"
    lock_method    = "PUT"
    unlock_method  = "DELETE"
    username       = "{your-github-org}/{your-github-repo}"
  }
}
```

Then initalize Terraform and migrate state:

```bash
export TF_HTTP_PASSWORD="{your-github-token}"
terraform init -migrate-state
terraform plan
```

### Using `terraform_remote_state`

Using the `terraform_remote_state` data resource is possible for state sharing.

Use the `TF_HTTP_PASSWORD` is required, as the GitHub token should NEVER be checked into Source Control.

➡ See our [example remote repository](https://github.com/tfstate/example-remote), which pulls state from [example](https://github.com/tfstate/example).

Example:

```hcl
data "terraform_remote_state" "example" {
  backend = "http"

  config = {
    address        = "https://api.tfstate.dev/github/v1"
    username       = "{your-github-org}/{other-github-repo}"
  }
}
```

Then plan and apply operations will work:

```bask
export TF_HTTP_PASSWORD={your-github-token}
terraform plan
terraform apply
```

### ADVANCED: Manually Unlocking State

If locking or unlocking state is necssary, use the `terraform force-unlock` command:

```bash
terraform force-unlock {lock-id}
```

---

## Contributing 💪

### Readme/Docs

We'd love PRs to make things clearer for users. For TFstate.dev there's two important READMEs:

- [This Readme](https://github.com/tfstate/.github/blob/main/profile/README.md) (Also the tfstate.dev website)
- [GitHub SLS REST API](https://github.com/tfstate/github-sls-rest-api/blob/main/README.md)

### Backends

- [Githb SLS REST API](https://github.com/tfstate/github-sls-rest-api)
  - This is the Open Source Backend for TFstate.dev
  - Follow the [Contributing Guidelines](https://github.com/tfstate/github-sls-rest-api/blob/main/CONTRIBUTING.md)

### Financial Support

TFstate.dev is provided as a free Hosted API and Open Source project to accelerate the proper usage of Terraform and minimize dependencies.

Scaffoldly generously hosts this API free-of-charge, but donations are graciously acceepted to help cover costs.

Visit [TFstate.dev on OpenCollective](https://opencollective.com/tfstate).

---

## About TFstate.dev 📚

TFstate.dev was created to simplify the number of prerequisties required to use Terraform. We desire to extend the functionality of a GitHub token beyond GitHub, and this allows adoption of Terraform along with secure state storage with the one-and-only prerequisite to generate a GitHub token and begin using Terraform.

Scaffoldly is a developer of various tooling designed to ease and simplify the adoption of DevOps for new project. TFstate.dev is one of Scaffoldly's many projects.

Want to get in touch? Message us on [Gitter](https://gitter.im/tfstate/community).

Copyright 2022 Scaffoldly LLC

![](https://sly-dev.scaffold.ly/auth/px?tfstate-profile)
