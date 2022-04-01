# Environment description and architecture

## Overview

The goal here is to simulate a bare bone environment that approximates the main components you would find in deployments that follow the [Cloud Adoption framework](https://docs.microsoft.com/azure/cloud-adoption-framework/) and require a private access. For the sake of learning and exposure to more restrictive guidelines. To that end, the following area of interest are included in the architecture:

- Simulating a hub and spoke network that includes a Hub virtual network, a Data management Landing zone virtual network and a Data Landing zone virtual network.
- The above three virtual network will be peered to simulate a typical deployment
- All deployed services will make use of private endpoints
- The Purview service will be using the Managed VNET feature to scan the data source using private network traffic
- Simulating a fully private "secure access workstation" involving spinning up a virtual machine that is fully private (i.e. not accessible from the internet)
- Leveraging the Azure Bastion Service to handle the public facing handshake.
- [Optional] Enable a feature of Azure Cloud Defender called Just in time access to limit the attack surface of the Virtual machine.

This will simulate the most stringent of standards that you may encounter in the field and how Azure can address those requirements.

## Architecture diagram

Diagram goes here
