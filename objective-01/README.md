# Objective 01 – Base Network and Monitoring

This template builds three small virtual networks for a hub-and-spoke-style lab, along with supporting security and monitoring resources.

- Network security groups: three NSGs (`nsgmin001`, `nsgmin002`, `nsgmin003` by default).
- Virtual networks: three VNets (`vnet1`, `vnet2`, `vnet3`) copied from the `vNets` object. Each contains `subnet1`, `subnet2`, and `GatewaySubnet`.
- Subnet protection: `subnet1` and `subnet2` in each VNet are associated with the matching NSG index; `GatewaySubnet` is left open for gateway use.
- Storage: one StorageV2 account (`storageAccountName`, must be globally unique).
- Monitoring: one Log Analytics workspace (`logAnalyticsWorkspaceName`).
- Outputs: resource IDs for the storage account and workspace.

## Resource relationships
- NSGs are created first, then each VNet depends on its same-index NSG.
- VNets share the same shape but keep separate address spaces and NSG instances.
- The storage account and Log Analytics workspace are independent of the network stack, so they can be referenced by later objectives.

## Parameters to override
- `location`: deployment region (defaults to the resource group location).
- `vNets`: object defining each VNet’s address space and subnets.
- `nsgNames`: array of NSG names; length should match the number of VNets.
- `storageAccountName`: globally unique name (3–24 lowercase letters/numbers).
- `logAnalyticsWorkspaceName`: workspace name.

## Deploy with Azure CLI
```bash
rgName="rg-hybrid-demo"
location="eastus"
storageName="sthybrid0001"           # must be globally unique
workspaceName="log-hybrid-0001"

az group create --name "$rgName" --location "$location"

az deployment group create \
  --resource-group "$rgName" \
  --template-file objective-01/azureDeploy.json \
  --parameters location="$location" storageAccountName="$storageName" logAnalyticsWorkspaceName="$workspaceName"
```

## Deploy with Azure PowerShell
```powershell
$rgName = "rg-hybrid-demo"
$location = "eastus"
$storageName = "sthybrid0001"        # must be globally unique
$workspaceName = "log-hybrid-0001"

Connect-AzAccount
New-AzResourceGroup -Name $rgName -Location $location

New-AzResourceGroupDeployment `
  -ResourceGroupName $rgName `
  -TemplateFile "./objective-01/azureDeploy.json" `
  -TemplateParameterObject @{
    location = $location
    storageAccountName = $storageName
    logAnalyticsWorkspaceName = $workspaceName
  }
```

To customize VNets or NSG names, add `vNets` and `nsgNames` entries to the parameter object (PowerShell) or `--parameters` block (CLI).
