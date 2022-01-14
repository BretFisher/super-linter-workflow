# Super Linter Reusable Worklow Example

The GitHub [Super-Linter](https://github.com/marketplace/actions/super-linter) project is a fantastic way to lint all your file types with a single GitHub Actions Workflow. A great way to implement it everywhere is to use GHA's [Reusable Workflows](https://docs.github.com/en/actions/learn-github-actions/reusing-workflows) (see below).

[My video walkthrough of this repo](https://youtu.be/aXZgQM8DqXg)

## Features of this Super-Linter example

- All the features of Super-Linter in a Workflow
- Bonus: Added Job steps to correctly determine which branch to diff files with
- Bonus: Lints only changed files on a PR, but lints all files on merge to main (or any release) branch

## How to reuse this example as a *Reusable* Workflow

1. Fork this repo for you to customize your linters
2. Add a workflow to all your other repos that calls your linter workflow using GitHub's "Reusable Workflow"
3. Add something similar to this to those workflows:

```yaml
---
name: Lint Code Base

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  call-super-linter:
    #customize uri to point to your forked linter repo
    uses: bretfisher/super-linter-workflow/.github/workflows/super-linter.yaml@main
```

## How to run Super-Linter locally

Option 1: Use [nektos/act](https://github.com/nektos/act) to run an existing GitHub Action workflow on your local repo clone.

Option 2: Use Docker to [run the Super-Linter image directly](https://github.com/github/super-linter/blob/main/docs/run-linter-locally.md).
