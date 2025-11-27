# Objective 04 – BGP-Enabled Gateways and VNet Connections

This template rebuilds the VPN gateways with BGP enabled and wires up bidirectional VNet-to-VNet connections.

- VPN gateways: one per VNet (`<vnet>-vng`) using `VpnGw2`, route-based, generation 2, with custom ASN and BGP peering IP.
- Connections: four `Vnet2Vnet` connections linking vnet1↔vnet2 and vnet2↔vnet3, all with BGP enabled.
- Shared key: single `sharedKey` parameter reused across all connections.
- Outputs: IDs for the vnet1→vnet2 and vnet3→vnet2 connections.

## Resource relationships
- Each gateway depends on an existing public IP named `<vnet>-vng-pip` and the VNet’s `GatewaySubnet`.
- Gateways use the per-VNet ASN and peering IP from `bgpSettings`.
- Each connection references the two gateways it links; connections are independent but share the same pre-shared key.

## Parameters to override
- `location`: deployment region (defaults to the resource group location).
- `vNets`: object with VNet names; used to derive gateway names and connections.
- `bgpSettings`: per-VNet ASN and BGP peering address for the gateway’s IP configuration.
- `sharedKey`: pre-shared key used for all connections (secure string).

## Prerequisites
- Public IPs named `<vnet>-vng-pip` already exist (for example, from Objective 02).
- VNets and `GatewaySubnet` subnets exist.

## Deploy with Azure CLI
```bash
rgName="rg-hybrid-demo"
location="eastus"
psk="P@ssw0rd!"  # choose your own strong PSK

az group create --name "$rgName" --location "$location"

az deployment group create \
  --resource-group "$rgName" \
  --template-file objective-04/azureDeploy.json \
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
  -TemplateFile "./objective-04/azureDeploy.json" `
  -TemplateParameterObject @{
    location = $location
    sharedKey = $psk
  }
```

To change ASNs or peering IPs, edit the `bgpSettings` object; to add/remove VNets, update both `vNets` and the connection definitions.
