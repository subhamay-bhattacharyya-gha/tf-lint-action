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
| `tflint-ver`      | TFLint version to install (e.g., `v0.52.0`). Uses organization variable `TF_LINT_VER` if not provided, falls back to `v0.52.0` | No       | `""` (uses `TF_LINT_VER` variable or `v0.52.0`) |
| `use-cache`       | Enable plugin caching (`true` or `false`)                                   | No       | `true`      |
| `tflint-format`   | TFLint output format (`default`, `json`, `compact`, `checkstyle`)           | No       | `compact`   |

---

## 📤 Behavior

- Prints all input parameters for debugging visibility
- Determines TFLint version using priority: explicit input → organization variable `TF_LINT_VER` → fallback to `v0.52.0`
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

### Using Repository Variable for TFLint Version

To use a repository variable for the default TFLint version:

1. Go to your repository Settings → Secrets and variables → Actions → Variables tab
2. Add a variable named `TFLINT_VER` with your desired version (e.g., `v0.53.0`)
3. Use the action without specifying `tflint-ver`:

```yaml
      - name: Run TFLint with Repository Variable
        uses: subhamay-bhattacharyya-gha/tf-lint-action@main
        with:
          terraform-dir: "infrastructure"
          use-cache: "true"
          tflint-format: "json"
          # tflint-ver will use the TFLINT_VER repository variable
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
          # This will automatically use: infra/aws/tf

  lint-gcp:
    runs-on: ubuntu-latest
    steps:
      - name: Lint GCP Infrastructure
        uses: subhamay-bhattacharyya-gha/tf-lint-action@main
        with:
          cloud-provider: "gcp"
          # This will automatically use: infra/gcp/tf

  lint-azure:
    runs-on: ubuntu-latest
    steps:
      - name: Lint Azure Infrastructure
        uses: subhamay-bhattacharyya-gha/tf-lint-action@main
        with:
          cloud-provider: "azure"
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

## 📝 Note on TFLINT_VER Repository Variable

The action supports using a repository variable `TFLINT_VER` to set a default TFLint version across your organization or repository. This provides flexibility in version management:

- **Priority Order**: Explicit `tflint-ver` input → `TFLINT_VER` repository variable → `v0.52.0` fallback
- **Setup**: Repository Settings → Secrets and variables → Actions → Variables tab → Add `TFLINT_VER`
- **Benefits**: Centralized version management, easy updates across multiple workflows, consistent tooling versions

When the repository variable is set, all workflows using this action will automatically use that version unless explicitly overridden with the `tflint-ver` input.

## License

MIT
