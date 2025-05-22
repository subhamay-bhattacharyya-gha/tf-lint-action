# TFLint Composite GitHub Action

![Release](https://github.com/subhamay-bhattacharyya-gha/tf-lint-action/actions/workflows/release.yaml/badge.svg)&nbsp;
![Commit Activity](https://img.shields.io/github/commit-activity/t/subhamay-bhattacharyya-gha/tf-lint-action)&nbsp;
![Last Commit](https://img.shields.io/github/last-commit/subhamay-bhattacharyya-gha/tf-lint-action)&nbsp;
![Release Date](https://img.shields.io/github/release-date/subhamay-bhattacharyya-gha/tf-lint-action)&nbsp;
![Repo Size](https://img.shields.io/github/repo-size/subhamay-bhattacharyya-gha/tf-lint-action)&nbsp;
![File Count](https://img.shields.io/github/directory-file-count/subhamay-bhattacharyya-gha/tf-lint-action)&nbsp;
![Issues](https://img.shields.io/github/issues/subhamay-bhattacharyya-gha/tf-lint-action)&nbsp;
![Top Language](https://img.shields.io/github/languages/top/subhamay-bhattacharyya-gha/tf-lint-action)&nbsp;
![Custom Endpoint](https://img.shields.io/endpoint?url=https://gist.githubusercontent.com/bsubhamay/cccdd0893e92625ba02f918c0506f334/raw/tf-lint-action.json?)

A GitHub Composite Action to lint Terraform code using [TFLint](https://github.com/terraform-linters/tflint). It supports version selection, plugin caching, output formatting, and logs results to the GitHub Action summary.

## 🚀 Features

- Installs and configures TFLint
- Optional caching for plugin directory
- Supports multiple output formats
- Writes results to GitHub Summary
- Fails workflow on lint issues

## 🔧 Inputs

| Name             | Description                                           | Required | Default     |
|------------------|-------------------------------------------------------|----------|-------------|
| `tflint-ver`     | TFLint version to install (e.g. `v0.52.0`)            | No       | `v0.52.0`   |
| `use-cache`      | Enable plugin cache (`true` or `false`)              | No       | `true`      |
| `tflint-format`  | TFLint output format (`default`, `json`, `compact`)  | No       | `compact`   |

## 📦 Example Usage

```yaml
name: Terraform Lint

on:
  push:
    paths:
      - '**.tf'
      - '.tflint.hcl'

jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Run TFLint
        uses: subhamay-bhattacharyya-gha/tf-lint-action@v1
        with:
          tflint-ver: v0.52.0
          use-cache: 'true'
          tflint-format: compact

```

## License

MIT
