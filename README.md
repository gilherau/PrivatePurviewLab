# PrivatePurviewLab

This lab will guide you through activities to create a private network environment for Azure Purview. This lab does not seek to replace other Azure Purview labs and will not go into a lot of details for purview specific functionalities but instead will focus on the typical azure environments that Azure Purview will be deployed to in the field.

>:muscle: We fully realize that much of what you will do here could have been automated using scripting and the ARM engine. But where's the fun in that? By manually doing all the steps in this lab, you will have a much more memorable experience and will understand the plumbing that powers Azure.

>:moneybag: **IMPORTANT** please be mindful of the azure consumption of this lab. If left unattended for long periods of time the cost could be substantial. We strongly encourage you to follow the tips and guidance marked in this lab with the :moneybag: icon for tips about saving costs.

## :student: Learning objectives

- Refresh your knowledge of the Cloud Adoption Framework and the Data management scenario
- How to create a private network and securely log on to it
- Simulate a private network as a customer would see it.
- Learn about proper security hygiene and hardening best practices for modern data platforms
- Simulate a Cloud Adoption Framework mock data management and landing zones and data landing zones

### Knowledge level

This lab assumes the reader has basic familiarity with the following topics:

- Windows 10 operating system
- Basic TCP/IP network concepts
- the OSI model

## :shopping_cart: Requirements

- An [Azure account](https://azure.microsoft.com/free/) with an active subscription. Note: If you don't have access to an Azure subscription, you may be able to start with a [free account](https://www.azure.com/free).
- You must have the necessary privileges within your Azure subscription to create resources, perform role assignments, register resource providers (if required), etc.
- A local download of the Bing Covid-19 sample dataset in parquet format. Follow [this link](https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.parquet) to download the file
- (Optional) A local [installation](https://docs.microsoft.com/cli/azure/install-azure-cli) of Azure CLI
- Patience, curiosity and a readiness to tinker.

## :student: Learning modules

1. [Environment description and architecture](/modules/module00.md)
1. [Setting up the simulated network and secure access workstation](/modules/module01.md)
1. [Setting up the secure access workstation (SAW)](/modules/module02.md)
1. [Setting up "private" sample data sources](/modules/Module03.md)
1. Setting up "Private" Azure Purview account
1. Setting up "private" purview scans using the Managed VNET feature

[Begin lab](/modules/module00.md)