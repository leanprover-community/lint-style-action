# Lean style linter action

This action sets up and runs the `lake exe lint-style` command on your repository. This lints for formatting issues with your Lean code.

To enable this action, add it to a workflow that runs on a push or pull request. For example:
```yml
on:
  push:
  pull_request:

jobs:
  lint-style:
    name: Lint style
    runs-on: ubuntu-latest
    steps:
      - uses: leanprover-community/lint-style-action
        with:
          mode: check
          lint-bib-file: true
```

## Configuration

### Input: `mode`

The actions taken based on style linter output.

* When set to `check`, this action runs the style linters.
* When set to `suggest`, this action adds review comments with suggestions.
* When set to `fix-worktree`, this action applies the same fixes as `suggest` but leaves them uncommitted in the working tree instead of posting review comments. The workflow is responsible for doing something with them.

Required. Allowed values: "check", "suggest" or "fix-worktree".

`fix-worktree` is for workflows that would rather push the fixes than comment them, for example with
[pre-commit-ci/lite-action](https://github.com/pre-commit-ci/lite-action).

### Input: `lint-bib-file`

Enables linting of the bibiliography using `./scripts/lint-bib.sh`.

Allowed values: "true" or "false". Default value: "false".

### Input: `ref`

The branch, tag or SHA to lint. This defaults to the reference or SHA for the event that triggered the workflow. This corresponds to the `ref` input of [actions/checkout](https://github.com/actions/checkout).

## Things to know

* The action checks the repository out itself, and wipes the workspace before doing so, so it has to
  run *before* any step of yours that puts something there -- including your own `actions/checkout`.
* The action builds and runs code from the ref it lints: `lake exe lint-style` and `lake exe mk_all`
  are built from that ref's sources, and `scripts/lint-bib.sh` is that ref's script. If the ref can
  come from someone you don't trust, such as a fork's pull request, don't give the job a token that
  can write or any secrets.
* `suggest` and `fix-worktree` report success whether or not there were style errors, because
  `lake exe lint-style --fix` always exits zero. Errors that no fixer can repair are only reported by
  `check` mode, so keep a `check` run somewhere.

## Compatibility

You will need to a step manually installing Python 3.8 to your workflow if you want to use `lint-style-action` with versions of mathlib before [0770cc9](https://github.com/leanprover-community/mathlib4/commit/0770cc908695e32053a911d22b560b577bb3d888) (2026-09-25, v4.35.0-rc3), e.g.:
```yml
    - name: install Python
      uses: actions/setup-python@a26af69be951a213d495a4c3e4e4022e16d87065 # v5.6.0
      with:
        python-version: 3.8
```
