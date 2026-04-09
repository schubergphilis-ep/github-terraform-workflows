# Reusable GitHub Workflows for Terraform Modules and Providers

This repository provides reusable GitHub Actions workflows for Terraform-based projects. It covers both **Terraform modules** and **Terraform providers**, standardizing CI checks, documentation generation, policy validation, and release automation.

While primarily intended for use within our organization, these workflows are generic and open source — anyone can reuse them.

## Available Workflows

| Workflow | File | Purpose |
|---|---|---|
| Module CI | `module-ci.yaml` | Lint, docs, policy, and unit tests for Terraform modules |
| Module Release | `module-release.yaml` | Automated releases via release-please for modules |
| Provider CI | `provider-ci.yaml` | Build, test, vet, lint, and config validation for Go-based providers |
| Provider Release | `provider-release.yaml` | Automated releases via release-please + GoReleaser for providers |
| Commit Checks | `commit.yaml` | Conventional Commits enforcement on PR titles (shared) |

---

## Module Workflows

### Module CI (`module-ci.yaml`)

Runs static analysis, documentation generation, and unit tests on Terraform code:

- **Lint** — Checks formatting (`terraform fmt`) and runs TFLint
- **Docs** — Generates documentation into `README.md` using `terraform-docs` (root + `modules/` subdirectory)
- **Policy** — Runs Checkov for security and compliance checks
- **Unit Tests** — Runs `terraform test` if files exist under `test/` or `tests/`

### Module Release (`module-release.yaml`)

