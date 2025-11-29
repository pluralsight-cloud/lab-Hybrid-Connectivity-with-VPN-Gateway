# Objective 06 – Test VMs and ICMP Access

This template deploys a lightweight Linux VM into each VNet, enables ICMP inbound on existing NSGs, adds SSH inbound to the first NSG, assigns a public IP to the first VM, and writes boot diagnostics to an existing storage account.

- NSG rules: adds `Allow-Ping` (ICMP inbound, priority 200) to each NSG in `nsgNames`, plus `Allow-SSH` (TCP 22 inbound from Internet to Virtual Network, priority 100) on the first NSG.
- Public IP: one Standard static IPv4 PIP for the first VM.
- NICs: one NIC per VM, placed in the specified VNet/subnet (`vmSubnet`, default `subnet1`).
- VMs: three Ubuntu 22.04 LTS VMs (`<vnet>-vm`) using `Standard_B1ms`, password auth enabled; the first VM’s NIC is bound to the public IP.
- Boot diagnostics: uses the provided storage account for boot logs.
- Outputs: resource IDs for all three VMs.

## Resource relationships
- NSG rules target existing NSGs; NSGs must already exist.
- The first NIC depends on the public IP; other NICs are private-only.
- Each NIC depends on its target subnet in the existing VNet.
- Each VM depends on its NIC and references the storage account for boot diagnostics.

## Parameters to override
- `location`: deployment region (defaults to the resource group location).
- `vNets`: VNet names and VM subnet names (per VNet).
- `adminUsername`, `adminPassword`: credentials for the VMs (use secure, unique values).
- `storageAccountName`: existing storage account used for boot diagnostics.
- `nsgNames`: array of existing NSG names aligned with the VNet order.

## Prerequisites
- VNets and the target subnets already exist.
- NSGs matching `nsgNames` already exist and are associated to those subnets.
- Storage account exists for boot diagnostics.

## Deploy with Azure CLI
```bash
rgName="rg-hybrid-demo"
location="eastus"
storageName="sthybrid0001"          # existing storage account
adminUser="azureuser"
adminPass="ReplaceWithStrongP@ss!"  # choose a strong password

az group create --name "$rgName" --location "$location"

az deployment group create \
  --resource-group "$rgName" \
  --template-file objective-06/azureDeploy.json \
  --parameters location="$location" storageAccountName="$storageName" adminUsername="$adminUser" adminPassword="$adminPass"
```

## Deploy with Azure PowerShell
```powershell
$rgName = "rg-hybrid-demo"
$location = "eastus"
$storageName = "sthybrid0001"            # existing storage account
$adminUser = "azureuser"
$adminPass = "ReplaceWithStrongP@ss!"    # choose a strong password

Connect-AzAccount
New-AzResourceGroup -Name $rgName -Location $location

New-AzResourceGroupDeployment `
  -ResourceGroupName $rgName `
  -TemplateFile "./objective-06/azureDeploy.json" `
  -TemplateParameterObject @{
    location = $location
    storageAccountName = $storageName
    adminUsername = $adminUser
    adminPassword = $adminPass
  }
```

To change VM subnets or NSG mappings, edit the `vNets` and `nsgNames` parameters; to change image/size, adjust `vmSku` and `imageReference` in the template variables.
