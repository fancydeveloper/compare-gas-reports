# 🔥🛠️ Foundry Gas Diff Reporter

- An awesome description to complete

## Setup

A full setup guide to complete

## Usage

### Automatically generate a gas report diff on every PR

Add a workflow (`.github/workflows/foundry-gas-report.yml`):

```yaml
name: Report gas diff

on:
  push:
    branches:
      - main
  pull_request:
    # Optionally configure to run only for specific files. For example:
    # paths:
    # - "src/**"

jobs:
  compare_gas_reports:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      # Add any build steps here. For example:
      - run: forge test --gas-report > gasreport.ansi

      - name: Compare gas reports
        uses: Rubilmax/foundry-gas-report@v3.1
        with:
          workflowId: foundry-gas-report.yml # must be the name of the workflow file
          ignore: test/**/* # optionally filter out gas reports from specific paths
        id: gas_diff

      - name: Add gas diff to sticky comment
        uses: marocchino/sticky-pull-request-comment@v2
        with:
          message: ${{ steps.gas_diff.outputs.markdown }}
```

## Options

### `workflowId` _{string}_ (required)

The workflow ID the reference gas report was uploaded from.
By default, the reference gas report of next workflow runs are uploaded by this action from the previous workflow runs,
thus it should correspond to the ID of the workflow this action is defined in: `foundry-gas-report.yml` in the above example.

### `token` _{string}_

The github token allowing the action to upload and download gas reports generated by foundry.

_Defaults to: `${{ github.token }}`_

### `report` _{string}_

This should correspond to the path of a file where the output of forge's gas report has been logged.
Only necessary when generating multiple gas reports in the same workflow.

_Defaults to: `gasreport.ansi`_

### `report` _{string}_

The title displayed in the markdown output. Can be used to identify multiple gas diffs in the same PR.

_Defaults to: `Changes to gas cost`_

### `ignore` _{string}_

The list of paths from which to ignore gas reports, separated by a comma.
This allows to clean out gas diffs from dependency contracts impacted by a change (e.g. Proxies, ERC20, ...).

_Defaults to: `node_modules/**/*` (node dependencies are always discarded from gas reports)_

### `match` _{string}_

The list of paths of which only to keep gas reports, separated by a comma.
This allows to only display gas diff of specific contracts.

_Defaults to: `node_modules/**/*` (node dependencies are always discarded from gas reports)_

## Status

![Status: Experimental](https://img.shields.io/badge/Status-Experimental-blue)

This repository is maintained independently from [Foundry](https://github.com/foundry-rs/foundry) and may not work as expected with all versions of `forge`.
