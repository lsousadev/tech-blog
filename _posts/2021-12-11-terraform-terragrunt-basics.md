---
layout: post
author: Lucas
title: Terraform + Terragrunt Basics
subtitle: Note Repository
categories: [terraform]
tags: [devops, terraform, terragrunt, iac]
---
<!--🔴 🟠 ⚫ ⚪ 🟣 🟢 🟡 🔵-->

## Basics

### Commands

- `terraform init` creates the Remote State: configures your local settings, downloads provider requirements, similar to activating a Python virtualenv
- `terraform plan` compares current state with desired state
- `terraform apply` makes the necessary API calls to create/delete/update the resources as needed, then polls them periodically until it is in its desired state
- `terraform graph` creates a dependency graph with all resources
- `terraform destroy` removes everything in the state file

### Variables

![Terraform Variables](/assets/images/2021-12-11-terraform-terragrunt-basics/tf-variables.png)

- since there is no value declared, if you run `terraform plan`, it will prompt for input
- you can declare a value via CLI with `terraform apply -var name=foo`, or inside terraform.tfvars: `name = "foo"`

### Dependencies

- reference a resource by their resource type and their name, eg: `aws_instance.example1`

![Terraform Dependencies](/assets/images/2021-12-11-terraform-terragrunt-basics/tf-dependencies.png)

- `aws_eip` requires an instance’s ID to be specified
- ID is an attribute of `aws_instance` that Terraform discovers, sets, and exports on creation
- terraform creates a dependency graph under the hood to figure out the order to create each resource based on dependencies, which can be viewed with `terraform graph`

### State

- by default, state is stored locally in `.tfstate` files, but state can be stored remotely (for collaboration) using S3

![Terraform Backend](/assets/images/2021-12-11-terraform-terragrunt-basics/tf-backend.png)

![Terraform Backend with Lock](/assets/images/2021-12-11-terraform-terragrunt-basics/tf-backend-lock.png)

- `terraform init` configures and connects to remote state storage
- most remote backends support locking and encryption
- locking: while using Terraform, you can request a lock and no one will be able to change the state
- lock is stored in DynamoDB
- encryption: terraform stores secrets in plaintext inside state file

## Modules

- collection of Terraform code that you can reuse, configure, and version control — like blueprints
- module = folder with Terraform files
- convention for Gruntwork modules:
    1. `vars.tf` - specify module inputs

        ![vars.tf Example](/assets/images/2021-12-11-terraform-terragrunt-basics/tf-vars.png)

    2. `main.tf` - create resources

        ![main.tf Example](/assets/images/2021-12-11-terraform-terragrunt-basics/tf-main.png)

    3. `outputs.tf` - specify outputs (can be used as input for other modules)

        ![outputs.tf Example](/assets/images/2021-12-11-terraform-terragrunt-basics/tf-outputs.png)

![Terraform Module Code](/assets/images/2021-12-11-terraform-terragrunt-basics/tf-module-code.png)

- a source can be a directory or URL
- reference module outputs as attributes of that module
- the `module` block needs to specify values for variables that don’t have default value — it cannot be passed via CLI and will not be prompted during plan/apply

### Example

![Terraform Module Directory](/assets/images/2021-12-11-terraform-terragrunt-basics/tf-module-directory.png)

- `./main.tf` - only three block types: terraform (version), provider “aws”, and module blocks w/ the module’s variables specified inside the block
- `./vars.tf` - declaration (no values) of variables used inside `main.tf`. Since there’s no `terraform.tfvars`, the values will need to be passed via CLI or prompt
- `./outputs.tf` - outputs values from a module’s output via `module.module_label.output_label`
- `./rails-module/main.tf` - has terraform (version) and resource blocks, no provider block needed
- `./rails-module/vars.tf` - variables used in `[./rails-module/main.tf](http://main.tf)` and declared here need to be addressed inside module block (see `./main.tf`)
- `./rails-module/outputs.tf` - outputs values from `./rails-module/main.tf` via `resource_type.resource_label_attribute`

### Module Community Standards

- `variables.tf` instead of vars.tf
- description property to each variable and output + a README file

## Best Practices

- `plan` before `apply`
- stage before prod
- isolated environments

    ![Terraform Module Environment](/assets/images/2021-12-11-terraform-terragrunt-basics/tf-module-env.png)

  - there is a special data source called `terraform_remote_state` that shares state between environments — you can read outputs from one environment into another
- use versioned modules
  - if modules are defined in a separate repository, different environments can use different versioned URLs

    ![Terraform Module Version](/assets/images/2021-12-11-terraform-terragrunt-basics/tf-module-version.png)

  - the `source` URL supports versioning using the `ref` parameter to point to a tag or a commit ID

    ![Terraform Module Source](/assets/images/2021-12-11-terraform-terragrunt-basics/tf-module-source.png)

- use remote state
  - you have to create the bucket yourself — enabling versioning is recommended
    - the lock table is automatically created
