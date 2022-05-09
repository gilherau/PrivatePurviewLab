# Setting up sample data sources

[:arrow_backward: Previous module](/modules/module02.md) - [**home**](/README.md) - [Next Module :arrow_forward:](/modules/module04.md)

Here the goal is to set up two sample data sources that we will configure to use private endpoints. For a fuller experience, we will setup a small data lake first and an Azure SQL instance pre-loaded with the AdventureWorks Sample database. That last step is optional, this lab can be performed with only one data source and Azure Data Lake is arguably the simpler use case to set up.

## Setting us a sample private data lake

Here we will setup a simple data lake consisting of an Azure Storage Account with hierarchical namespace enabled. During the creation process, we will also configure the storage account with a private endpoint and deny any traffic that is not coming from the private connection.

>**Note:** We will also activate security settings that will make our lives slightly more complicated but are important security best practices. More specifically, we will disable the use of keys to authenticate and force all authentication to use Azure AD as the authentication providers. While using keys is not necessarily bad, it is not recommended in production since they do not offer the same level of security, traceability and observability as using Oauth.

1. Start by navigating to your **PrivatePurviewLab** resource group and click on the **+** sign to get started. ![addBlob](/images/Module01_5.png) ___
1. Then search for **Storage Account** and click on **Storage Account** ![SA1](/images/module03/Module03_1.png) ___
1. Click on the **Storage account** item and click **Create** ![sa2](/images/module03/Module03_2.png) ![sa3](/images/module03/Module03_3.png) ___
1. On the **Basics** tabs of the creation wizard, make sure the correct subscription is selected and select **PrivatePurviewLab** as the resource group name. ![sa4](/images/module03/Module03_5.png) ___
1. Scroll down and chose a name for the storage account. We suggest using **ppladlsgen2**. Make sure the region is consistent with the one we've been using so far. Since this is a lab, we can use a lower service tier so go ahead and use **standard** for performance and **LRS** for the redundancy option. Finally click on **Next : Advanced** ![sa5](/images/module03/Module03_5.png) ___
1. In the **Advanced** tab, uncheck **Enable blobl public access** and **Enable Storage account key access**. Then go ahead and check **Default to Azure Active Directory Authorization in the Azure portal** ![sa6](/images/module03/Module03_6.png) ___
1. Keep scrolling and check **Enable hierarchical namespace**. Leave all other settings in this tab as is and click on **Next : Networking** ![sa7](/images/module03/Module03_7.png) ___
1. In the **Networking tab** select the **Disable publicaccess and use private access** option and click on **+ Add private endpoint** ![sa8](/images/module03/Module03_8.png) ___
1. This will open a sub-blade on the right. Ensure the correct subscription and resource group are selected. Then **choose a name** for the private endpoint. We recommend **pvladlsgen2-pe**. Next select the **dlz-vnet** virtual network that we created in module01 and make sure the **dlz-sn-1** subnet is selected. Next, ensure the **Integrate with private DNS zone** setting is set to **yes** and hit **Ok**. ![sa9](/images/module03/Module03_9.png) ___
1. Finally ensure that **Microsoft network routing** is selected for the routing preference. We have no further change to apply so go ahead ans hit **Review + create** ![sa10](/images/module03/Module03_10.png)
1. Navigate to your storage account **overview page**

## Granting data owner access

You may be tempted to believe that because you are an administrator of the subscription that this level of permission would cascade down to your the data in your containers. That is not the case. You must grant you sufficient privileges to read the data in the data lake.

1. In the Storage account overview page, click on **Access control (IAM)** ![sa11](/images/module03/Module03_11.png) ___
1. Then click on **Add role assignment** ![sa12](/images/module03/Module03_12.png) ___
1. In the list of roles, you must choose one of the Storage blob data roles that will allow access to the data plane of the data lake. Since we are granting this role to us, let's click **Storage blob data Ownwer** note that this is a lot of privileges and does not represent a production scenario ![sa13](/images/module03/Module03_13.png) ___
1. Next you must click **Select members**, then in the search box, **type the security principal you want to assign the role to** (note that in a production scenario this would likely be a group security principal and **not** a user principal). Next ensure the principal is listed in the selected section below the search box and click **Select**. Finally hit **Review and assign** to finish the role assignment.![sa14](/images/module03/Module03_14.png) ___

>We now have full access to both the management plane of the data lake as well as the data plane. Note that we will repeat this step whenever we want to grant access to the data. As you may guess, we will have to grant the **Storage Blob data Reader** to the Microsoft Purview account later in this lab.
___

## Creating our Data Lake Structure

The last step that we can create in the management plane is creating the data lake root container, everything else needs to be done in the VM that is connected to the VNET. It's nevertheless a good idea to get the VM booted up and continue the next steps there.

1. Please ensure your **SAW VM is started**
1. Go ahead and **connect to your VM** and **log in to the Azure Portal**.
1. We first have to create a root for our data lake. In an Azure Storage Account, the container is the root. **Navigate to your storage account overview page** and click on **Containers**, then 
1. Then we 

## Accessing the data plane of your private data lake

As you remember from previous module, we have to use a machine that is inside the VNET where the private endpoint is located in order to access the data inside the blob. To achieve that we will use the SAW virtual machine we created in Module02. Note that this does not apply to the management plane of the Storage account. In other words, you don't have to log in to your SAW to do things like adding users to a role or changing a setting in the IAM section of the Storage account blade in the portal.



