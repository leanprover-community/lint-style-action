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

Secret token used by the style bot to check out the pull request, push the style commit and react to a review comment. Only used in `fix` mode, and defaults to the workflow's own `GITHUB_TOKEN`. When in doubt, set this to the value of `secrets.GITHUB_TOKEN`.

Note that `fix` mode can only update a pull request whose branch lives in the repository itself. For a pull request from a fork, the style commit has to be pushed to the fork, which `secrets.GITHUB_TOKEN` cannot do; this input then has to be a token with write access to the fork.

### Input: `ref`

The branch, tag or SHA to lint. This defaults to the reference or SHA for the event that triggered the workflow. This corresponds to the `ref` input of [actions/checkout](https://github.com/actions/checkout).

## Permissions

If you restrict the permissions of `GITHUB_TOKEN` with the
[`permissions`](https://docs.github.com/en/actions/reference/workflow-syntax-for-github-actions#permissions)
key -- which is worth doing, since any permission you do not list is set to `none` -- each mode
needs the following:

| Mode | Permissions |
|---|---|
| `check` | `contents: read` |
| `suggest` | `contents: read`, `pull-requests: write` |
| `fix` | `contents: write`, `pull-requests: write`, `issues: write` |

`suggest` needs `pull-requests: write` to post its review comments. Note that it does *not* use
`BOT_FIX_STYLE_TOKEN` for this: [reviewdog/action-suggester](https://github.com/reviewdog/action-suggester)
falls back to the workflow's own `GITHUB_TOKEN`, so the `permissions` key is the only way to grant it.

`fix` splits its work across two tokens, so its row above is the union of what each one needs:

* `BOT_FIX_STYLE_TOKEN` checks out the pull request, pushes the style commit and reacts to a review
  comment. It needs `contents: write` and `pull-requests: write`.
* The workflow's own `GITHUB_TOKEN` checks the commenter's permission and reacts to an ordinary pull
  request comment, because
  [actions-cool/check-user-permission](https://github.com/actions-cool/check-user-permission) and
  [peter-evans/create-or-update-comment](https://github.com/peter-evans/create-or-update-comment)
  both default to it and this action does not override that. It needs `issues: write`, which only
  the `permissions` key can grant.

If you pass `secrets.GITHUB_TOKEN` as `BOT_FIX_STYLE_TOKEN` -- or leave the input unset, which comes
to the same thing -- the two are one token, and `permissions` has to cover the whole row.
