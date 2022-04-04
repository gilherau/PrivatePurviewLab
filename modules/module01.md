# Setting up the simulated network and secure access workstation (SAW)

Todo: Build a Table of Content

## Creating your resource group

For the purpose of this lab, we will keep everything nice and clean in one resource group so you can easily clean up when done.

1. First let's log on to the [Azure Portal](https://portal.azure.com) and **sign in**.
1. Then using the top search box let's search for **"resource group"** ![SearchingResourceGroup](/images/Module01_1.png)
1. Then we click on **"create"** ![ResourceGroupCreate](/images/Module01_2.png)
1. Chose your resource group name (suggestion: **"PurviewPrivateLab"**) and region ( no suggestion but we strongly suggest you **stay consistent**). Finally, hit **"Review and Create"** ![ResourceGroupDetail](/images/Module01_3.png)
1. Next hit the **"Go to Resource Group"** button and we can move to creating the VNETs ![ConfirmResourceGroupCreation](/images/Module01_4.png)

## Setting up the VNETS

1. Once in your resource group, hit the **"+"** sign to add a new service. ![AddNewVnet](/images/Module01_5.png)
1. Then search for **"Virtual Network"** and click on **"Virtual Network"** ![SearchForVnet](/images/Module01_6.png)
1. Hit the **"+"** sign to create a new VNET ![NewVnet](/images/Module01_7.png)
1. Next we enter the VNET basics. Make sure to **select the "PurviewPrivateLab" ResourceGroup** and name this VNET **"hub-vnet"**. Then **click on "IP Addresses"** up top ![VnetBasics](/images/Module01_8.png)
1. Now we choose our address space. For this lab let's use the following CIDR block of **10.60.0.0/24** which will give us a 65536 IP range. Then click on **"Add Subnet"** ![VnetAddressSpace](/images/Module01_9.png)
1. Time to enter the subnet details in the entry pane that opened on the right. At this step we are only creating one subnet to house our Secure Access Workstation. Name that subnet **"hub-sn-connect"** and type **10.60.1.0/24** as the subnet address range. Click **Add** when done. ![CreatingSubnets](/images/Module01_10.png)
1. Click on **Security** up top and this will take you to where we can provision our Azure Bastion Service. Click **Enable** next to BastionHost, then type **PrivatePurviewLabBastion** as the Bastion Name, then use **10.60.0.0/24** for the Subnet Range. Here a special dedicated subnet called "AzureBastionSubnet" will be created for us. We now have to create a public address for us to use the Bastion so click on **Create New** next to "Public IP Address" ![BastionConfig](/images/Module01_11.png)
1. We now have to provide a name for the Public address. Please use **PrivatePurviewLabPubIP** and hit **Ok**. ![PublicIPName](/images/Module01_12.png)
1. Finally, hit **review and Create** Validate that your screen looks like this the screenshot below and hit **Create**. ![FinalVnetConfig](/images/Module01_13.png)

## Connecting to your SAW using Azure AD credentials

This needs to be done using an Azure CLI command as follows

``` azurecli
az login
az account set --subscription "<SubscriptionId>"
az network bastion rdp --name "<BastionName>" --resource-group "<ResourceGroupName>" --target-resource-id "<ResourceIdOfVirtualMachine>"
```