Automates versioning and releases using [release-please](https://github.com/googleapis/release-please):

- Creates a Release PR with changelog when conventional commits are pushed to main
- When the Release PR is merged, creates a git tag and GitHub Release
- Uses `release-type: terraform-module`

### Module Setup

Add these workflow files to your module repository under `.github/workflows/`:

**`.github/workflows/ci.yaml`**
```yaml
name: CI

on:
  pull_request:
    types: [opened, synchronize, reopened, edited]

jobs:
  Commit:
    uses: schubergphilis-ep/github-terraform-workflows/.github/workflows/commit.yaml@main
    permissions:
      contents: read
      pull-requests: write
    secrets: inherit

  Terraform:
    uses: schubergphilis-ep/github-terraform-workflows/.github/workflows/module-ci.yaml@main
    permissions:
      contents: write
      security-events: write
    secrets: inherit
```

**`.github/workflows/release.yaml`**
```yaml
name: Release

on:
  push:
    branches: [main]

jobs:
  Release:
    uses: schubergphilis-ep/github-terraform-workflows/.github/workflows/module-release.yaml@main
    permissions:
      contents: write
      issues: write
      pull-requests: write
    secrets: inherit
```

Also add to your repository:
- `.github/release-please-config.json` — see [`examples/release-please-config-module.json`](examples/release-please-config-module.json)
- `.github/.release-please-manifest.json` — initially `{ ".": "0.0.0" }`

> [!NOTE]
> Pin `@main` to a specific release tag (e.g. `@v2`) for stability.

### Module Requirements

- Terraform code must be in the root of the repo
- No backend config required (`terraform init -backend=false`)
- `test/` or `tests/` directory for unit tests (if applicable)

---

## Provider Workflows

### Provider CI (`provider-ci.yaml`)

Runs Go-specific checks and validates Terraform Registry prerequisites:

- **Validate** — Checks repo name follows `terraform-provider-*` convention, validates `terraform-registry-manifest.json`
- **Build** — Compiles with `CGO_ENABLED=0` (required for HCP Terraform)
- **Test** — Runs `go test ./...`
- **Vet** — Runs `go vet ./...`
- **Lint** — Runs `golangci-lint`
- **GoReleaser Check** — Validates `.goreleaser.yml` configuration

All six jobs run in parallel.

**Inputs:**

| Input | Default | Description |
|---|---|---|
| `go-version` | _(from go.mod)_ | Go version override |
| `golangci-lint-version` | `latest` | golangci-lint version |

### Provider Release (`provider-release.yaml`)

Combines [release-please](https://github.com/googleapis/release-please) for versioning with [GoReleaser](https://goreleaser.com/) for building and publishing:

1. **On push to main** — release-please creates/updates a Release PR with a changelog
2. **When a Release PR is merged** — release-please creates a tag and GitHub Release, then GoReleaser automatically runs in the same workflow to build cross-platform binaries, sign checksums with GPG, and upload all artifacts to the release

The release includes:
- Compiled binaries for darwin/linux/windows/freebsd (amd64, arm64, arm, 386)
- `SHA256SUMS` file with GPG detached signature (`.sig`)
- `terraform-registry-manifest.json` (renamed with version)

The Terraform Registry webhook automatically picks up new releases.

**Secrets:**

| Secret | Required | Description |
|---|---|---|
| `gpg_private_key` | Yes | ASCII-armored GPG private key (RSA or DSA, not ECC) |
| `gpg_passphrase` | Yes | Passphrase for the GPG key |

### Provider Setup

#### 1. Repository Prerequisites

- Repository name **must** follow `terraform-provider-{NAME}` convention
- Add `terraform-registry-manifest.json` to the repo root:
  ```json
  {
    "version": 1,
    "metadata": {
      "protocol_versions": ["6.0"]
    }
  }
  ```
  Use `["5.0"]` for providers built with Terraform Plugin SDK v2, `["6.0"]` for Plugin Framework.

- Add `.goreleaser.yml` to the repo root — see [`examples/.goreleaser.yml`](examples/.goreleaser.yml)

#### 2. GPG Key Setup

The Terraform Registry requires GPG-signed checksums. To set this up:

1. Generate an RSA GPG keypair (ECC is not supported):
   ```bash
   gpg --full-generate-key  # Select RSA
   ```
2. Export and add the **public key** to [Terraform Registry > User Settings > Signing Keys](https://registry.terraform.io/settings/gpg-keys):
   ```bash
   gpg --armor --export "your-key-id"
   ```
3. Export the **private key** and add it as a GitHub Actions secret (`GPG_PRIVATE_KEY`):
   ```bash
   gpg --armor --export-secret-keys "your-key-id"
   ```
4. Add the passphrase as a GitHub Actions secret (`GPG_PASSPHRASE`)

#### 3. Workflow Files

Add these to your provider repository under `.github/workflows/`:

**`.github/workflows/ci.yaml`**
```yaml
name: CI

on:
  pull_request:
    types: [opened, synchronize, reopened, edited]

jobs:
  Commit:
    uses: schubergphilis-ep/github-terraform-workflows/.github/workflows/commit.yaml@main
    permissions:
      contents: read
      pull-requests: write
    secrets: inherit

  Provider:
    uses: schubergphilis-ep/github-terraform-workflows/.github/workflows/provider-ci.yaml@main
    permissions:
      contents: read
    secrets: inherit
```

**`.github/workflows/release.yaml`**
```yaml
name: Release

on:
  push:
    branches: [main]

jobs:
  Release:
    uses: schubergphilis-ep/github-terraform-workflows/.github/workflows/provider-release.yaml@main
    permissions:
      contents: write
      issues: write
      pull-requests: write
    secrets:
      gpg_private_key: ${{ secrets.GPG_PRIVATE_KEY }}
      gpg_passphrase: ${{ secrets.GPG_PASSPHRASE }}
```

Also add to your repository:
- `.github/release-please-config.json` — see [`examples/release-please-config-provider.json`](examples/release-please-config-provider.json)
- `.github/.release-please-manifest.json` — initially `{ ".": "0.0.0" }`

> [!NOTE]
> Pin `@main` to a specific release tag (e.g. `@v2`) for stability.

#### 4. Terraform Registry Publishing

Register your provider at [registry.terraform.io](https://registry.terraform.io) under Publish > Provider. The registry installs a webhook that automatically ingests new releases.

---

## Commit Checks (`commit.yaml`)

Enforces [Conventional Commits](https://www.conventionalcommits.org/) on PR titles. Shared by both module and provider repositories.

Supported types: `feat`, `fix`, `chore`, `refactor`

Posts an explanatory comment if the PR title is invalid, and removes it once corrected.

---

## Examples

The [`examples/`](examples/) directory contains reference configurations:

| File | Description |
|---|---|
| `module-ci.yaml` | Caller workflow for module CI |
| `module-release.yaml` | Caller workflow for module releases |
| `provider-ci.yaml` | Caller workflow for provider CI |
| `provider-release.yaml` | Caller workflow for provider releases |
| `.goreleaser.yml` | GoReleaser config for providers |
| `release-please-config-module.json` | release-please config for modules |
| `release-please-config-provider.json` | release-please config for providers |

## License

This repository is licensed under the Apache 2.0 License.

## Contributions

Contributions are welcome! Please open a pull request with a [Conventional Commits](https://www.conventionalcommits.org/) title.
