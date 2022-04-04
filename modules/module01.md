# Setting up the simulated network and secure access workstation (SAW)

Blah

## Creating your resource group

For the purpose of this lab, we will keep everything nice and clean in one resource group so you can easily clean up when done.



## Setting up the VNETS

Blah

## Connecting to your SAW using Azure AD credentials

This needs to be done using an Azure CLI command as follows

``` azurecli
az login
az account set --subscription "<SubscriptionId>"
az network bastion rdp --name "<BastionName>" --resource-group "<ResourceGroupName>" --target-resource-id "<ResourceIdOfVirtualMachine>"
```
