# AGENTS.md

Guidance for coding agents working in `super-linter-workflow`.

## Repo Summary

- This repository is a reusable GitHub Actions workflow wrapper around GitHub Super-Linter.
- Main implementation: `.github/workflows/reusable-super-linter.yaml`.
- Local self-check workflow: `.github/workflows/call-local-super-linter.yaml`.
- Consumer template: `templates/call-super-linter.yaml`.
- Main docs: `README.md`.
- Linter configs: `.github/linters/.yaml-lint.yml` and `.github/linters/.markdown-lint.yml`.

## Build, Lint, And Test Reality

- There is no traditional build step.
- There is no unit-test framework in this repository.
- The closest thing to a test is running the local workflow with `act`.
- Focused validation is usually a single-file linter run.

## Primary Commands

Run the repo's local workflow with `act`:

```bash
act -W .github/workflows/call-local-super-linter.yaml
```

Run the PR-style event locally:

```bash
act pull_request -W .github/workflows/call-local-super-linter.yaml
```

Run only the single job in that workflow:

```bash
act -W .github/workflows/call-local-super-linter.yaml -j call-super-linter
```

For this repo, that is the closest equivalent to "run a single test".

## Single-File Validation

Lint one YAML file with the repo config:

```bash
yamllint -c .github/linters/.yaml-lint.yml .github/workflows/reusable-super-linter.yaml
```

Lint the template YAML:

```bash
yamllint -c .github/linters/.yaml-lint.yml templates/call-super-linter.yaml
```

Lint Markdown docs:

```bash
markdownlint -c .github/linters/.markdown-lint.yml README.md
```

Lint this file after editing it:

```bash
markdownlint -c .github/linters/.markdown-lint.yml AGENTS.md
```

Validate workflow syntax if `actionlint` is installed:

```bash
actionlint .github/workflows/*.yaml
```

Validate workflow security if `zizmor` is installed:

```bash
zizmor .github/workflows/*.yaml
```

## Typical Validation Order

- Docs-only change: run `markdownlint` on changed Markdown.
- YAML-only change: run `yamllint` on changed YAML.
- Workflow logic change: run `yamllint`, `actionlint`, `zizmor`, then `act` if available.
- Template or README change: verify examples still match the reusable workflow inputs.
- If a tool is unavailable, say so clearly in your final summary.

## YAML Style

- Start YAML files with `---`.
- Use two-space indentation.
- Keep list indentation consistent.
- Follow `.github/linters/.yaml-lint.yml`.
- Do not introduce duplicate keys.
- Preserve Unix newlines.
- Avoid formatting churn unrelated to the task.
- Keep comments close to the block they explain.

## GitHub Actions Style

- Treat `.github/workflows/reusable-super-linter.yaml` as the source of truth.
- Keep `.github/workflows/call-local-super-linter.yaml` thin; it should call the reusable workflow, not reimplement it.
- Keep `templates/call-super-linter.yaml` aligned with actual inputs and comments.
- Keep `README.md` aligned with the template and workflow behavior.
- Preserve the current `permissions: {}` at workflow scope and explicit job permissions.
- Preserve `concurrency` unless there is a task-specific reason to change it.
- Keep third-party actions pinned by SHA.
- If you update a pinned action, update the trailing version comment too.
- Use clear job and step names that explain intent.

## Shell In Workflow Steps

- Keep inline shell short and readable.
- Prefer simple shell over adding helper scripts.
- Use `shell: bash` only when needed.
- Quote variable expansions unless unquoted behavior is intentional.
- Use `$GITHUB_ENV` to pass values between steps, matching the existing pattern.
- Preserve readable `echo`, `printf`, and `sed` usage.
- Keep PR-vs-push branching obvious in step logic.

## Naming Conventions

- Reusable workflow inputs use lower-case kebab-case, such as `devops-only` and `filter-regex-exclude`.
- Environment variables use upper snake case, such as `DEFAULT_BRANCH` and `VALIDATE_ALL_CODEBASE`.
- Workflow, job, and step names should be descriptive and human-readable.
- Workflow files should continue using `*.yaml` under `.github/workflows/`.

## Error Handling And Safety

- Prefer small, explicit steps over dense one-liners.
- Do not silently broaden or narrow lint coverage.
- Do not silently change permissions.
- If you change defaults, also update comments, template examples, and README docs.
- Be careful with `filter-regex-exclude` and injected env vars; they directly affect lint scope.
- Preserve the current logic that handles PR target branches versus direct branch pushes.

## Cross-File Consistency Checklist

When changing workflow behavior, check all affected copies of that behavior:

- reusable workflow inputs
- local caller `with:` block
- template example usage
- README examples and explanation
- nearby comments describing the behavior

## Before Finishing

- Lint every changed YAML file with `yamllint`.
- Lint every changed Markdown file with `markdownlint`.
- Run `actionlint` and `zizmor` for workflow changes if available.
- Run `act -W .github/workflows/call-local-super-linter.yaml -j call-super-linter` for meaningful workflow edits if available.
- Mention any commands you could not run.
