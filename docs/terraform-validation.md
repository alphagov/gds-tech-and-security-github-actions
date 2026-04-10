# Terraform Validation with pre-commit

This document describes how to set up a GitHub Actions workflow to validate your Terraform code using `pre-commit`.

## Example Workflow

Create a new workflow file in your repository (e.g. `.github/workflows/terraform-validate.yml`) with the following content:

```yaml
name: Terraform Validate

on:
  push:
    branches:
    - main
  pull_request:
    branches:
    - main

permissions:
  contents: read

jobs:
  terraform-pre-commit:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout repository
      uses: actions/checkout@v6

    - name: Install terraform and dependencies
      uses: alphagov/gds-tech-and-security-github-actions/terraform/deps@<release_tag>

    - name: Run pre-commit
      uses: alphagov/gds-tech-and-security-github-actions/pre-commit@<release_tag>
```

Ensure you have a `.pre-commit-config.yaml` file in your repository root with hooks defined.

## Description

- **`terraform/deps`**: This action installs Terraform and its dependencies
- **`pre-commit`**: This action runs runs pre-commit hooks against your code
