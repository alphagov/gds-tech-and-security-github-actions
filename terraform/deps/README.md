# Terraform Dependencies Action

This composite action installs the necessary tools and dependencies for Terraform module validation and documentation.

It installs:

- Terraform
- TFLint
- Trivy
- Checkov
- terraform-docs

## Usage

```yaml
- uses: alphagov/gds-github-workflows/terraform/deps@<release_tag>
  with: [inputs]
```
