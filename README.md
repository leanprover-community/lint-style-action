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
* When set to `fix`, this action looks for a "bot fix style" comment and pushes a commit with the suggested fixes.

Required. Allowed values: "check", "suggest" or "fix".

### Input: `lint-bib-file`

Enables linting of the bibiliography using `./scripts/lint-bib.sh`.

Allowed values: "true" or "false". Default value: "false".

### Input: `BOT_FIX_STYLE_TOKEN`

Secret token used by the style bot to interact with GitHub. Only required for the `fix` mode. When in doubt, set this to the value of `secrets.GITHUB_TOKEN`.

### Input: `ref`

The branch, tag or SHA to lint. This defaults to the reference or SHA for the event that triggered the workflow. This corresponds to the `ref` input of [actions/checkout](https://github.com/actions/checkout).

## Compatibility

You will need to a step manually installing Python 3.8 to your workflow if you want to use `lint-style-action` with versions of mathlib before [0770cc9](https://github.com/leanprover-community/mathlib4/commit/0770cc908695e32053a911d22b560b577bb3d888) (2026-09-25, v4.35.0-rc3), e.g.:
```yml
    - name: install Python
      uses: actions/setup-python@a26af69be951a213d495a4c3e4e4022e16d87065 # v5.6.0
      with:
        python-version: 3.8
```
