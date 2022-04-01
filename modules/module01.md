# Setting up the simulated network and secure access workstation (SAW)

Blah

## Setting up the VNETS

Blah

## Connecting to your SAW using Azure AD credentials

This needs to be done using an Azure CLI command as follows

``` azurecli
az login
az account set --subscription "73499bed-85c5-460b-9c83-39aa7d9cd41a"
az network bastion rdp --name "PrivatePvlBastion" --resource-group "Private-PVL" --target-resource-id "/subscriptions/73499bed-85c5-460b-9c83-39aa7d9cd41a/resourceGroups/Private-PVL/providers/Microsoft.Compute/virtualMachines/PrivatePvlWorkstation"
```
