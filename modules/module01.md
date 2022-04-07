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

## The Secure Access Workstation

Now it's time to deploy a windows 10 machine that will be used to simulate a secure access workstation (SAW). Note we will not do any deep hardening measures to lock the machine down as we would in a real world secure access workstation so please do not consider this a template. For the full SAW story, please read [this article](https://docs.microsoft.com/en-us/security/compass/privileged-access-deployment):

**Important** We are still going to enforce certain security features to secure access to this VM so that you get a sense of how this could be deployed in the field:

1. We will create the VM **without** any public addresses. Please pay attention to the instructions below and make sure your VM is **not** exposed to the internet.
1. We will use the Azure AD login extension so that we **do not have to log on using a local accounts**. Note that If you are using an Azure subscription where there is no conditional access policies, it may be simpler to use a local account. (See VM authentication section further below).
1. We will **use Azure Bastion as the public endpoint** through which we will connect. Note that by default, Azure Bastion does not support AAD login but there's preview feature to allow one to log in using AAD credential. Please read the section below to find out more.
1. We will **activate "Just in time access"** this means that each time we want to connect to the VM, we have to explicitly ask for the Remote desktop protocol (RDP) port to be whitelisted and only for a 3h duration. This will ensure that the attack surface of that VM is kept under tight control. Make sure to read the "Connect to the SAW using Bastion" section for the exact procedure.

## Create and deploy a private virtual machine

1. On your resource group overview page, hit the **"+"** sign to add a new service. ![AddVM](/images/Module01_5.png)
1. Search for **Virtual Machine** and click on **virtual Machine** ![CreateVm](/images/Module01_18.png)
1. Hit **Create** ![createVmButton](/images/Module01_19.png)
1. In the VM "basics" tab, please ensure you have your correct Subscription and resource group, then Chose **pplsaw** as the VM name, chose the right region, availability option and security type. Then chose **Windows 10 Pro** for the image to use. ![EnterVmDetail1](/images/Module01_20.png)
1. Keep scrolling the "basics" tab and chose a size (we recommend **D2S_v3**), choose an admin user name and password **(Make secure note of this password in case you need it)** and make sure the **"public inbound port" is set to "None"** ![VmBasics2](/images/Module01_21.png)
1. Finally, please confirm you have Eligible windows 10 licensing rights. Refer to the informational link there to ensure you do and. When ready, click on **Disks**![vmbasics3](/images/Module01_22.png)
1.  Chose **Standard SSD** since we do not need high performance and **check the "Delete with VM"** option. Encryption type should be set to its **Default**![Disk1](/images/Module01_23.png)
1. Then click on **Create Disk**![Disk2](/images/Module01_25.png)
1.  Now hit the **Networking** button ![Networking1](/images/Module01_26.png)
1. and enter **hub-vnet** as the virtual network, **hub-sn-connect** as the subnet, **-IMPORTANT!!** make sure you set Public IP to **None**. Then select **Basic** for NIC network security group and **None** for Public Inbound port.  ![networking2](/images/Module01_27.png)
1. wrapping up "Networking" by **checking** the **"Delete NIC when VM is Deleted"** and **"Accelerated networking"** options. Then hit the **"Management"** button.  ![network3](/images/Module01_28.png)
1. On the Management tab, MAke sure the boot diagnostics are set **Disable**, Login with Azure AD is **Checked** and **auto-shutdown is properly set**  ![Management1](/images/Module01_29.png) ![Management2](/images/Module01_30.png)
1. Finally, hit **Review and Create**. If the settings look correct, hit **Create** ![Management2](/images/Module01_31.png) 
1.Last step is to enable the "Just in time" policy for this VM. When accessing the overview pane of the VM, click on **Configuration** and click on the **Enable Just-in-time** button. Please refer to the next section on how to connect to the VM. ![Jit](/images/Module01_32.png)

## Connecting to your SAW

Regardless of how you authenticate to the VM, because we've activated **just-in-time access** you will have to **request access** every time you want to connect. Each request is valid for a period of 3h after it was granted.
If your organization does not enforce JIT access, you can ignore this guidance.

### Requesting "just-in-time access"

**IMPORTANT** If you have turned on JIT access, this step is required before you attempt to login. OF course if you do not have JIT enabled or you requested access less than 3hours ago you can disregard these steps.

1. On your VM overview pane, click on **connect** ![jit1](/images/Module01_39.png)
1. Then make sure **"My IP"** is selected and click on the **Request Access** button ![jit2](/images/Module01_40.png)
1. Wait for the access to be authorized ![jit3](/images/Module01_41.png)![jit4](/images/Module01_42.png)
1. You are now ready to connect to your VM. You must now decide which authentication mechanism to use in order to log in. Both option will use Azure Bastion to connect but the way in which you invoke it will differ.

