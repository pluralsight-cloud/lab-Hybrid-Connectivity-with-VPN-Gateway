# Objective 05 – Expand VNets with Additional Address Space and Subnets

This template extends three VNets by adding an extra address space to each and creating two new subnets per VNet, attaching them to existing network security groups.

- Virtual networks: vnet1, vnet2, vnet3 updated to include their original and new address prefixes.
- New subnets: `subnet3` and `subnet4` per VNet, sized from the added address space.
- NSG association: each new subnet is linked to the matching NSG name index in `nsgNames`.
- Outputs: resource IDs for the newly created subnets in each VNet.

## Resource relationships
- Each VNet resource is declared with the combined address space (existing + additional).
- Each new subnet depends on its VNet and references an existing NSG (`nsgmin001`, `nsgmin002`, `nsgmin003` by default).
- NSGs are not deployed here; they must pre-exist (for example, from Objective 01).

## Parameters to override
- `location`: deployment region (defaults to the resource group location).
- `vNets`: object providing VNet names and existing address prefixes.
- `additionalAddressSpaces`: extra CIDR added to each VNet.
- `additionalSubnets`: subnet names and prefixes carved from the added space.
- `nsgNames`: array of NSG names aligned by VNet index.

## Prerequisites
- VNets already exist with the original address space.
- NSGs matching `nsgNames` already exist.
- Address spaces and subnets must not overlap existing ranges.

## Deploy with Azure CLI
```bash
rgName="rg-hybrid-demo"
location="eastus"

az group create --name "$rgName" --location "$location"

az deployment group create \
  --resource-group "$rgName" \
  --template-file objective-05/azureDeploy.json \
  --parameters location="$location"
```

## Deploy with Azure PowerShell
```powershell
$rgName = "rg-hybrid-demo"
$location = "eastus"

Connect-AzAccount
New-AzResourceGroup -Name $rgName -Location $location

New-AzResourceGroupDeployment `
  -ResourceGroupName $rgName `
  -TemplateFile "./objective-05/azureDeploy.json" `
  -TemplateParameterObject @{
    location = $location
  }
```

To change CIDRs, subnets, or NSG bindings, adjust the `additionalAddressSpaces`, `additionalSubnets`, and `nsgNames` parameters before deployment.
