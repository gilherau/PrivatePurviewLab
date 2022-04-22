# Setting up the base environment and simulated network

[:arrow_backward: Previous module](/modules/module00.md) - [**home**](/README.md) - [Next Module :arrow_forward:](/modules/module02.md)

Table of contents

* [Setting up the base environment and simulated network](#setting-up-the-base-environment-and-simulated-network)
  * [Creating your resource group](#creating-your-resource-group)
  * [Setting up the VNETS](#setting-up-the-vnets)
  * [Peering the Three VNETs together](#peering-the-three-vnets-together)

We will now create the resource group that will hold all our solution. This will allow to easily clean up resources when done with the lab. In a real world scenario, the different networks we will create would actually be in different Azure Subscriptions.

## Creating your resource group

For the purpose of this lab, we will keep everything nice and clean in one resource group so you can easily clean up when done.

1. First let's log on to the [Azure Portal](https://portal.azure.com) and **sign in**.
___
1. Then using the top search box let's search for **"resource group"**

    ![SearchingResourceGroup](/images/Module01_1.png)
___

1. Then we click on **"create"** ![ResourceGroupCreate](/images/Module01_2.png)
1. Chose your resource group name (suggestion: **"PurviewPrivateLab"**) and region ( no suggestion but we strongly suggest you **stay consistent**). Finally, hit **"Review and Create"**  

    ![ResourceGroupDetail](/images/Module01_3.png)

___

1. Next hit the **"Go to Resource Group"** button and we can move to creating the VNETs ![ConfirmResourceGroupCreation](/images/Module01_4.png)  

___

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

**Create the other two VNETs**
Repeat the process above but **skip step 7 and 8 (Creating an Azure Bastion)**. use the VNET address space and Subnet address space described in the table below

Use this table when repeating step 5
| VNET name  | VNET address space |
| ------------- | ------------- |
|dmlz-vnet|10.61.0.0/16|
|dlz-vnet|10.62.0.0/16|

Use this table when repeating step 6
| VNET name | Subnet name | Subnet address space |
| ------------- | ------------- | ------------- |
|dmlz-vnet|dmlz-sn-1|10.61.1.0/24|
|dlz-vnet|dlz-sn-1|10.62.1.0/24|

## Peering the Three VNETs together

This step will connect all three VNETs together into a mesh. This essentially makes all three network become one big network. This is useful when scaling out your data mesh across multiple subscription. It serves no real purpose for this lab but it demonstrate how a typical network environment is setup.

1. Start by accessing the overview of pane of the **hub-vnet** and click on either one of the  **peerings** button (see screenshot)![ClickPeering](/images/Module01_14.png)
1. Then click **+ Add** ![PeeringAdd](/images/Module01_15.png)
1. Now enter the peering details. Since we are starting the peering process from the Hub vnet to the DMLZ vnet, we will call this peering **hub2dmlz**, under the "Remote virtual network" heading, choose a name for the peering name from the other side. We recommend a reverse name so here **dmlz2hub**. Finally make sure the subscription name is correct and that you choose the correct "destination" virtual network, in this case, choose **dmlz-vnet** and finish by clicking **Add**   ![CreatePeeringDetails](/images/Module01_16.png)![PeeringDetail2](/images/Module01_17.png) 

Repeat this process using the following parameters:

| Source vnet | Peering name | Remote vnet peering name | Virtual network (Destination) |
| ------------- | ------------- |------------- |------------- |
| dmlz-vnet | dmlz2dlz | dlz2dmlz | dlz-vnet |

Note that there's no need to peer the dlz-vnet with the hub-vnet since we have peered the dmlz-vnet and the hub vnet.
