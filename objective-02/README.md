# Objective 02 – VPN Gateways and Diagnostics

This template layers VPN gateways onto three existing VNets and enables diagnostics to a storage account and Log Analytics workspace.

- Public IPs: one Standard static IPv4 address per VNet (named `<vnet>-vng-pip`).
- VPN gateways: one per VNet (named `<vnet>-vng`) using `VpnGw2`, `Vpn`, route-based, generation 2.
- Diagnostic settings: per-gateway settings sending all logs and metrics to the provided storage account and workspace.
- Outputs: object with the resource IDs of each gateway.

## Resource relationships
- Each public IP is independent.
- Each VPN gateway depends on its matching public IP and attaches to the `GatewaySubnet` of that VNet (the VNet and subnet must already exist).
- Each diagnostic setting depends on its matching VPN gateway and references the shared storage account and Log Analytics workspace.

## Parameters to override
- `location`: deployment region (defaults to the resource group location).
- `vNets`: object describing VNet names and subnets; only the names and presence of `GatewaySubnet` are used here.
- `storageAccountName`: existing StorageV2 account to hold diagnostic logs (must exist in the same subscription).
- `logAnalyticsWorkspaceName`: existing workspace for diagnostics.

## Prerequisites
- The VNets and their `GatewaySubnet` subnets already exist (for example, from Objective 01).
- The storage account and Log Analytics workspace already exist in the target subscription.

## Deploy with Azure CLI
```bash
rgName="rg-hybrid-demo"
location="eastus"
storageName="sthybrid0001"           # existing storage account
workspaceName="log-hybrid-0001"      # existing Log Analytics workspace

az group create --name "$rgName" --location "$location"

az deployment group create \
  --resource-group "$rgName" \
  --template-file objective-02/azureDeploy.json \
  --parameters location="$location" storageAccountName="$storageName" logAnalyticsWorkspaceName="$workspaceName"
```

## Deploy with Azure PowerShell
```powershell
$rgName = "rg-hybrid-demo"
$location = "eastus"
$storageName = "sthybrid0001"        # existing storage account
$workspaceName = "log-hybrid-0001"   # existing Log Analytics workspace

Connect-AzAccount
New-AzResourceGroup -Name $rgName -Location $location

New-AzResourceGroupDeployment `
  -ResourceGroupName $rgName `
  -TemplateFile "./objective-02/azureDeploy.json" `
  -TemplateParameterObject @{
    location = $location
    storageAccountName = $storageName
    logAnalyticsWorkspaceName = $workspaceName
  }
```

To customize VNet names or counts, adjust the `vNets` object parameter; the template copies gateways and public IPs for each VNet entry.