## Choosing an authentication mechanism for the SAW

We now have to decide how we will authenticate to the SAW VM. The easiest way is to use a local account. Such a local account was created during the VM provisioning earlier. A local account may not comply with your organization's policy though and as a result you may have to first **domain join your VM** and **enroll the VM in your device management** system. The following sections will explain the two options

### Connect to the SAW using local account and Azure Bastion

If you do not wish/need to use Azure Active Directory authentication to your VM. here is how you use Azure Bastion to connect:

First, if you have enabled JIT access, you have to request access. See 

### Connecting to your SAW using Azure AD credentials

For organizations that have managed devices, AAD login is likely required.
In order to connect, also, the device likely needs to be "managed". If this is your situation a few pre-requisites are in order to allow AAD login.

### Install Azure CLI and confirm it is able to log in

1. [Download the windows version of Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli-windows?tabs=azure-cli) bits and install it on your machine
1. Open a windows Terminal (hit the windows key and type **"Terminal"**) and type in the following command:

``` azurecli
az login
```

This should open a browser window to authenticate you to Azure and confirm Azure CLI works on your machine.

### Upgrade Azure Bastion to support AAD login using native client

In order to use native client and AAD login, we must upgrade Azure Bastion to the **Standard** tier.

**Important** if you do not plan to use AAD login, do not perform these steps.

1. Navigate to your **Resource group** and find your Azure Bastion in the list and **click on it** ![BastionUp](/images/Module01_33.png)
1. In the overview page, click **Configuration** ![bastionUp2](/images/Module01_34.png)
1. Then Select the **Standard** tier and check **Native CLient Support** and then click **Apply** ![BastionUp3](/images/Module01_35.png)

### Granting your user login permission on the VM

Because you now have enabled Azure Bastion to log you in using your AAD credentials, we must grant the required role to your UPN.

1. In the VM overview pane, click on the **Access control (IAM)** button in the left blade and click on **Add role assignment** ![vmiam1](/images/Module01_48.png)
1. Select the **Virtual machine Administrator Login** role and click next ![vmiam2](/images/Module01_49.png)
1. Click **Select members** ![vmiam3](/images/Module01_50.png)
1. Then **enter your user principal name** (UPN (usually your work email)) and add you to the role ![vmiam4](/images/Module01_51.png)

### Connect to your SAW using the local account and Azure Bastion

This is the procedure to log on to the VM using the local account

1. On the VM overview pane, click on **Connect** and in the drop down, select **Azure Bastion** and on the next page click **Use Azure Bastion** ![SawAdmPass1](/images/Module01_43.png)![SawAdmPass2](/images/Module01_44.png)
1. Enter the local user name and password you chose during the VM creation and click **Connect** steps ![SamAdmPass1](/images/Module01_45.png)
1. This will open a new browser window that will be a web based Remote Desktop to the VM.

**Since all the other resources are private, some of the operations like accessing the purview Studio will only be possible from this VM.**

### Join your SAW machine to Azure Ad

Now we will join the machine to our Work domain so that we don't have to use the local admin password or a local user.

1. In the VM, click on the **Start button** and search for the word **"Connect"** and in the search result you should see a link that send you to the settings page that lets you join an AD. (See screenshot below) ![AAdConnect1](/images/Module01_46.png)
1. Then click on **Connect** and enter your User principal name (usually your work address) and hit **Next** ![AAdConnect2](/images/Module01_47.png)

**Be patient** it may take a while for this process to complete, especially if your organization uses devices enrollment to control access. Now is the perfect time to go grab a cup of coffee.

### Note down a few important variables

The next step will require you to know your subscriptionID and the ResourceID of your Virtual machine.

1. On your Virtual machine overview page, the subscription Id is shown there. Copy and paste that information for later use. Then click on the **JSON view** button to get the resource ID ![vmresid](/images/Module01_36.png)
1. Note the **Copy to clipboard** button that is available to grab the resource ID. Please paste it somewhere for later use. ![vmresid2](/images/Module01_37.png)

### Connect to your VM using Azure Bastion Native AAD login and Remote Desktop

We're almost there!

This next step needs to be done using an Azure CLI command so fire up your terminal (if you closed it previously) and run the following commands **one line at a time** (we could make a script but the point is to learn here!)

Make sure you replace "SubscriptionId" with your own ID (keep the quotes) and same for "resource-group" and " "target-resource-id"

``` azurecli
az login
az account set --subscription "<SubscriptionId>"
az network bastion rdp --name "<BastionName>" --resource-group "<ResourceGroupName>" --target-resource-id "<ResourceIdOfVirtualMachine>"
```
