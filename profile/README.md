# TFstate.dev ✨

**TFstate.dev** is a free [Terraform State Provider](https://www.terraform.io/language/settings/backends/http) and [Open Source](https://github.com/tfstate/github-sls-rest-api) Service for secure Terraform Remote State hosting using a GitHub Token, courtsey of [Scaffoldly](https://scaffold.ly)

Features:

- Zero Bootstrapping: any valid GitHub token can access this API
- Encrypted State + Locking
- State Locking
- Highly available [Hosted API](https://api.tfstate.dev/github/swagger.html) in AWS Lambda + API Gateway

✅ We do not store or save the provided GitHub token.

---

## Getting started 🚀

First, a GitHub token is needed. This can be a [Personal Access Token](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token), a [GitHub Actions Secret](https://docs.github.com/en/actions/security-guides/automatic-token-authentication), or any other form of [GitHub Oauth Token](https://github.blog/2021-04-05-behind-githubs-new-authentication-token-formats/). At a minimum, the token needs `repo:read` access for the configured repository.

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

_Note_: If the token used has rotated or been changed (such as a [GitHub Actions Secret](https://docs.github.com/en/actions/security-guides/automatic-token-authentication)), add the `-reconfigure` flag.

### Additional Topics

- [Migrating Existing State](migrating.md)
- [Using `terraform_remote_state`](remote_state.md)
- [Workspaces](workspaces.md)
- [Unlocking State](unlocking.md)

### Premium Features (Beta)

- [Planfile Hosting](planfile_hosting.md)

Need additional help? Message us on [Gitter](https://gitter.im/tfstate/community).

---

## Contributing 💪

TODO

---

## About TFstate.dev 📚

TFstate.dev was created to simplify the number of prerequisties required to use Terraform. We desire to extend the functionality of a GitHub token beyond GitHub, and this allows adoption of Terraform along with secure state storage with the one-and-only prerequisite to generate a GitHub token and begin using Terraform.

Scaffoldly is a developer of various tooling designed to ease and simplify the adoption of DevOps for new project. TFstate.dev is one of Scaffoldly's many projects.

Want to get in touch? Message us on [Gitter](https://gitter.im/tfstate/community).

Copyright 2022 Scaffoldly LLC
