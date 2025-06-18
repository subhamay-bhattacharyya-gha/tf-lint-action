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
- Supports custom TFLint versions
- Optionally caches plugin directory
- Supports multiple output formats (`default`, `compact`, `json`, `checkstyle`)
- Logs results in the GitHub Actions Summary
- Fails the workflow on lint issues
- Allows release-tag-based checkout

---

## 🔧 Inputs

| Name              | Description                                                                 | Required | Default     |
|-------------------|-----------------------------------------------------------------------------|----------|-------------|
| `terraform-dir`   | Relative path to the directory containing Terraform configuration files     | No       | `tf`        |
| `release-tag`     | Git release tag to check out. If omitted, the current branch or tag is used | No       | `""`        |
| `tflint-ver`      | TFLint version to install (e.g., `v0.52.0`)                                 | No       | `v0.52.0`   |
| `use-cache`       | Enable plugin caching (`true` or `false`)                                   | No       | `true`      |
| `tflint-format`   | TFLint output format (`default`, `json`, `compact`, `checkstyle`)           | No       | `compact`   |

---

## 📤 Behavior

- Checks out the repo at the specified release tag or fallback to `github.ref_name`
- Caches `.tflint.d/plugins` if enabled
- Initializes TFLint and installs plugins
- Runs TFLint in the specified format
- Displays issues in the GitHub Actions Summary
- Fails the step if lint issues are found

---

## 📦 Example Usage

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

## License

MIT
