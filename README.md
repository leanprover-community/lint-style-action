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

Required. Allowed values: "check" or "suggest".

### Input: `lint-bib-file`

Enables linting of the bibiliography using `./scripts/lint-bib.sh`.

Allowed values: "true" or "false". Default value: "false".

### Input: `ref`

The branch, tag or SHA to lint. This defaults to the reference or SHA for the event that triggered the workflow. This corresponds to the `ref` input of [actions/checkout](https://github.com/actions/checkout).
