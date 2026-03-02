# Deno CI Workflow

A reusable GitHub Actions workflow for Deno projects that provides complete CI/CD automation including testing, publishing to JSR and NPM, and creating GitHub releases.

## Features

- **Comprehensive CI checks**: Format, lint, type-check, and test your Deno projects
- **Dual publishing**: Automatically publish to both JSR (JavaScript Registry) and NPM
- **GitHub releases**: Automatically create releases with version tags
- **Smart publishing**: Only publishes if the version doesn't already exist
- **Configurable**: Customize Deno version, Node.js version, and skip specific publishing targets

## Usage

To use this reusable workflow in your Deno project, create a workflow file in your repository at `.github/workflows/ci.yml`:

```yaml
name: CI
on:
  push:
    branches:
      - main

jobs:
  ci-and-publish:
    uses: quentinadam/deno-ci-workflow/.github/workflows/ci-publish.yml@main
    secrets:
      NPM_TOKEN: ${{ secrets.NPM_TOKEN }}
```

Replace `quentinadam/deno-ci-workflow` with the actual repository path where this workflow is published.

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `deno-version` | Deno version to use | No | `v2.x` |
| `node-version` | Node.js version for NPM publishing | No | `24.x` |
| `skip-jsr` | Skip JSR publishing | No | `false` |
| `skip-npm` | Skip NPM publishing | No | `false` |

## Secrets

| Secret | Description | Required |
|--------|-------------|----------|
| `NPM_TOKEN` | NPM token for publishing | Only if publishing to NPM |

## Examples

### Basic Usage (JSR + NPM)

```yaml
name: CI
on:
  push:
    branches:
      - main

jobs:
  ci-and-publish:
    uses: quentinadam/deno-ci-workflow/.github/workflows/ci-publish.yml@main
    secrets:
      NPM_TOKEN: ${{ secrets.NPM_TOKEN }}
```

### Custom Deno and Node Versions

```yaml
name: CI
on:
  push:
    branches:
      - main

jobs:
  ci-and-publish:
    uses: quentinadam/deno-ci-workflow/.github/workflows/ci-publish.yml@main
    with:
      deno-version: v2.1.0
      node-version: '22.x'
    secrets:
      NPM_TOKEN: ${{ secrets.NPM_TOKEN }}
```

### JSR Only (Skip NPM)

```yaml
name: CI
on:
  push:
    branches:
      - main

jobs:
  ci-and-publish:
    uses: quentinadam/deno-ci-workflow/.github/workflows/ci-publish.yml@main
    with:
      skip-npm: true
```

### NPM Only (Skip JSR)

```yaml
name: CI
on:
  push:
    branches:
      - main

jobs:
  ci-and-publish:
    uses: quentinadam/deno-ci-workflow/.github/workflows/ci-publish.yml@main
    with:
      skip-jsr: true
    secrets:
      NPM_TOKEN: ${{ secrets.NPM_TOKEN }}
```

## Requirements

For this workflow to function correctly, your repository must:

1. **Have a `deno.json` file** with `name` and `version` fields:
   ```json
   {
     "name": "@scope/package-name",
     "version": "1.0.0"
   }
   ```

2. **Configure repository permissions** for publishing:
   - JSR: Ensure your repository has the necessary permissions for JSR publishing
   - NPM: Add an `NPM_TOKEN` secret to your repository settings

3. **Grant workflow permissions**:
   - Go to your repository Settings → Actions → General
   - Under "Workflow permissions", ensure workflows have permission to create releases

## Workflow Behavior

The workflow performs the following steps:

1. **CI Checks**: Runs format, lint, type-check, and tests
2. **Version Check**: Checks if the current version exists on JSR and NPM
3. **Dry Run**: Performs a dry-run publish to validate the package
4. **GitHub Release**: Creates a GitHub release with the version tag (e.g., `v1.0.0`)
5. **Publishing**: Publishes to JSR and/or NPM if:
   - The version doesn't already exist
   - The GitHub release was successfully created
   - The commit SHA matches the tag commit SHA

This ensures that publishing only happens on the exact commit that was tagged, preventing accidental publishes.

## License

MIT
