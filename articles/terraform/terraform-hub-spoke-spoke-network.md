---
title: Zelfstudie - Een gesproken netwerk maken in Azure met Terraform
description: Meer informatie over het implementeren van twee spaakvNets die zijn aangesloten op een hub in een hub-spoke topologie
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 2a36b8ac22fb52f6b8f1246fd254d9c3ff22fc82
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74159008"
---
# <a name="tutorial-create-a-spoke-network-in-azure-using-terraform"></a>Zelfstudie: Een gesproken netwerk maken in Azure met Terraform

In deze zelfstudie implementeert u twee afzonderlijke gesproken netwerken om de scheiding van workloads aan te tonen. De netwerken delen gemeenschappelijke bronnen met behulp van hub virtueel netwerk. Knooppunten kunnen worden gebruikt om werkbelastingen te isoleren in hun eigen VNets, afzonderlijk beheerd vanaf andere knooppunten. Elke workload kan meerdere lagen bevatten, met meerdere subnetten die zijn verbonden via Azure-load balancers.

Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> * Gebruik HCL (HashiCorp Language) om de Spoke VNets te implementeren in hub-spoke topologie
> * Terraform gebruiken om virtuele machines te maken in de spaaknetwerken
> * Terraform gebruiken om virtuele netwerkpeeringen met de hubnetwerken op te zetten

## <a name="prerequisites"></a>Vereisten

1. [Maak een hub- en spoke hybride netwerktopologie met Terraform in Azure.](./terraform-hub-spoke-introduction.md)
1. [On-premises virtueel netwerk maken met Terraform in Azure.](./terraform-hub-spoke-on-prem.md)
1. [Maak een virtueel hubnetwerk met Terraform in Azure.](./terraform-hub-spoke-hub-network.md)
1. [Maak een virtueel netwerktoestel met Terraform in Azure.](./terraform-hub-spoke-hub-nva.md)

## <a name="create-the-directory-structure"></a>De mapstructuur maken

In deze sectie worden twee spaakscripts gemaakt. Elk script definieert een gesproken virtueel netwerk en een virtuele machine voor de werkbelasting. Er wordt vervolgens een peered virtueel netwerk gemaakt van hub naar spoke.

