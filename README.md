# Super Linter Reusable Workflow Examples

[![Lint all the codes](https://github.com/BretFisher/super-linter-workflow/actions/workflows/super-linter.yaml/badge.svg)](https://github.com/BretFisher/super-linter-workflow/actions/workflows/super-linter.yaml)

The GitHub [Super-Linter](https://github.com/marketplace/actions/super-linter) project is a fantastic way to lint all your file types with a single GitHub Actions Workflow. A great way to implement it everywhere is to use GHA's [Reusable Workflows](https://docs.github.com/en/actions/learn-github-actions/reusing-workflows) (see below).

[My video walkthrough of this repository](https://youtu.be/aXZgQM8DqXg)

This repo is part of a group of my sample repos on GitHub Actions:

* [bretfisher/github-actions-templates](https://github.com/BretFisher/github-actions-templates) - Main repo
* (you are here) [bretfisher/super-linter-workflow](https://github.com/BretFisher/super-linter-workflow) - Reusable linter workflow
* [bretfisher/docker-build-workflow](https://github.com/BretFisher/docker-build-workflow)- Reusable docker build workflow


## Features of this custom Super-Linter example

- All the features of [Super-Linter](https://github.com/marketplace/actions/super-linter) in a *Reusable* Workflow.
- Bonus: Optionally turn off non-DevOps linters (CSS, JS, HTML, etc.) when you want to ignore code (in my case it's to ignore sample code I stick in DevOps projects).
- Bonus: I added Job steps to correctly determine which branch to diff files with (in the case of having multiple release branches).
- Bonus: Lints only changed files on a PR, but lints all files on merge to main (or any release) branch.

## How to reuse this example as a *Reusable* Workflow

1. Fork this repository for you to customize your linters in a single location for your org/projects.
2. Add a new workflow to all your other repositories and paste in this YAML to call the central-repos reusable workflow.

```yaml
---
name: Lint Code Base

on:
  
  push:
    branches: [main]
  
  pull_request:

jobs:
  call-super-linter:

    name: Call Super-Linter

    # use Reusable Workflows to call my linter config remotely
    # https://docs.github.com/en/actions/learn-github-actions/reusing-workflows
    
    permissions:
      contents: read # clone the repo to lint
      statuses: write #read/write to repo custom statuses

    #FIXME: customize uri to point to your forked linter repository
    uses: bretfisher/super-linter-workflow/.github/workflows/super-linter.yaml@main
    
    # Optional settings examples
    
    # with:
    
      # devops-only: false
      
      # filter-regex-exclude: html/.*
```

## How to run Super-Linter locally

Option 1: Use [nektos/act](https://github.com/nektos/act) to run an existing GitHub Action workflow on your local repository clone.

Option 2: Use Docker to [run the Super-Linter image directly](https://github.com/github/super-linter/blob/main/docs/run-linter-locally.md).
