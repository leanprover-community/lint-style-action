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
* When set to `autofix`, this action applies the same fixes as `suggest` but leaves them uncommitted in the working tree instead of posting review comments. The caller is responsible for doing something with them.

Required. Allowed values: "check", "suggest" or "autofix".

`autofix` is for callers that would rather push the fixes than comment them, for example with
[pre-commit-ci/lite-action](https://github.com/pre-commit-ci/lite-action). Two things to know about
it:

* The action checks the repository out itself, and wipes the workspace before doing so, so it has to
  run *before* any step of yours that puts something there -- including your own `actions/checkout`.
* It reports success whether or not there were style errors, because `lake exe lint-style --fix`
  always exits zero. Errors that no fixer can repair are only reported by `check` mode, so keep a
  `check` run somewhere.

### Input: `lint-bib-file`

Enables linting of the bibiliography using `./scripts/lint-bib.sh`.

Allowed values: "true" or "false". Default value: "false".

### Input: `ref`

The branch, tag or SHA to lint. This defaults to the reference or SHA for the event that triggered the workflow. This corresponds to the `ref` input of [actions/checkout](https://github.com/actions/checkout).
