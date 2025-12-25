# Terraform Lint with TFLint · Composite GitHub Action

![Release](https://github.com/subhamay-bhattacharyya-gha/tf-lint-action/actions/workflows/release.yaml/badge.svg)&nbsp;
![Commit Activity](https://img.shields.io/github/commit-activity/t/subhamay-bhattacharyya-gha/tf-lint-action)&nbsp;
![Last Commit](https://img.shields.io/github/last-commit/subhamay-bhattacharyya-gha/tf-lint-action)&nbsp;
![Release Date](https://img.shields.io/github/release-date/subhamay-bhattacharyya-gha/tf-lint-action)&nbsp;
![Repo Size](https://img.shields.io/github/repo-size/subhamay-bhattacharyya-gha/tf-lint-action)&nbsp;
![File Count](https://img.shields.io/github/directory-file-count/subhamay-bhattacharyya-gha/tf-lint-action)&nbsp;
![Issues](https://img.shields.io/github/issues/subhamay-bhattacharyya-gha/tf-lint-action)&nbsp;
![Top Language](https://img.shields.io/github/languages/top/subhamay-bhattacharyya-gha/tf-lint-action)&nbsp;
![Custom Endpoint](https://img.shields.io/endpoint?url=https://gist.githubusercontent.com/bsubhamay/cccdd0893e92625ba02f918c0506f334/raw/tf-lint-action.json?)

A GitHub Composite Action to lint Terraform code using [TFLint](https://github.com/terraform-linters/tflint). This action supports version control, plugin caching, output formatting, and writing results to the GitHub Actions summary. It fails the workflow on lint errors and is CI-friendly.

---

## 🚀 Features

- Installs and configures TFLint
- Supports custom TFLint versions with repository variable fallback
- Prints action inputs for debugging visibility
- Shows current directory and file listing for troubleshooting
- Optionally caches plugin directory
- Supports multiple output formats (`default`, `compact`, `json`, `checkstyle`)
- Logs results in the GitHub Actions Summary
- Fails the workflow on lint issues
- Allows release-tag-based checkout

---

## 🔧 Inputs

| Name              | Description                                                                 | Required | Default     |
|-------------------|-----------------------------------------------------------------------------|----------|-------------|
| `terraform-dir`   | Relative path to the directory containing Terraform configuration files. Can be combined with cloud-provider for multi-cloud setups | No       | `tf`        |
| `cloud-provider`  | Cloud provider for multi-cloud setups (`aws`, `gcp`, `azure`). When specified, modifies terraform-dir to `infra/<cloud-provider>/tf` | No       | `""` (disabled) |
| `release-tag`     | Git release tag to check out. If omitted, the current branch or tag is used | No       | `""`        |
| `tflint-ver`      | TFLint version to install (e.g., `v0.52.0`). Pass organization variable `TF_LINT_VER` from calling workflow | No       | `v0.52.0` |
| `use-cache`       | Enable plugin caching (`true` or `false`)                                   | No       | `true`      |
| `tflint-format`   | TFLint output format (`default`, `json`, `compact`, `checkstyle`)           | No       | `compact`   |

---

## 📤 Behavior

- Prints all input parameters for debugging visibility
- Uses the provided TFLint version (defaults to `v0.52.0` if not specified)
- Checks out the repo at the specified release tag or fallback to `github.ref_name`
- Caches `.tflint.d/plugins` if enabled
- Initializes TFLint and installs plugins
- Shows current working directory and available files for troubleshooting
- Runs TFLint in the specified format
- Displays issues in the GitHub Actions Summary
- Fails the step if lint issues are found

---

## 📦 Example Usage

### Basic Usage

```yaml
name: Terraform Lint

on:
  push:
    paths:
      - "**/*.tf"
      - ".tflint.hcl"

jobs:
  lint:
    runs-on: ubuntu-latest
    steps:

      - name: Run TFLint
        uses: subhamay-bhattacharyya-gha/tf-lint-action@main
        with:
          terraform-dir: "tf"
          release-tag: ""
          tflint-ver: "v0.52.0"
          use-cache: "true"
          tflint-format: "compact"
```

### Using Organization Variable for TFLint Version

To use an organization variable for the default TFLint version, pass it from your workflow:

1. Set up the organization variable:
   - Go to your organization Settings → Secrets and variables → Actions → Variables tab
   - Add a variable named `TF_LINT_VER` with your desired version (e.g., `v0.53.0`)

2. Pass the organization variable to the action in your workflow:

```yaml
      - name: Run TFLint with Organization Variable
        uses: subhamay-bhattacharyya-gha/tf-lint-action@main
        with:
          terraform-dir: "infrastructure"
          tflint-ver: ${{ vars.TF_LINT_VER || 'v0.52.0' }}
          use-cache: "true"
          tflint-format: "json"
```

### Multi-Cloud Implementation

For multi-cloud infrastructure setups, use the `cloud-provider` input to automatically target the correct directory structure:

```yaml
name: Multi-Cloud Terraform Lint

on:
  push:
    paths:
      - "infra/**/*.tf"
      - ".tflint.hcl"

jobs:
  lint-aws:
    runs-on: ubuntu-latest
    steps:
      - name: Lint AWS Infrastructure
        uses: subhamay-bhattacharyya-gha/tf-lint-action@main
        with:
          cloud-provider: "aws"
          tflint-ver: ${{ vars.TF_LINT_VER || 'v0.52.0' }}
          # This will automatically use: infra/aws/tf

  lint-gcp:
    runs-on: ubuntu-latest
    steps:
      - name: Lint GCP Infrastructure
        uses: subhamay-bhattacharyya-gha/tf-lint-action@main
        with:
          cloud-provider: "gcp"
          tflint-ver: ${{ vars.TF_LINT_VER || 'v0.52.0' }}
          # This will automatically use: infra/gcp/tf

  lint-azure:
    runs-on: ubuntu-latest
    steps:
      - name: Lint Azure Infrastructure
        uses: subhamay-bhattacharyya-gha/tf-lint-action@main
        with:
          cloud-provider: "azure"
          tflint-ver: ${{ vars.TF_LINT_VER || 'v0.52.0' }}
          # This will automatically use: infra/azure/tf
```

**Expected Directory Structure for Multi-Cloud:**
```
repository/
├── infra/
│   ├── aws/
│   │   └── tf/
│   │       ├── main.tf
│   │       └── variables.tf
│   ├── gcp/
│   │   └── tf/
│   │       ├── main.tf
│   │       └── variables.tf
│   └── azure/
│       └── tf/
│           ├── main.tf
│           └── variables.tf
└── .tflint.hcl
```

---

## 📝 TFLint Version Management Best Practices

**Recommended Approach**: Use the organization variable `TF_LINT_VER` to maintain consistent TFLint versions across all repositories in your organization. This should be your default approach:

```yaml
tflint-ver: ${{ vars.TF_LINT_VER || 'v0.52.0' }}
```

**Override When Necessary**: In cases where you need to use a specific TFLint version that differs from the organization standard (e.g., testing a new version, compatibility requirements), hardcode the version:

```yaml
tflint-ver: "v0.53.0"  # Override organization variable for specific needs
```

This approach ensures organization-wide consistency while allowing flexibility for special cases.

---

## 📝 Note on TF_LINT_VER Organization Variable

The action supports using an organization variable `TF_LINT_VER` for centralized version management across your organization. Since composite actions cannot directly access `vars` context, you need to pass the organization variable from your calling workflow:

- **Setup**: Organization Settings → Secrets and variables → Actions → Variables tab → Add `TF_LINT_VER`
- **Usage**: Pass the variable using `tflint-ver: ${{ vars.TF_LINT_VER || 'v0.52.0' }}` in your workflow
- **Benefits**: Organization-wide version management, easy updates across all repositories, consistent tooling versions
- **Fallback**: Use `|| 'v0.52.0'` to provide a fallback version if the organization variable is not set

This approach ensures all workflows across your organization use the same TFLint version while maintaining flexibility for individual overrides.

## License

MIT
