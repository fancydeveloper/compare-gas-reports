# 🔥🛠️ Foundry Gas Diff Reporter

Easily generate & compare gas reports automatically generated by Foundry on each of your Pull Requests!

## Live Example

### Changes to gas costs

#### 🧾 Summary

| Contract             | Method                           |            Avg (+/-) |                          % |
| :------------------- | :------------------------------- | -------------------: | -------------------------: |
| **PositionsManager** | _supplyLogic_<br />_borrowLogic_ | +849 ❌<br />+702 ❌ | **+0.25%**<br />**+0.13%** |
| **Morpho**           | _supply_                         |              +809 ❌ |                 **+0.23%** |

---

<details>
<summary><strong>Full diff report</strong> 👇</summary>
<br />

| Contract             |    Deployment Cost (+/-) | Method                           |                          Min (+/-) |                        % |                                    Avg (+/-) |                          % |                              Median (+/-) |                         % |                                     Max (+/-) |                         % |                  # Calls (+/-) |
| :------------------- | -----------------------: | :------------------------------- | ---------------------------------: | -----------------------: | -------------------------------------------: | -------------------------: | ----------------------------------------: | ------------------------: | --------------------------------------------: | ------------------------: | -----------------------------: |
| **PositionsManager** | 4,546,050&nbsp;(+14,617) | _supplyLogic_<br />_borrowLogic_ | 737&nbsp;(0)<br />148,437&nbsp;(0) | **0.00%**<br />**0.00%** | 365,894&nbsp;(+849)<br />542,977&nbsp;(+702) | **+0.25%**<br />**+0.13%** | 383,960&nbsp;(+995)<br />438,816&nbsp;(0) | **+0.27%**<br />**0.00%** | 2,121,294&nbsp;(+304)<br />1,090,968&nbsp;(0) | **+0.01%**<br />**0.00%** | 500&nbsp;(0)<br />292&nbsp;(0) |
| **Morpho**           |       3,150,242&nbsp;(0) | _supply_                         |                     3,997&nbsp;(0) |                **0.00%** |                          371,586&nbsp;(+809) |                 **+0.23%** |                       395,247&nbsp;(+995) |                **+0.27%** |                         2,125,764&nbsp;(+304) |                **+0.01%** |                   502&nbsp;(0) |

</details>

## Getting started

### Automatically generate a gas report diff on every PR

Add a workflow (`.github/workflows/foundry-gas-diff.yml`):

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
        uses: Rubilmax/foundry-gas-diff@v3.6
        with:
          workflowId: foundry-gas-diff.yml # must be the name of the workflow file
          ignore: test/**/* # optionally filter out gas reports from specific paths
        id: gas_diff

      - name: Add gas diff to sticky comment
        if: github.event_name == 'pull_request' || github.event_name == 'pull_request_target'
        uses: marocchino/sticky-pull-request-comment@v2
        with:
          delete: ${{ !steps.gas_diff.outputs.markdown }} # delete the comment in case changes no longer impacts gas costs
          message: ${{ steps.gas_diff.outputs.markdown }}
```

## Options

### `workflowId` _{string}_ (required)

The workflow ID the reference gas report was uploaded from.
By default, the reference gas report of next workflow runs are uploaded by this action from the previous workflow runs,
thus it should correspond to the ID of the workflow this action is defined in: `foundry-gas-diff.yml` in the above example.

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

_No default assigned: optional opt-in (Please note that node dependencies are always discarded from gas reports)_

### `match` _{string}_

The list of paths of which only to keep gas reports, separated by a comma.
This allows to only display gas diff of specific contracts.

_No default assigned: optional opt-in_

## Status

![Status: Experimental](https://img.shields.io/badge/Status-Experimental-blue)

This repository is maintained independently from [Foundry](https://github.com/foundry-rs/foundry) and may not work as expected with all versions of `forge`.
