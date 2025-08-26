# .github

A special repository used to store documentation, templates, workflows, and descriptions for our organization. It is also used to provide default documents for all repositories. When a repository is created within our organization, it will automatically inherit the contents of the `.github` repository.

## Github Action Workflows

### Sync Labels

#### Description

Syncs labels accross repositories of the organization.

It should run on push.

#### Requirements

The labels are configured in a YAML file located in `.github/labels.yml` in this repo.
The repositories must be specified in the `call-label-syncer.yml` workflow.
This workflow requires to have permissions in order to operate on other repositories so you must specify a personal access token (PAT) in your secrets.

#### Inputs

| secrets  | description                    | required |
| -------- | -------------------------------| -------- |
| PAT      | personal access token          | true     |

| inputs          | description                                 | required | default              |
| --------------- | ------------------------------------------  | -------- | -------------------- |
| labels.yml      | Label configuration (desc, color, text)     | true     | `.github/labels.yml` |
| repositories    | repositories list (e.g plan4better/goat) | true     |                      |

#### Example Usage

``` yaml

name: "Sync labels"

on:
  push:
    paths:
      - '.github/labels.yml'
      - '.github/workflows/call-label-syncer.yml'
    branches:
      - main
jobs:
  build:
    name: Sync labels
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: micnncim/action-label-syncer@v1.3.0
        with:
            manifest: .github/labels.yml
            repository: |
                plan4better/goat
            token: ${{ secrets.PAT }}

```

`.github/labels.yml`

``` yaml
- name: api
  color: f77c47
  description: Everything related the API.
- name: devops
  color: ed2d69
  description: Devops related
```

### Apply Issue Labels to PR

#### Description

Applies labels from linked issues to pull requests.
It should run on a workflow call.

``` yaml
name: "Apply issue labels to PR"

on:
  pull_request_target:
    types:
      - opened

jobs:
  label-on-pr:
    name: pr-apply-label
    uses: "plan4better/.github/.github/workflows/pr-apply-issue-labels.yml@main"
    permissions:
      contents: none
      issues: read
      pull-requests: write
    secrets: inherit

```
