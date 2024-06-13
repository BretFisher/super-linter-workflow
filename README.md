# Super Linter Reusable Workflow Examples

The GitHub [Super-Linter](https://github.com/marketplace/actions/super-linter) project is a fantastic way to lint all your file types with a single GitHub Actions Workflow. A great way to implement it everywhere is to use GHA's [Reusable Workflows](https://docs.github.com/en/actions/learn-github-actions/reusing-workflows) (see below).

[My video walkthrough of this repository](https://youtu.be/aXZgQM8DqXg)

## Features of this custom Super-Linter example

- All the features of [Super-Linter](https://github.com/marketplace/actions/super-linter) in a *Reusable* Workflow.
- Bonus: Optionally turn off non-DevOps linters (CSS, JS, HTML, etc.) when you want to ignore code (in my case it's to ignore sample code I stick in DevOps projects).
- Bonus: I added Job steps to correctly determine which branch to diff files with (in the case of having multiple release branches).
- Bonus: Lints only changed files on a PR, but lints all files on merge to main (or any release) branch.

> ⚠️ **DO NOT call this reusable workflow directly**, rather, use it as a template repository and fork it for your own reusable workflow. I might change this workflow at anytime, based on new GHA features or learnings, and your calling workflow might break. ⚠️

## How to reuse this example as a *Reusable* Workflow

1. Fork this repository for you to customize your linters in a single location for your org/projects.
2. Add a new workflow to all your other repositories and paste in this YAML to call the central-repos reusable workflow.

```yaml
---
# template source: https://github.com/bretfisher/super-linter-workflow/blob/main/templates/call-super-linter.yaml
name: Lint Code Base

on:

  # run anytime a PR is merged to main or a direct push to main
  push:
    branches: [main]

  # run on any push to a PR branch
  pull_request:

# cancel any previously-started, yet still active runs of this workflow on the same branch
concurrency:
  group: ${{ github.ref }}-${{ github.workflow }}
  cancel-in-progress: true

jobs:
  call-super-linter:

    name: Call Super-Linter

    permissions:
      contents: read # clone the repo to lint
      statuses: write # read/write to repo custom statuses

    ### use Reusable Workflows to call my workflow remotely
    ### https://docs.github.com/en/actions/learn-github-actions/reusing-workflows
    ### you can also call workflows from inside the same repo via file path

    # FIXME: customize uri to point to your own reusable linter repository
    uses: bretfisher/super-linter-workflow/.github/workflows/reusable-super-linter.yaml@main

    ### Optional settings examples

    # with:
      ### For a DevOps-focused repository. Prevents some code-language linters from running
      ### defaults to false
      # devops-only: false

      ### A regex to exclude files from linting
      ### defaults to empty
      # filter-regex-exclude: html/.*

      ### For adjusting the runner's configuration
      ### defaults to '["ubuntu-latest"]'
      # runner-labels: '["self-hosted", "Linux"]'

```

## How to run Super-Linter locally

Option 1: Use [nektos/act](https://github.com/nektos/act) to run an existing GitHub Action workflow on your local repository clone.

Option 2: Use Docker to [run the Super-Linter image directly](https://github.com/github/super-linter/blob/main/docs/run-linter-locally.md).

Option 3: Pick the linter you want to run from Super-Linter, then install it locally to run manually. If you have a linter config, be sure to point the linter to `.github/linters/*`, and also realize that super-linter has default linter configs, that may change the linters behavior inside super-linter, with [templates listed here](https://github.com/github/super-linter/tree/main/TEMPLATES).

## This repository is part of my example DevOps repos on GitHub Actions

- [bretfisher/github-actions-templates](https://github.com/BretFisher/github-actions-templates) - Main repository
- (you are here) [bretfisher/super-linter-workflow](https://github.com/BretFisher/super-linter-workflow) - Reusable linter workflow
- [bretfisher/docker-build-workflow](https://github.com/BretFisher/docker-build-workflow)- Reusable docker build workflow
- [bretfisher/docker-ci-automation](https://github.com/BretFisher/docker-ci-automation) - Step by step video and example of a Docker CI workflow
- [My full list of container examples and tools](https://github.com/bretfisher)

## 🎉🎉🎉 Join my cloud native DevOps community 🎉🎉🎉

- [My Cloud Native DevOps Discord server](https://devops.fan)
- [My weekly YouTube Live show](https://www.youtube.com/@BretFisher)
- [My weekly newsletter](https://www.bretfisher.com/newsletter)
- [My courses and coupons](https://www.bretfisher.com/courses)
