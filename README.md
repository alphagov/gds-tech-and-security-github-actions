# GitHub CI/CD Workflows

This repository contains a collection of GitHub Actions workflow templates that can be used with various types of repositories to automate the build, test, and deployment of applications and infrastructure.

## Consuming Github Actions from this repo

Create the workflow job, and then include the action as a step:

`- uses: alphagov/gds-tech-and-security-github-actions/{directories}@{ref}`

E.g. for `pre-commit` action in `pre-commit/action.yml`:

```yml
...

jobs:
  pre-commit:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout repository
      uses: actions/checkout@v6

    - name: Run pre-commit
      uses: alphagov/gds-tech-and-security-github-actions/pre-commit@main
```

The `ref` can be a branch or a git tag, but for sensitive repositories it is **highly recommended** to use the commit SHA.

For each action usage guide, refer to each action `README.md` or `action.yml` file.

## Add new Github Actions

Actions are defined as [composites](https://docs.github.com/en/actions/tutorials/create-actions/create-a-composite-action). It allows each action the flexibility to be included across many workflows.

1. Install [pre-commit](https://pre-commit.com/).
1. Create a directory in the root of the repo, with the following convention: `<category [optional]>/<action_name>/action.yml`.

## Release a new Github Action version

1. Go to [Release Action](https://github.com/alphagov/gds-tech-and-security-github-actions/actions/workflows/_release.yml) workflow
1. Press the button `Run workflow` (only works for the `default` branch)
1. Input a name for the action. It must correspond to an existing directory in the root of the repo
1. (optional) specify a version. If not specified, it will auto-bump the latest tag
1. Press `Run workflow` again to trigger the workflow
1. The changelog is auto-generated once it is finished. If necessary, please modify it to include important changes

## Workflows examples

Examples of workflows using the composite actions:
- [Terraform Validation](./docs/terraform-validation.md) - Validate Terraform code
- [Terraform Module Release](./docs/terraform-module-release.md) - Release and publish Terraform modules

## How to setup Deployment Protection & Approval

The workflow templates in this repository are designed to be used with GitHub's deployment protection and approval feature. This feature allows you to require manual approval before a deployment can be executed. When merging to main branch we automatically use a 'production' environment, this can be configured with the repository setting to ensure all changes to this environment must be manually approved before applying the change.

### Steps to setup Deployment Protection & Approval

1. Go to the repository settings
2. Click on the `Branches` tab
3. Click on the `Add rule` button
4. In the `Branch name pattern` field, enter the branch name you want to protect (e.g. `main`)
5. Check the `Require pull request reviews before merging` checkbox
6. Check the `Require status checks to pass before merging` checkbox
7. Check the `Require branches to be up to date before merging` checkbox
8. Check the `Include administrators` checkbox
9. Click on `Environments` and choose the environment you want to protect (e.g. `production`)
10. Check the `Require reviewers` checkbox and select the reviewers you want to require approval from
11. Check the `Prevent self-review` checkbox

## License

This project is distributed under the [Apache License, Version 2.0](./LICENSE).
