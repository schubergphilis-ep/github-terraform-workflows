# Reusable GitHub Workflows for Terraform modules

This repository provides reusable GitHub Actions workflows for Terraform-based projects. These workflows help standardize checks such as Terraform linting, formatting, policy validation, and commit message enforcement across repositories.

While primarily intended for use within our organization, these workflows are generic and open source — anyone can reuse them to improve CI consistency and quality in their own Terraform projects.

## Available Workflows

### 🧪 CI Workflow (`.github/workflows/ci.yaml`)

This workflow runs static analysis and unit tests on Terraform code:

- ✅ **Lint**
  - Checks Terraform formatting (`terraform fmt`)
  - Runs TFLint (`tflint`)
- 🔐 **Policy**
  - Runs Checkov (`checkov`) to validate security and compliance policies
- 🧪 **Unit Tests**
  - Runs `terraform test` if any files exist under `test/**`

### 📦 Commit Checks Workflow (`.github/workflows/commit.yaml`)

This workflow enforces [Conventional Commits](https://www.conventionalcommits.org/) on pull request titles:

- Validates PR titles using `amannn/action-semantic-pull-request`
- Leaves a helpful comment if the title does not follow the expected format
- Removes the comment automatically once the PR title is corrected

Supported commit types:

- `feat`
- `fix`
- `chore`
- `refactor`

## How to Use

To reuse these workflows in your own repository, add the following to your repo’s `.github/workflows/ci.yaml`:

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
    uses: schubergphilis-ep/github-terraform-workflows/.github/workflows/ci.yaml@main
    permissions:
      contents: write # 👈 Needed for reusable workflows to push commits
      security-events: write
    secrets: inherit
```

> [!NOTE]
> Change `main` to the latest release to use a versioned workflow, e.g. `v1`.

## Requirements

- Your Terraform code must be in the root of the repo.
- These workflows assume:
  - No backend config is required (`terraform init -backend=false`)
  - `test/**` contains unit tests (if applicable)
- To allow the commit check workflow to post PR comments, ensure `GITHUB_TOKEN` has write access to pull requests:

```yaml
permissions:
  pull-requests: write
```

## License

This repository is licensed under the Apache 2.0 License.

## Contributions

Contributions are welcome! Please open a pull request with clear commit messages and relevant test cases.