1. Blader naar [Azure Portal](https://portal.azure.com).

1. Azure [Cloud Shell openen](/azure/cloud-shell/overview). Als u nog geen omgeving hebt geselecteerd, selecteert u **Bash** als uw omgeving.

    ![Cloud Shell-prompt](./media/terraform-common/azure-portal-cloud-shell-button-min.png)

1. Ga naar de map `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Maak de nieuwe directory de actieve directory:

    ```bash
    cd hub-spoke
    ```

## <a name="declare-the-two-spoke-networks"></a>Verklaar de twee spaaknetwerken

1. Open in Cloud Shell een `spoke1.tf`nieuw bestand met de naam .

    ```bash
    code spoke1.tf
    ```

1. Plak de volgende code in de editor:

    ```hcl
    locals {
      spoke1-location       = "CentralUS"
      spoke1-resource-group = "spoke1-vnet-rg"
      prefix-spoke1         = "spoke1"
    }

    resource "azurerm_resource_group" "spoke1-vnet-rg" {
      name     = local.spoke1-resource-group
      location = local.spoke1-location
    }

    resource "azurerm_virtual_network" "spoke1-vnet" {
      name                = "spoke1-vnet"
      location            = azurerm_resource_group.spoke1-vnet-rg.location
      resource_group_name = azurerm_resource_group.spoke1-vnet-rg.name
      address_space       = ["10.1.0.0/16"]

      tags {
        environment = local.prefix-spoke1
      }
    }

    resource "azurerm_subnet" "spoke1-mgmt" {
      name                 = "mgmt"
      resource_group_name  = azurerm_resource_group.spoke1-vnet-rg.name
      virtual_network_name = azurerm_virtual_network.spoke1-vnet.name
      address_prefix       = "10.1.0.64/27"
    }

    resource "azurerm_subnet" "spoke1-workload" {
      name                 = "workload"
      resource_group_name  = azurerm_resource_group.spoke1-vnet-rg.name
      virtual_network_name = azurerm_virtual_network.spoke1-vnet.name
      address_prefix       = "10.1.1.0/24"
    }

    resource "azurerm_virtual_network_peering" "spoke1-hub-peer" {
      name                      = "spoke1-hub-peer"
      resource_group_name       = azurerm_resource_group.spoke1-vnet-rg.name
      virtual_network_name      = azurerm_virtual_network.spoke1-vnet.name
      remote_virtual_network_id = azurerm_virtual_network.hub-vnet.id

      allow_virtual_network_access = true
      allow_forwarded_traffic = true
      allow_gateway_transit   = false
      use_remote_gateways     = true
      depends_on = ["azurerm_virtual_network.spoke1-vnet", "azurerm_virtual_network.hub-vnet" , "azurerm_virtual_network_gateway.hub-vnet-gateway"]
    }

    resource "azurerm_network_interface" "spoke1-nic" {
      name                 = "${local.prefix-spoke1}-nic"
      location             = azurerm_resource_group.spoke1-vnet-rg.location
      resource_group_name  = azurerm_resource_group.spoke1-vnet-rg.name
      enable_ip_forwarding = true

      ip_configuration {
        name                          = local.prefix-spoke1
        subnet_id                     = azurerm_subnet.spoke1-mgmt.id
        private_ip_address_allocation = "Dynamic"
      }
    }

    resource "azurerm_virtual_machine" "spoke1-vm" {
      name                  = "${local.prefix-spoke1}-vm"
      location              = azurerm_resource_group.spoke1-vnet-rg.location
      resource_group_name   = azurerm_resource_group.spoke1-vnet-rg.name
      network_interface_ids = [azurerm_network_interface.spoke1-nic.id]
      vm_size               = var.vmsize

      storage_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04-LTS"
        version   = "latest"
      }

      storage_os_disk {
        name              = "myosdisk1"
        caching           = "ReadWrite"
        create_option     = "FromImage"
        managed_disk_type = "Standard_LRS"
      }

      os_profile {
        computer_name  = "${local.prefix-spoke1}-vm"
        admin_username = var.username
        admin_password = var.password
      }

      os_profile_linux_config {
        disable_password_authentication = false
      }

      tags {
        environment = local.prefix-spoke1
      }
    }

    resource "azurerm_virtual_network_peering" "hub-spoke1-peer" {
      name                      = "hub-spoke1-peer"
      resource_group_name       = azurerm_resource_group.hub-vnet-rg.name
      virtual_network_name      = azurerm_virtual_network.hub-vnet.name
      remote_virtual_network_id = azurerm_virtual_network.spoke1-vnet.id
      allow_virtual_network_access = true
      allow_forwarded_traffic   = true
      allow_gateway_transit     = true
      use_remote_gateways       = false
      depends_on = ["azurerm_virtual_network.spoke1-vnet", "azurerm_virtual_network.hub-vnet", "azurerm_virtual_network_gateway.hub-vnet-gateway"]
    }
    ```

1. Sla het bestand op en sluit de editor af.

1. Maak een nieuw bestand met de naam `spoke2.tf`.

      ```bash
      code spoke2.tf
      ```
    
1. Plak de volgende code in de editor:
    
    ```hcl
    locals {
      spoke2-location       = "CentralUS"
      spoke2-resource-group = "spoke2-vnet-rg"
      prefix-spoke2         = "spoke2"
    }

    resource "azurerm_resource_group" "spoke2-vnet-rg" {
      name     = local.spoke2-resource-group
      location = local.spoke2-location
    }

    resource "azurerm_virtual_network" "spoke2-vnet" {
      name                = "${local.prefix-spoke2}-vnet"
      location            = azurerm_resource_group.spoke2-vnet-rg.location
      resource_group_name = azurerm_resource_group.spoke2-vnet-rg.name
      address_space       = ["10.2.0.0/16"]

      tags {
        environment = local.prefix-spoke2
      }
    }

    resource "azurerm_subnet" "spoke2-mgmt" {
      name                 = "mgmt"
      resource_group_name  = azurerm_resource_group.spoke2-vnet-rg.name
      virtual_network_name = azurerm_virtual_network.spoke2-vnet.name
      address_prefix       = "10.2.0.64/27"
    }

    resource "azurerm_subnet" "spoke2-workload" {
      name                 = "workload"
      resource_group_name  = azurerm_resource_group.spoke2-vnet-rg.name
      virtual_network_name = azurerm_virtual_network.spoke2-vnet.name
      address_prefix       = "10.2.1.0/24"
    }

    resource "azurerm_virtual_network_peering" "spoke2-hub-peer" {
      name                      = "${local.prefix-spoke2}-hub-peer"
      resource_group_name       = azurerm_resource_group.spoke2-vnet-rg.name
      virtual_network_name      = azurerm_virtual_network.spoke2-vnet.name
      remote_virtual_network_id = azurerm_virtual_network.hub-vnet.id

      allow_virtual_network_access = true
      allow_forwarded_traffic = true
      allow_gateway_transit   = false
      use_remote_gateways     = true
      depends_on = ["azurerm_virtual_network.spoke2-vnet", "azurerm_virtual_network.hub-vnet", "azurerm_virtual_network_gateway.hub-vnet-gateway"]
    }

    resource "azurerm_network_interface" "spoke2-nic" {
      name                 = "${local.prefix-spoke2}-nic"
      location             = azurerm_resource_group.spoke2-vnet-rg.location
      resource_group_name  = azurerm_resource_group.spoke2-vnet-rg.name
      enable_ip_forwarding = true

      ip_configuration {
        name                          = local.prefix-spoke2
        subnet_id                     = azurerm_subnet.spoke2-mgmt.id
        private_ip_address_allocation = "Dynamic"
      }

      tags {
        environment = local.prefix-spoke2
      }
    }

    resource "azurerm_virtual_machine" "spoke2-vm" {
      name                  = "${local.prefix-spoke2}-vm"
      location              = azurerm_resource_group.spoke2-vnet-rg.location
      resource_group_name   = azurerm_resource_group.spoke2-vnet-rg.name
      network_interface_ids = [azurerm_network_interface.spoke2-nic.id]
      vm_size               = var.vmsize

      storage_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04-LTS"
        version   = "latest"
      }

      storage_os_disk {
        name              = "myosdisk1"
        caching           = "ReadWrite"
        create_option     = "FromImage"
        managed_disk_type = "Standard_LRS"
      }

      os_profile {
        computer_name  = "${local.prefix-spoke2}-vm"
        admin_username = var.username
        admin_password = var.password
      }

      os_profile_linux_config {
        disable_password_authentication = false
      }

      tags {
        environment = local.prefix-spoke2
      }
    }

    resource "azurerm_virtual_network_peering" "hub-spoke2-peer" {
      name                      = "hub-spoke2-peer"
      resource_group_name       = azurerm_resource_group.hub-vnet-rg.name
      virtual_network_name      = azurerm_virtual_network.hub-vnet.name
      remote_virtual_network_id = azurerm_virtual_network.spoke2-vnet.id
      allow_virtual_network_access = true
      allow_forwarded_traffic   = true
      allow_gateway_transit     = true
      use_remote_gateways       = false
      depends_on = ["azurerm_virtual_network.spoke2-vnet", "azurerm_virtual_network.hub-vnet", "azurerm_virtual_network_gateway.hub-vnet-gateway"]
    }
    ```
     
1. Sla het bestand op en sluit de editor af.
  
## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"] 
> [Een hub en spoke-netwerk valideren met Terraform in Azure](./terraform-hub-spoke-validation.md)