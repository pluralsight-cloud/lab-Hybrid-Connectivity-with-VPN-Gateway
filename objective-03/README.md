# Objective 03 – VNet-to-VNet Connections

This template creates site-to-site style connections between the VPN gateways in three VNets.

- Connections: four `Microsoft.Network/connections` resources linking vnet1↔vnet2 and vnet2↔vnet3 (bidirectional pairs).
- Shared key: single `sharedKey` parameter reused across all connections.
- Outputs: IDs for the vnet1→vnet2 and vnet3→vnet2 connections.

## Resource relationships
- Each connection references two existing VPN gateways (`<vnet>-vng`) from Objective 02.
- Gateways must already be present and associated with `GatewaySubnet` in their VNets.
- Connections are independent of each other but share the same pre-shared key.

## Parameters to override
- `location`: deployment region (defaults to the resource group location).
- `vNets`: object with VNet names; used to derive gateway names (`<vnet>-vng`).
- `sharedKey`: pre-shared key used for all connections (secure string).

## Prerequisites
- VPN gateways for vnet1, vnet2, and vnet3 already deployed (Objective 02).
- Matching `GatewaySubnet` exists in each VNet.

## Deploy with Azure CLI
```bash
rgName="rg-hybrid-demo"
location="eastus"
psk="P@ssw0rd!"  # choose your own strong PSK

az group create --name "$rgName" --location "$location"

az deployment group create \
  --resource-group "$rgName" \
  --template-file objective-03/azureDeploy.json \
  --parameters location="$location" sharedKey="$psk"
```

## Deploy with Azure PowerShell
```powershell
$rgName = "rg-hybrid-demo"
$location = "eastus"
$psk = "P@ssw0rd!"   # choose your own strong PSK

Connect-AzAccount
New-AzResourceGroup -Name $rgName -Location $location

New-AzResourceGroupDeployment `
  -ResourceGroupName $rgName `
  -TemplateFile "./objective-03/azureDeploy.json" `
  -TemplateParameterObject @{
    location = $location
    sharedKey = $psk
  }
```

To change VNet names or add/remove pairs, update the `vNets` object and duplicate/remove connection resources as needed.
