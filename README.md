# Super Linter Reusable Workflow Examples

The GitHub [Super-Linter](https://github.com/marketplace/actions/super-linter) project is a fantastic way to lint all your file types with a single GitHub Actions Workflow. A great way to implement it everywhere is to use GHA's [Reusable Workflows](https://docs.github.com/en/actions/learn-github-actions/reusing-workflows) (see below).

[My video walkthrough of this repository](https://youtu.be/aXZgQM8DqXg)

## Features of this custom Super-Linter example

- All the features of [Super-Linter](https://github.com/marketplace/actions/super-linter) in a _Reusable_ Workflow.
- Bonus: Optionally turn off non-DevOps linters (CSS, JS, HTML, etc.) when you want to ignore code (in my case it's to ignore sample code I stick in DevOps projects).
- Bonus: I added Job steps to correctly determine which branch to diff files with (in the case of having multiple release branches).
- Bonus: Lints only changed files on a PR, but lints all files on merge to main (or any release) branch.

> ⚠️ **DO NOT call this reusable workflow directly**, rather, use it as a template repository and fork it for your own reusable workflow. I might change this workflow at any time, based on new GHA features or learnings, and your calling workflow might break. ⚠️

## **Breaking Change 2026-03-31**

As of Super-Linter v6 (8/2024), it needs `pull-request: write` permissions by default to create a summary comment in the PR. Ensure your "calling" and "called" reusable workflows are set to the minimum permissions below. And also a friendly reminder to fork this repository and call it from your other repos rather than calling this repository's reusable workflow directly, which doesn't have releases or versions to limit the blast radius of breaking changes like this one. I also realize the irony that any time you fork my other repositories, that likely calls this repository, thus ensnaring you in this problem... but hey, I bet you learned something from the Actions failing! 😏 🤷‍♂️

```yaml
permissions:
  contents: read # clone the repository to lint
  pull-requests: write # create a summary comment in PR
  statuses: write # read/write to repository custom statuses
```

## How to reuse this example as a _Reusable_ Workflow

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

# reset permissions to none at the workflow level
# we'll set them at the job level below
permissions: {}

jobs:
  call-super-linter:
    name: Call Super-Linter

    permissions:
      contents: read # clone the repository to lint
      pull-requests: write # create a summary comment in PR
      statuses: write # read/write to repository custom statuses

    ### use Reusable Workflows to call my workflow remotely
    ### https://docs.github.com/en/actions/learn-github-actions/reusing-workflows
    ### you can also call workflows from inside the same repository via file path

    # FIXME: customize uri to point to your own reusable linter repository
    # NOTE: zizmor scanner rule ignore added because we control sha pins via reusable workflow, not calling workflow
    uses: bretfisher/super-linter-workflow/.github/workflows/reusable-super-linter.yaml@main # zizmor: ignore[unpinned-uses]


    ### Optional settings examples

    # with:
    ### 1. Remember .github/super-linter.env is injected for setting linter on/off

    ### 2. For a DevOps-focused repository. Prevents some code-language linters from running
    ### defaults to false
    # devops-only: false

    ### 3. A regex to exclude files from linting
    ### defaults to empty
    # filter-regex-exclude: html/.*
    #
    ### 4. Additional environment variables to pass to super-linter (one per line)
    # extra-envs: |
    #   VALIDATE_DOCKERFILE=false
    #   VALIDATE_JSCPD=false
    #   VALIDATE_TRIVY=false
```

## How to run Super-Linter locally

Option 1: Use [nektos/act](https://github.com/nektos/act) to run an existing GitHub Action workflow on your local repository clone.

Option 2: Use Docker to [run the Super-Linter image directly](https://github.com/github/super-linter/blob/main/docs/run-linter-locally.md).

Option 3: Pick the linter you want to run from Super-Linter, then install it locally to run manually. If you have a linter config, be sure to point the linter to `.github/linters/*`, and also realize that super-linter has default linter configs, that may change the linters behavior inside super-linter, with [templates listed here](https://github.com/github/super-linter/tree/main/TEMPLATES).

## This repository is part of my example DevOps repos on GitHub Actions

- [bretfisher/github-actions-templates](https://github.com/BretFisher/github-actions-templates) - Main repository
- (you are here) [bretfisher/super-linter-workflow](https://github.com/BretFisher/super-linter-workflow) - Reusable linter workflow
- [bretfisher/docker-build-workflow](https://github.com/BretFisher/docker-build-workflow)- Reusable Docker build workflow
- [bretfisher/docker-ci-automation](https://github.com/BretFisher/docker-ci-automation) - Step by step video and example of a Docker CI workflow
- [My full list of container examples and tools](https://github.com/bretfisher)

## 🎉🎉🎉 Join my cloud native DevOps community 🎉🎉🎉

- [My Cloud Native DevOps Discord server](https://devops.fan)
- [My weekly YouTube Live show](https://www.youtube.com/@BretFisher)
- [My weekly newsletter](https://www.bretfisher.com/newsletter)
- [My courses and coupons](https://www.bretfisher.com/courses)
