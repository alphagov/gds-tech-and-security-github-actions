# Terraform Module Release

This GitHub Actions workflow template ([terraform-module-release.yml](../.github/workflows/terraform-module-release.yml)) can be used with Terraform repositories to automatically create a GitHub release when a version has been tagged.

The workflow supports:

- **Single-module repositories** with standard `v*` tags
- **Multi-module repositories** with independent versioning using `<module-name>/v*` tags
- Two modes for release notes generation:
  1. **Default Mode**: Uses GitHub's automatic release notes generation
  2. **Git Cliff Mode**: Uses [git-cliff](https://github.com/orhun/git-cliff) for customizable changelog generation

## Tag Naming Convention

### Single-Module Repository

For repositories containing a single Terraform module, use standard semantic versioning tags:

```bash
git tag v1.0.0
git push origin v1.0.0
```

Create a new workflow file in your Terraform repository (e.g. `.github/workflows/release.yml`) with the below contents:

```yml
name: Release

on:
  push:
    tags:
      - "v*"

permissions:
  contents: write

jobs:
  release:
    uses: alphagov/gds-tech-and-security-github-actions/.github/workflows/terraform-module-release.yml@<commit SHA> # v<semantic version>
    name: GitHub Release
```

### Multi-Module Repository

For repositories containing multiple Terraform modules, use a tag format that includes the module name:

`<module-name>/v<version>`

Examples:

```bash
# Release azurerm-conditional-access at version 1.0.0
git tag azurerm-conditional-access/v1.0.0
git push origin azurerm-conditional-access/v1.0.0

# Release terraform-azurerm-another-module at version 2.1.0
git tag terraform-azurerm-another-module/v2.1.0
git push origin terraform-azurerm-another-module/v2.1.0
```

This creates GitHub releases with titles like:
- `azurerm-conditional-access v1.0.0`
- `terraform-azurerm-another-module v2.1.0`

For repositories with multiple modules, create a workflow that extracts the module name from the tag:

```yml
name: Release

on:
  push:
    tags:
      - "*/v*"  # Matches tags like azurerm-conditional-access/v1.0.0

permissions:
  contents: write

jobs:
  extract-module:
    name: Extract Module Info
    runs-on: ubuntu-latest
    outputs:
      module-name: ${{ steps.extract.outputs.module }}
      version: ${{ steps.extract.outputs.version }}
    steps:
      - name: Extract module and version from tag
        id: extract
        run: |
          TAG="${GITHUB_REF#refs/tags/}"
          MODULE="${TAG%/v*}"
          VERSION="${TAG#*/}"
          echo "module=${MODULE}" >> "$GITHUB_OUTPUT"
          echo "version=${VERSION}" >> "$GITHUB_OUTPUT"
          echo "Module: ${MODULE}, Version: ${VERSION}"

  release:
    uses: alphagov/gds-tech-and-security-github-actions/.github/workflows/terraform-module-release.yml@<commit SHA> # v<semantic version>
    name: GitHub Release
    needs: extract-module
    with:
      module-name: ${{ needs.extract-module.outputs.module-name }}
      version: ${{ needs.extract-module.outputs.version }}
```

## Inputs

### Optional Inputs

| Input | Default | Description |
|-------|---------|-------------|
| `enable-cliff` | `false` | Indicates if the repository uses git-cliff for changelog generation. Requires a `.cliff/cliff.toml` configuration file in your repository. |
| `module-name` | `""` | The name of the module being released (for multi-module repos) |
| `version` | `""` | The version being released (e.g., `v1.0.0`) |

## Consuming Released Modules

Once a module is released, users can reference it in their Terraform configurations using the GitHub source with a ref:

```hcl
# Single-Module Repository
module "example" {
  source = "git::https://github.com/org/terraform-module-repo.git?ref=v1.0.0"
}

# Multi-Module Repository
module "conditional_access" {
  source = "git::https://github.com/org/terraform-modules-repo.git//azurerm-conditional-access?ref=azurerm-conditional-access/v1.0.0"
}
```

Note: When using modules from a monorepo, the `ref` should match the full tag (including the module name prefix), and the path after `//` specifies the subdirectory containing the module.

**Note:** This template may change over time, so it is recommended that you point to a tagged version rather than the main branch.
