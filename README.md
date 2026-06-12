# `guardian/actions-build-facts`

A ([composite](https://docs.github.com/en/actions/tutorials/create-actions/create-a-composite-action)) GitHub Action to obtain facts about an ongoing build.

This Action handles some of the nuances of GitHub Actions, so you don't have to.
For example, see https://github.com/orgs/community/discussions/26325.

See [`action.yml`](action.yml) for details on the available inputs and outputs.

## Permissions
This Action does not require any permissions.

## Example usage

```yaml
name: CI
on:
  pull_request:
  workflow_dispatch:
  push:
    branches:
      - main
jobs:
  # Obtain build facts
  facts:
    runs-on: ubuntu-slim
    permissions: {} # This job doesn't need any permissions.
    outputs:
      branchName: ${{ steps.get-build-facts.outputs.branchName }}
      buildNumber: ${{ steps.get-build-facts.outputs.buildNumber }}
      commitSha: ${{ steps.get-build-facts.outputs.commitSha }}
    steps:
      # Find the latest version here - https://github.com/guardian/actions-build-facts/releases.
      - uses: guardian/actions-build-facts@v0.0.1
        id: get-build-facts

  # Now use the facts in your build steps
  build:
    runs-on: ubuntu-latest
    needs:
      - facts
    permissions:
      contents: read
    steps:
      - uses: actions/checkout@v6.0.3

      - uses: actions/setup-node@v6.4.0
        with:
          node-version-file: '.tool-versions'
          cache: npm

      - run: npm run build
        env:
          BRANCH_NAME: ${{ needs.facts.outputs.branchName }}
          BUILD_NUMBER: ${{ needs.facts.outputs.buildNumber }}
          COMMIT_SHA: ${{ needs.facts.outputs.commitSha }}
```