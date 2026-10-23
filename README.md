# Terraform VCS-Driven Infrastructure with HCP Terraform

## Index

- [Overview](#overview)
- [Technical Skills Demonstrated](#technical-skills-demonstrated)
- [How the Workflow Works](#how-the-workflow-works)
- [Pull Request Workflow](#pull-request-workflow)
- [Repository Files](#repository-files)
- [What I Tested](#what-i-tested)
- [Source](#source)
- [Scope](#scope)

## Overview

I created this repository while learning how Terraform can be integrated with GitHub and HCP Terraform, formerly known as Terraform Cloud, to support a VCS-driven infrastructure workflow.

The focus of this project was using GitHub as the source for infrastructure configuration and connecting it to HCP Terraform for remote Terraform runs. I worked with commits, branches, pull requests, workspace variables, remote state, and speculative plans while deploying a small AWS EC2 environment.

The infrastructure is intentionally simple so I could focus on understanding how GitHub, Terraform, HCP Terraform, and AWS work together.

## Technical Skills Demonstrated

- **Terraform and HCL:** Infrastructure as Code, variables, outputs, providers, resource configuration, tagging, and lifecycle behavior
- **HCP Terraform:** Remote execution, *VCS-driven workspaces*, workspace variables, remote state, and speculative plans
- **AWS:** EC2 provisioning, security groups, instance user data, and AWS provider authentication
- **GitOps:** Using version-controlled Terraform configuration to drive infrastructure changes
- **VCS integration:** Connecting a GitHub repository to HCP Terraform so repository changes can initiate remote Terraform runs
- **Credential management:** Keeping AWS credentials outside of source control and supplying them through the HCP Terraform workspace
- **Change review:** Using pull requests and speculative plans to preview infrastructure changes before merging them

## How the Workflow Works

I connected this GitHub repository to an HCP Terraform VCS-driven workspace. The workspace tracks the repository's `main` branch.

When I change the Terraform configuration, I commit the change and push it to GitHub. HCP Terraform detects the new commit, retrieves the updated configuration, and starts a remote Terraform run. After I review and approve the plan, HCP Terraform applies the change to the AWS environment.

```text
1. I edit the Terraform files locally
                    |
                    v
2. I commit the changes with Git
                    |
                    v
3. I push the commit to the main branch on GitHub
                    |
                    v
4. GitHub notifies the connected HCP Terraform workspace
                    |
                    v
5. HCP Terraform retrieves the updated configuration
                    |
                    v
6. HCP Terraform runs terraform plan remotely
                    |
                    v
7. I review and approve the proposed changes
                    |
                    v
8. HCP Terraform runs terraform apply
                    |
                    v
9. Terraform creates or updates the AWS infrastructure
```

For this VCS-driven workflow, I do not run `terraform apply` from my local Cloud9 environment. My local work consists of editing the files and using Git to commit and push them. HCP Terraform runs the Terraform commands remotely.

## Pull Request Workflow

I also tested how infrastructure changes can be reviewed before they are merged into `main`.

```text
Feature branch
      |
      v
Commit and push the proposed change
      |
      v
Open a pull request into main
      |
      v
HCP Terraform runs a speculative plan
      |
      v
Review the expected infrastructure changes
      |
      v
Merge the pull request after review
      |
      v
The new commit on main triggers a real Terraform run
```

The speculative plan previews the effect of the proposed change without applying it to AWS. This provides an opportunity to review the expected infrastructure impact as part of the pull-request process.

## Repository Files

- **`main.tf`**: Contains the main Terraform configuration, including the AWS provider, HCP Terraform workspace connection, EC2 instance, security group, and resource tags.
- **`variables.tf`**: Defines the input variables expected by the Terraform configuration.
- **`terraform.tfvars`**: Supplies repository-level values such as the application environment and application version.
- **`outputs.tf`**: Defines the information Terraform returns after provisioning, including values used to locate and access the EC2 instance.
- **`user_data.sh`**: Runs when the EC2 instance starts and configuresvided to the remote run through HCP Terraform workspace environment variables.

Values such as the assigned username and AWS region are provided through Terraform workspace variables. Application settings such as the environment and application version are defined in `terraform.tfvars`.

## What I Tested

I updated the application version in `terraform.tfvars`, committed the change, and pushed it to `main`. The push triggered an HCP Terraform run that planned and applied the updated AWS deployment.

Because the application version is included in the EC2 instance's user data, changing the version caused Terraform to replace the instance. The replacement allowed the startup script to run again with the new version.

I also created a feature branch, added a resource tag, pushed the branch, and opened a pull request against `main`. HCP Terraform generated a speculative plan that showed the expected infrastructure changes before the branch was merged.

These exercises helped me understand how Infrastructure as Code can use the same version-control and review practices commonly used for application code.

## Source

I completed this project to work towards my personal goal of "up-skilling" in Terraform. I followed the Lab 12 materials available in the [jwkidd3/terraform_v2 training repository](https://github.com/jwkidd3/terraform_v2).

The Terraform files in this repository are based on the training materials from that lab. I used them to practice configuring a VCS-driven workflow across GitHub, HCP Terraform, Terraform, and AWS.

## Scope

This repository represents a training environment rather than a production architecture. The AWS infrastructure is intentionally limited to a small EC2 deployment so I could concentrate on Terraform, GitHub integration, HCP Terraform remote execution, Git-based change management, and pull-request planning.
